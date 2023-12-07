---
title: gitlab-ee 及 runner 部署安装配置报错信息及解决方法
author: leazhi
tags:
  - [ubuntu] 
  - [gitlab]
categories:
  - [ubuntu]
  - [git]
date: 2023-07-18 21:35:44
cover: https://hexo.linuser.com/gallery/code/985805.png
discription: 
keywords: 
password: 
message: 
---

# GitLab-Runner 相关

{% note danger simple %}
## 错误一：无法启动 gitlab-runner：FATAL: Failed to start gitlab-runner: exit status 5 
{% endnote %}
在之前的 gitlab-runner 主机上重新注册 gitlab-runner,注册完成后，启动 gitlab-runner,报错：
```bash
ubuntu@vm02:~$ sudo gitlab-runner start
Runtime platform                                    arch=amd64 os=linux pid=2472 revision=456e3482 version=15.10.0
FATAL: Failed to start gitlab-runner: exit status 5 
```

解决方法：

重新指定安装参数进行安装，然后再启动 gitlab-runner:
```bash
重新安装 gitlab-runner
ubuntu@vm02:~$ sudo gitlab-runner install --user=gitlab-runner --working-directory=/home/gitlab-runner
Runtime platform                                    arch=amd64 os=linux pid=2479 revision=456e3482 version=15.10.0

再次启动 gitlab-runner:
ubuntu@vm02:~$ sudo gitlab-runner start
Runtime platform                                    arch=amd64 os=linux pid=2543 revision=456e3482 version=15.10.0
```

{% note danger simple %}
## 错误二：CI/CD 流水线失败：ERROR: Job failed: prepare environment: exit status 1.
{% endnote %}
配置好 Runner 后，测试发现 runner 运行失败，任务终端报：
```bash
Running with gitlab-runner 15.10.0 (456e3482)
  on vm02 SuWk6_nh, system ID: s_b2206b376822
Preparing the "shell" executor
00:00
Using Shell (bash) executor...
Preparing environment
00:00
Running on vm02...
ERROR: Job failed: prepare environment: exit status 1. Check https://docs.gitlab.com/runner/shells/index.html#shell-profile-loading for more information
```

解决方法：

编辑 gitlab-runner 用户家目录下的 .bash_logout，将该文件中的内容全部注释：
```bash
gitlab-runner@vm02:~/Game-sport$ cat /home/gitlab-runner/.bash_logout 
# ~/.bash_logout: executed by bash(1) when login shell exits.

# when leaving the console clear the screen to increase privacy

#if [ "$SHLVL" = 1 ]; then
#    [ -x /usr/bin/clear_console ] && /usr/bin/clear_console -q
#fi
```

然后再次执行 流水线任务即可！


{% note danger simple %}
## 错误三：CI/CD 流水线失败：HTTP Basic: Access denied. The provided password or token is incorrect or your account has 2FA enabled and you must use a personal access token instead of a password.
{% endnote %}
配置好 Runner 后，测试发现一直失败，查看详情，发现报：
```bash
Running with gitlab-runner 15.10.0 (456e3482)
  on ubuntu2204-001 oPSERo_d, system ID: s_4153648d0c16
Preparing the "shell" executor
00:00
Using Shell (bash) executor...
Preparing environment
00:00
Running on ubuntu2204-001...
Getting source from Git repository
00:01
Fetching changes with git depth set to 20...
Initialized empty Git repository in /home/gitlab-runner/builds/oPSERo_d/0/leazhi/Game-pay/.git/
Created fresh repository.
remote: HTTP Basic: Access denied. The provided password or token is incorrect or your account has 2FA enabled and you must use a personal access token instead of a password. See http://gitlab.linuser.com/help/topics/git/troubleshooting_git#error-on-git-fetch-http-basic-access-denied
fatal: Authentication failed for 'http://gitlab.linuser.com/leazhi/Game-pay.git/'
Cleaning up project directory and file based variables
00:00
ERROR: Job failed: exit status 1
```
报错原因：因为我使用了代理，gitlab 服务安装在局域网内部，使用 FRP 做的内网穿透，而 gitlab-runner 安装在公网服务器，所以报上面的错误！

解决方法：

编辑 /etc/gitlab-runner/config.toml 文件，在 [[runner]] 配置下添加 `clone_url = ""` ,如下：
```bash
concurrent = 1
check_interval = 0
shutdown_timeout = 0

[session_server]
  session_timeout = 1800

[[runners]]
  name = "vm02"
  url = "https://gitlab.linuser.com"
  clone_url = "https://gitlab.linuser.com"
  id = 5
  token = "SuWk6_nhmjcJsUsTnczM"
  token_obtained_at = 2023-03-18T09:39:20Z
  token_expires_at = 0001-01-01T00:00:00Z
  executor = "shell"
  [runners.cache]
    MaxUploadedArchiveSize = 0
```


