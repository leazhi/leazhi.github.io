---
title: docker 之服务部署-redis
author: leazhi
tags:
  - [redis]
  - [docker]
categories:
  - [redis]
  - [docker]
date: 2023-09-21 09:40:48
cover: 
discription: 
keywords: redis, docker
password: 
message: 
---

## 系统环境
- OS：kali linux 2023.03
- Kernel：6.4.0-kali3-amd64 #1 SMP PREEMPT_DYNAMIC Debian 6.4.11-1kali1 (2023-08-21) x86_64 GNU/Linux
- Docker：Docker version 20.10.25+dfsg1, build b82b9f3

## 服务部署

{% note danger simple %}
**官方镜像虽然提供了挂载目录，但是不能指定配置文件挂载，且数据不能持久化，一旦服务器重启，数据都会丢失。生产慎用！！！**
{% endnote %}

### 部署前的准备

1.查找 Redis 的镜像：
```bash
──(leazhi㉿kali-desktop)-[~]
└─$ sudo docker search redis                 
NAME                                DESCRIPTION                                     STARS     OFFICIAL   AUTOMATED
redis                               Redis is an open source key-value store that…   12357     [OK]       
bitnami/redis                       Bitnami Redis Docker Image                      265                  [OK]
redislabs/redisinsight              RedisInsight - The GUI for Redis                91                   
redis/redis-stack                   redis-stack installs a Redis server with add…   70                   
redislabs/redisearch                Redis With the RedisSearch module pre-loaded…   58                   
redis/redis-stack-server            redis-stack-server installs a Redis server w…   54                   
redislabs/rejson                    RedisJSON - Enhanced JSON data type processi…   53                   
redislabs/redismod                  An automated build of redismod - latest Redi…   41                   [OK]
redislabs/redis                     Clustered in-memory database engine compatib…   40                   
redislabs/redisgraph                A graph database module for Redis               26                   [OK]
redislabs/rebloom                   A probablistic datatypes module for Redis       24                   [OK]
redislabs/redistimeseries           A time series database module for Redis         12                   
redislabs/operator                                                                  7                    
redislabs/redisai                                                                   6                    
redislabs/redis-py                                                                  5                    
redislabs/redisgears                An automated build of RedisGears                4                    
redislabs/redis-webcli              A tiny Flask app to provide access to Redis …   3                    [OK]
redislabs/redisml                   A Redis module that implements several machi…   3                    [OK]
redislabs/k8s-controller                                                            2                    
redislabs/operator-internal         This repository contains pre-released versio…   1                    
redislabs/olmtest                   Test artefact for OLM CSV                       1                    
redislabs/ng-redis-raft             Redis with redis raft module                    0                    
redislabs/memtier_benchmark         Docker image to run memtier_benchmark           0                    
redislabs/olm-bundle                                                                0                    
redislabs/k8s-controller-internal                                                   0                                      
```

