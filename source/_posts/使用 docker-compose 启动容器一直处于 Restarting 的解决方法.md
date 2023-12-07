---
title: 使用 docker-compose 启动容器一直处于 Restarting 的解决方法
author: leazhi
tags:
  - [docker]
  - [docker-compose]
categories:
  - [docker]
date: 2023-09-29 08:29:22
cover: 
discription: 
keywords: docker-compose, docker
password: 
message: 
---

这是给一个客户的解决方法，客户使用 docker-compose 启动容器一直处于 Restarting 状态，无法正常使用。

<!--more-->

## 问题描述

客户使用如下 docker-compose 脚本启动容器一直处于 Restarting 状态，无法正常使用。
```yaml
version: '3'
services:
  ubuntu_ntp:
    image: ubuntu:ntp
    restart: always
    networks:
      docker_macvlan:
        ipv4_address: 192.168.1.203
    dns:
      - 192.168.1.202
    command: 
      - /usr/local/sbin/run.sh

networks:
  docker_macvlan:
    external: true
```

## 问题分析

查看日志，发现容器启动时，报错如下：

```
root@TECSVRAS01:~/docker-compose/ntp# docker-compose logs -f ubuntu_ntp 
Attaching to ntp_ubuntu_ntp_1 
ntp_ubuntu_ntp_1 exited with code 0
```
## 问题解决

打开 google，进行搜索，找到这样一段解释：
- command必须是阻塞式执行的;
- command执行结束或者退出时，容器就会退出或者重启;

于是，想到脚本的 command 所执行的脚本内容为：
```bash
#!/bin/bash

sysemctl start chrond

/bin/bash
```

结合查到的内容，修改上面的 yaml 文件，将 command 修改为如下内容：
```yaml
version: '3'
services:
  ubuntu_ntp:
    image: ubuntu:ntp
    restart: always
    networks:
      docker_macvlan:
        ipv4_address: 192.168.1.203
    dns:
      - 192.168.1.202
    tty: true
    command: 
      - /usr/local/sbin/run.sh
networks:
  docker_macvlan:
    external: true
```

删除一直在 Restarting 的旧容器，然后重新执行 `docker-compose up -d` ，可以看到容器正常启动。