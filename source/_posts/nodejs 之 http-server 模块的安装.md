---
title: nodejs 之 http-server 模块的安装
author: leazhi
tags:
  - [nodejs]
  - [npm]
  - [http-server]
categories:
  - [nodejs]
date: 2023-09-10 17:34:27
cover: 
discription: 
keywords: 
password: 
message: 
---
## 系统环境：
- OS：Ubuntu 22.04.3 LTS
- Kernel：5.15.0-84-generic #93-Ubuntu SMP Tue Sep 5 17:16:10 UTC 2023 x86_64 x86_64 x86_64 GNU/Linux
- NodeJS：v18.17.1

## nodejs 安装

nodejs 官方网址：https://nodejs.org/en

1.下载官方提供的 LTS（长期维护）版本到本地解压：

2.将解压出来的 nodejs 目录移动到指定位置并重命名：
```bash
leazhi@debian:~/下载$ sudo mv node-v18.17.1-linux-x64 /usr/local/nodejs
```

3.将 nodejs/bin 目录下的可执行文件软链接到 /usr/bin/ 目录下：
```bash
leazhi@debian:~/下载$ sudo ln -s /usr/local/nodejs/bin/{node,npm,npx} /usr/bin/
```

4.测试 npm 命令能够正常输入版本号：
```bash
leazhi@debian:~$ npm --version
9.6.7
```

<hr class="custom-hr"> </hr>

## nodejs 配置

### 环境配置

#### 配置缓存路径（模块安装路径）

默认情况，使用 npm install 安装的模块，会安装在 %appdata%\npm\node_modules 下。由于C:盘空间宝贵，因此建设改到 nodejs 安装目录下

1.先在 nodejs 安装目录下新建 2 个目录，分别为 global 和 cache;

2.执行命令：
```bash
# 设置 npm 全局环境路径（安装新模块时都会安装到该目录下）
$ npm config set prefix "D:\Program Files\nodejs\global"

# 设置 npm 缓存路径
$ npm config set cache "D:\Program Files\nodejs\cache"
```

#### 配置远程仓库

默认是国外的远程仓库，网速慢安装组件慢，因此建议改成国内的镜像仓库：
```bash
$ npm config set registry https://registry.npm.taobao.org --global
```

如果配置过程中出现如下报错：
```bash
leazh@DESKTOP-VC622UG MINGW64 /d/Program Files/nodejs
$ npm config set registry https://registry.npm.taobao.org --global
npm ERR! code EPERM
npm ERR! syscall mkdir
npm ERR! path D:\Program Files\nodejs\global\etc
npm ERR! errno -4048
npm ERR! Error: EPERM: operation not permitted, mkdir 'D:\Program Files\nodejs\g
lobal\etc'
npm ERR!  [Error: EPERM: operation not permitted, mkdir 'D:\Program Files\nodejs
\global\etc'] {
npm ERR!   errno: -4048,
npm ERR!   code: 'EPERM',
npm ERR!   syscall: 'mkdir',
npm ERR!   path: 'D:\\Program Files\\nodejs\\global\\etc'
npm ERR! }
npm ERR!
npm ERR! The operation was rejected by your operating system.
npm ERR! It's possible that the file was already in use (by a text editor or ant
ivirus),
npm ERR! or that you lack permissions to access it.
npm ERR!
npm ERR! If you believe this might be a permissions issue, please double-check t
he
npm ERR! permissions of the file and its containing directories, or try running
npm ERR! the command again as root/Administrator.

npm ERR! Log files were not written due to an error writing to the directory: D:
\Program Files\nodejs\cache\_logs
npm ERR! You can rerun the command with `--loglevel=verbose` to see the logs in
your terminal
```

**解决方法**：

赋予 nodejs 安装目录当前用户的读写权限即可！


## 模块安装

1.执行命令：
```bash
leazh@DESKTOP-VC622UG MINGW64 /d/Program Files/nodejs
$ npm install -g http-server

added 40 packages in 4s
npm notice
npm notice New major version of npm available! 9.6.7 -> 10.1.0
npm notice Changelog: https://github.com/npm/cli/releases/tag/v10.1.0
npm notice Run npm install -g npm@10.1.0 to update!
npm notice
```

2.安装完成，执行 http-server 命令时报找不到命令。解决方法，配置 系统环境变量，将 http-server 模块的安装路径添加到系统环境变量下即可！