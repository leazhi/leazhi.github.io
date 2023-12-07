---
title: vscode 中 docker 插件无法正常连接本地 docker 的解决方法
author: leazhi
tags:
  - [docker]
  - [vscode]
categories:
  - [docker]
  - [vscode]
date: 2023-12-04 20:22:03
cover: 
discription: 
keywords: 
password: 
message: 
---

## 系统环境

- 系统：ubuntu 23.10
- 内核：Linux ubuntu2310 6.5.0-13-generic #13-Ubuntu SMP PREEMPT_DYNAMIC Fri Nov  3 12:16:05 UTC 2023 x86_64 x86_64 x86_64 GNU/Linux
- docker：24.0.7, build afdd53b
- vscode：1.84.2

## 错误一：perission denied errir when connecting to docker

在 ubuntu 上使用 sudo 安装完 docker , vscode 中安装 docker 插件后，点击 vscode 左侧的 docker 插件，发现报：

```bash
Error：permission denied while trying to connect to docker ...
```

### 报错原因

默认情况下，Docker 命令只能由具有 root 或 sudo 权限的用户运行。如果您想以非 root 用户身份运行 Docker 命令，则需要将您的用户添加到该docker组中。为此，请执行以下操作：

### 解决方法

1.将当前用户添加到 docker 组中:
```bash
sudo usermod -aG docker ${USER}
```

2.执行下面的命令重载组员身份（或者重启下系统），确保能 vscode 能正常使用 docker 插件：
```bash
newgrp docker       # reboot
```