2.将官方提供的镜像拉去到本地：
```bash
┌──(leazhi㉿kali-desktop)-[~]
└─$ sudo docker pull redis                 
Using default tag: latest
latest: Pulling from library/redis
a803e7c4b030: Already exists 
8009fe658ed7: Pull complete 
3033e3de8673: Pull complete 
442450dd5ab0: Pull complete 
c47c32a5a8bc: Pull complete 
8007816b16c2: Pull complete 
Digest: sha256:2a2c7d6d79131e532fbf42a9973eb13a279f6b0b96bf7a44e496a9446549003c
Status: Downloaded newer image for redis:latest
docker.io/library/redis:latest
```
3.查看镜像详情（主要所查看监听端口和挂载目录情况）：
```bash
┌──(leazhi㉿kali-desktop)-[~]
└─$ sudo docker inspect redis   
[
    {
        "Id": "sha256:da63666bbe9aec9bd719f3f54c70bbda8c34d059385c4102a37c30d2fa0d7daa",
        "RepoTags": [
            "redis:latest"
        ],
        "RepoDigests": [
            "redis@sha256:2a2c7d6d79131e532fbf42a9973eb13a279f6b0b96bf7a44e496a9446549003c"
        ],
        "Parent": "",
        "Comment": "",
        "Created": "2023-09-20T16:58:10.978001334Z",
        "Container": "a91cd4696dd52c1cf3bde50381881fa8d5cd3e372cecef6b6c29577093ec3ffe",
        "ContainerConfig": {
            "Hostname": "a91cd4696dd5",
            "Domainname": "",
            "User": "",
            "AttachStdin": false,
            "AttachStdout": false,
            "AttachStderr": false,
            "ExposedPorts": {
                "6379/tcp": {}
            },
            "Tty": false,
            "OpenStdin": false,
            "StdinOnce": false,
            "Env": [
                "PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin",
                "GOSU_VERSION=1.16",
                "REDIS_VERSION=7.2.1",
                "REDIS_DOWNLOAD_URL=http://download.redis.io/releases/redis-7.2.1.tar.gz",
                "REDIS_DOWNLOAD_SHA=5c76d990a1b1c5f949bcd1eed90d0c8a4f70369bdbdcb40288c561ddf88967a4"
            ],
            "Cmd": [
                "/bin/sh",
                "-c",
                "#(nop) ",
                "CMD [\"redis-server\"]"
            ],
            "Image": "sha256:c642420e43f6d1afcc9fb2df83bd65e5d1d2550a4e4f0f630afe16f8f089fa51",
            "Volumes": {
                "/data": {}
            },
            "WorkingDir": "/data",
            "Entrypoint": [
                "docker-entrypoint.sh"
            ],
            "OnBuild": null,
            "Labels": {}
        },
        "DockerVersion": "20.10.23",
        "Author": "",
        "Config": {
            "Hostname": "",
            "Domainname": "",
            "User": "",
            "AttachStdin": false,
            "AttachStdout": false,
            "AttachStderr": false,
            "ExposedPorts": {
                "6379/tcp": {}
            },
            "Tty": false,
            "OpenStdin": false,
            "StdinOnce": false,
            "Env": [
                "PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin",
                "GOSU_VERSION=1.16",
                "REDIS_VERSION=7.2.1",
                "REDIS_DOWNLOAD_URL=http://download.redis.io/releases/redis-7.2.1.tar.gz",
                "REDIS_DOWNLOAD_SHA=5c76d990a1b1c5f949bcd1eed90d0c8a4f70369bdbdcb40288c561ddf88967a4"
            ],
            "Cmd": [
                "redis-server"
            ],
            "Image": "sha256:c642420e43f6d1afcc9fb2df83bd65e5d1d2550a4e4f0f630afe16f8f089fa51",
            "Volumes": {
                "/data": {}
            },
            "WorkingDir": "/data",
            "Entrypoint": [
                "docker-entrypoint.sh"
            ],
            "OnBuild": null,
            "Labels": null
        },
        "Architecture": "amd64",
        "Os": "linux",
        "Size": 137679110,
        "VirtualSize": 137679110,
        "GraphDriver": {
            "Data": {
                "LowerDir": "/data/docker/overlay2/4454068880afa6e71de9480db2752e6ca39db384101706072552c420e37202bf/diff:/data/docker/overlay2/94c1e85315f3aac1f2bcda3ea73c9331de6a119f9c37a9819aede1eb7103f013/diff:/data/docker/overlay2/ed4636f8a9a2091570e723ec61743ed20bef9e9f3e5b14f8e9c3bce2d8674d11/diff:/data/docker/overlay2/46462a7e827c3e8ad4bcf1ff5c52b1e3ff21209fe20cd02e5f749e9070d2b851/diff:/data/docker/overlay2/611534c416b1c09fd0bbab971de8e6a1518730defa514182df6151f4a302fb44/diff",
                "MergedDir": "/data/docker/overlay2/0eaddf560a77189e9ebddc501e61fc3db15ed8c60894461df5a581585a5a570c/merged",
                "UpperDir": "/data/docker/overlay2/0eaddf560a77189e9ebddc501e61fc3db15ed8c60894461df5a581585a5a570c/diff",
                "WorkDir": "/data/docker/overlay2/0eaddf560a77189e9ebddc501e61fc3db15ed8c60894461df5a581585a5a570c/work"
            },
            "Name": "overlay2"
        },
        "RootFS": {
            "Type": "layers",
            "Layers": [
                "sha256:d310e774110ab038b30c6a5f7b7f7dd527dbe527854496bd30194b9ee6ea496e",
                "sha256:f914d798e5567829caefe7883b249be525fd9ecf042207e52f2e4c65fd8df56b",
                "sha256:0858861ae2e9a64bd3a212573c23a2c03a011091a9575a6399616c92b60aec03",
                "sha256:7705812a5af850c03d26086cde2c10caf6c3ea08025ba8a38139b9271cb02545",
                "sha256:12bb5bc37f184d757f19bd3cb4998249019ceb03981a2bf8e9f88d844a9e7e56",
                "sha256:a7659bb2e6f249e455da04160e60f23a8133ecb9032f38412f1df1a24c7b09e5"
            ]
        },
        "Metadata": {
            "LastTagTime": "0001-01-01T00:00:00Z"
        }
    }
]
```
**说明**：
从镜像的详情可以看出,该镜像映射的端口为: 6379，且只有一个可以挂载的目录：/data

### 容器部署

1.执行命令：
```bash
┌──(leazhi㉿kali-desktop)-[~]
└─$ sudo docker run -id --name redis --restart=always  -p 6379:6379 -v /data/docker/redis/data:/data redis:latest     
cb74b4d920c3f44c8d1669171cad3cc73acc5ee3af2c0215910da4e8249cde56
```

2.容器启动后，查看启动状态：
```bash
┌──(leazhi㉿kali-desktop)-[~]
└─$ sudo docker ps -a |egrep redis                                                                                
cb74b4d920c3   redis:latest              "docker-entrypoint.s…"   8 seconds ago    Up 6 seconds                0.0.0.0:6379->6379/tcp, :::6379->6379/tcp   redis
```

