---
title: 利用 gitlab 的CI_CD 自动发布到 hexo
author: leazhi
tags:
  - gitlab
  - gitlab-runner
  - hexo
  - CI/CD
categories:
  - [ubuntu]
date: 2023-07-17 19:26:22
cover: https://hexo.linuser.com/gallery/code/985805.png
discription: 
keywords: 
---

## 在 hexo 服务器上安装 gitlab-runner

### 安装 gitlab-runner

1.从官方站点下载同 gitlab 同版本的 gitlab-runner 到 hexo 服务器的 /usr/bin/ 目录下：
```bash
sudo wget -O /usr/bin/gitlab-runner https://gitlab-runner-downloads.s3.amazonaws.com/latest/binaries/gitlab-runner-linux-amd64
```

2.赋予 gitlab-runner 可执行权限：
```bash
sudo chmod +x /usr/bin/gitlab-runner
```

3.创建运行 gitlab-runner 的用户(注意：需要创建它的家目录和允许登录系统)
```bash
sudo useradd --comment 'Gitlab Runner' --create-home gitlab-runner --shell /bin/bash
```

4.创建 gitlab-runner 运行脚本文件 `sudo vim /lib/systemd/system/gitlab-runner.service`：
```bash
[Unit]
Description=GitLab Runner
ConditionFileIsExecutable=/usr/bin/gitlab-runner

After=syslog.target network.target

[Service]
StartLimitInterval=5
StartLimitBurst=10
ExecStart=/usr/bin/gitlab-runner "run" "--working-directory" "/home/gitlab-runner" "--config" "/etc/gitlab-runner/config.toml" "--service" "gitlab-runner" "--user" "gitlab-runner"
Restart=always
RestartSec=120
EnvironmentFile=-/etc/sysconfig/gitlab-runner

[Install]
WantedBy=multi-user.target
```

5.安装 runner:
```bash
sudo gitlab-runner install --user=gitlab-runner --working-directory=/home/gitlab-runner   # working-direcotry: 项目克隆或打包后文件存放的目录
```


### 注册 runner:

1.登录 gitlab web 端，找到要创建 CI/CD 的项目，创建 runner.具体做法，请参考：

2.执行命令，注册：
```bash
root@ubuntuhome:~$ sudo gitlab-runner register  --url http://gitlab.linuser.com  --token glrt-xs_3R4MHyu_x_rcaiVV7
Runtime platform                                    arch=amd64 os=linux pid=2862945 revision=b72e108d version=16.1.0
Running in system-mode.

Enter the GitLab instance URL (for example, https://gitlab.com/):
[http://gitlab.linuser.com]:
ERROR: Verifying runner... failed                   runner=xs_3R4MHy status=GET https://gitlab.linuser.com/api/v4/runners/verify: 401
PANIC: Failed to verify the runner.                 # 报这个错是因为使用的 http 协议，将其修改成 https 协议注册即可！
```

3.启动 gitlab-runner 服务：
```bash
sudo systemctl start gitlab-runner.service
```


### 使用 Frpc 代理后的配置

由于我的 gitlab 是在内网环境，加上联通又没有给单独配置公网 IP,所以，只能依靠自己搭建内网穿透服务进行内网映射！

1.编辑 /etc/gitlab-runner/config.toml 文件。将其修改为：
```bash
concurrent = 1
check_interval = 0
shutdown_timeout = 0

[session_server]
  session_timeout = 1800

[[runners]]
  name = "hexo"
  url = "https://gitlab.linuser.com"
  clone_url = "https://gitlab.linuser.com"              # 主要是加这一行（使用代理的配置）
  id = 10
  token = "glrt-xs_3R4MHyu_x_rcaiVV7"
  token_obtained_at = 2023-07-17T10:24:13Z
  token_expires_at = 0001-01-01T00:00:00Z
  executor = "shell"
  [runners.cache]
    MaxUploadedArchiveSize = 0
```

2.删除 gitlab-runner 家目录下的 .bash_logout.sh 文件:
```bash
rm /home/gitlab-runner/.bash_logout
```
不删该文件会导致自动发布失败,报错信息为：
```bash
Running with gitlab-runner 16.1.0 (b72e108d)
  on hexo xs_3R4MHy, system ID: s_78501ab433a4
Preparing the "shell" executor
00:00
Using Shell (bash) executor...
Preparing environment
00:00
ERROR: Job failed: prepare environment: exit status 1. Check https://docs.gitlab.com/runner/shells/index.html#shell-profile-loading for more information
```

**声明**：
gitlab-runner 安装配置好后，它默认会将项目的代码自动克隆到定义的 work-direcotry 目录里面，无需我们再次手动去获取代码或者打包


## 创建发布脚本

### 创建脚本

1.在 gitlab-runner 家目录下创建脚本文件 dep.sh：
```bash
#!/usr/bin/env bash

# gitlab-runner 会自动将 gitlab 上的 Hexo 项目克隆到 gitlab-runner 安装时指定的 work-directory 目录下（由于我的项目只需要动到 source 目录，所以这里直接指定要变动的目录）
work_dir='/home/gitlab-runner/builds/xs_3R4MHy/0/brothers/hexo/source'

# hexo 站点静态文件存放目录（即文章和图片上传的目录）
blog_dir='/data/hexo/blog/source'

# 判断下 ${blog_dir} 是否存在，存在的话，直接删除里面所有内容
if [ -d ${blog_dir} ]; then
	rm -rf ${blog_dir}/*
fi

# 重新复制
cp -rf ${work_dir}/* ${blog_dir}/

# 进入 hexo 站点根目录：
cd /data/hexo/blog

# 重启 hexo
pm2 stop hexo_run.js && pm2 start hexo_run.js
```

2.别忘记了修改 hexo 网站根目录的所属主和组为 gitlab-runner:
```bash
sudo chown -R gitlab-runner:gitlab-runner /data/hexo
```

## 创建文件，推到 gitlab 进行发布

### 在本地创建文件

1.在本地 hexo 项目的 source 目录下新建文章，然后推送至 gitalb

### 验证 CI/CD

1.登录 gitlab ，找到 hexo 项目下的 CI/CD 选项并点击进入。然后点击作业，查看流水线状态是否都 OK

2.访问网站，确保新创建的文章有被展示即可！