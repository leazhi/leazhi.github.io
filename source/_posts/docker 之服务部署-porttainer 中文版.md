---
title: docker 之服务部署-porttainer 中文版
author: leazhi
tags:
  - [docker]
categories:
  - [docker]
date: 2023-10-14 08:19:34
cover: 
discription: 
keywords: docker 可视化，web 管理
password: 
message: 
---

某个客户需求，自己并不懂技术，只是觉得 dokcer 好玩，想自己测试下。于是，找到我，要求部署 portainer 中文版。

## porttainer 中文部署


### 方法一：通过原版英文镜像进行部署，然后在下载中文包进行汉化

需要汉化的话，部署 portainer 的命令为：
```bash
sudo docker run -d --restart=always --name portainer -p 9000:9000 -v /var/run/docker.sock:/var/run/docker.sock -v /data/docker/portainer/data:/data -v /data/docker/portainer/public:/public  portainer/portainer-ce:latest
```

### 方法二：直接使用汉化版的 Image

1.汉化版镜像获取： 
```bash
sudo docker pull 6053537/portainer-ce
```

2.部署之前，先确保系统的 9000 端口没有被占用：
```bash
wusk@wusk:~$ sudo docker ps -a
CONTAINER ID   IMAGE                           COMMAND        CREATED        STATUS                    PORTS                                                           NAMES
0a43c0097402   portainer/portainer-ce:latest   "/portainer"   40 hours ago   Up 17 hours               8000/tcp, 9443/tcp, 0.0.0.0:9000->9000/tcp, :::9000->9000/tcp   portainer
676664ef2376   hello-world                     "/hello"       42 hours ago   Exited (0) 42 hours ago                                                                   serene_lamport
wusk@wusk:~$ sudo docker rm -f portainer
portainer
```

3.部署汉化版命令：
```bash
sudo docker run -d --name portainer-zh --restart=always -p 9000:9000 -v /var/run/docker.sock:/var/run/docker.sock -v /dockerData/portainer:/data 6053537/portainer-ce
```

4.部署完成后，就可以通过 9000 端口访问了。首次访问需要创建管理员账号密码！