{% note danger simple %}
## 错误四：Runner 注册失败：ERROR: Registering runner... failed ... : 401 Unauthorized
{% endnote %}
在一台之前注册过 runner 的服务器上重新注册 Runner，报：

```bash
ubuntu@vm02:~$ sudo gitlab-runner register
Runtime platform                                    arch=amd64 os=linux pid=1451 revision=456e3482 version=15.10.0
Running in system-mode.                            
                                                   
Enter the GitLab instance URL (for example, https://gitlab.com/):
http://gitlab.linuser.com/
Enter the registration token:
GR1348941sxMSCXbpWXJQUuQzBgnQ
Enter a description for the runner:
[vm02]: multipass, vm02
Enter tags for the runner (comma-separated):
multipass, vm02
Enter optional maintenance note for the runner:

WARNING: Support for registration tokens and runner parameters in the 'register' command has been deprecated in GitLab Runner 15.6 and will be replaced with support for authentication tokens. For more information, see https://gitlab.com/gitlab-org/gitlab/-/issues/380872 
ERROR: Registering runner... failed                 runner=GR1348941sxMSCXbp status=GET https://gitlab.linuser.com/api/v4/runners: 401 Unauthorized
PANIC: Failed to register the runner.              
```

### 报错原因一：该服务器以前注册过 runner，执行 gitlab-runner uninstall 后，没有清理配置文件: `/etc/gitlab-runner/config.toml`

解决方法：

删除之前注册过的 runner 配置文件，然后重新注册即可！
```bash
# 删除之前注册过的配置文件：
ubuntu@vm02:~$ sudo rm -rf /etc/gitlab-runner/config.toml
```

### 报错原因二：注册的时候使用的是 http 协议而非 https 协议

解决方法：

将 http 替换成 https 后再次注册即可！

{% note danger simple %}
## 错误五：此作业已阻塞，因为该项目没有分配任何可用 Runner
{% endnote %}
这种错误最容易出现在初次配置 CI 的时候，原因有三：

1. 没有正确安装注册 Runner;
2. Runner 机器上没有将该项目的代码拉取到本地；
3. Runner 配置错误，配置了已暂停(停止Runner 接收新的作业)

{% note danger simple %}
## 错误六：
{% endnote %}
git push 提交的时候，报：error: RPC 失败。curl 56 GnuTLS recv error (-54): Error in the pull function.
```bash
{23-03-19 19:39}ubuntu-leazhi:~/桌面/gitlab/python3@main leazhi% git push
枚举对象中: 8, 完成.
对象计数中: 100% (8/8), 完成.
使用 16 个线程进行压缩
压缩对象中: 100% (7/7), 完成.
error: 无法倒回 rpc post 数据 - 尝试增加 http.postBuffer
error: RPC 失败。curl 56 GnuTLS recv error (-54): Error in the pull function.
send-pack: unexpected disconnect while reading sideband packet
写入对象中: 100% (7/7), 112.52 MiB | 3.36 MiB/s, 完成.
总共 7（差异 1），复用 0（差异 0），包复用 0
fatal: 远端意外挂断了
Everything up-to-date
{23-03-19 19:40}ubuntu-l
```

解决方法：

1.打开命令终端，修改 git 全局配置：
```bash
git config http.postBuffer 524288000
git config ssh.postBuffer 524288000
git config http.version HTTP/1.1
```

2.编辑前端 nginx 代理，配置成：
```bash
server {
    listen 80;
    listen 443 ssl;

    server_name gitlab.linuser.com;

    ssl_certificate  /usr/local/ssl/gitlab.linuser.com/9326533_gitlab.linuser.com.pem;
    ssl_certificate_key  /usr/local/ssl/gitlab.linuser.com/9326533_gitlab.linuser.com.key;
    ssl_session_cache    shared:SSL:1m;
    ssl_session_timeout  5m;
    ssl_ciphers  HIGH:!aNULL:!MD5;
    ssl_prefer_server_ciphers  on;

    charset utf-8;

    # 将值改大一点
    client_max_body_size 200m;

    if ( $ssl_protocol = "" ) {
        rewrite ^ https://$host$request_uri?;
    }

    location / {
        proxy_pass http://127.0.0.1:8000;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header Host $host;
        proxy_set_header X-Forward-Proto https;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection "upgrade";
    }

    # 加上配置：
    location ~/git(/.*) {
    	client_max_body_size 200m;
    }

}
```

重启 nginx ，再次推送即可！