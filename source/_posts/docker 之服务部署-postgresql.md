---
title: docker 之服务部署-postgresql
author: leazhi
tags:
  - [postgresql]
  - [database]
  - [docker]
categories:
  - [postgresql]
  - [docker]
date: 2023-09-21 09:26:46
cover: 
discription: 
keywords: postgresql,docker
password: 
message: 
---

## 系统环境
- OS：kali linux 2023.03
- Kernel：6.4.0-kali3-amd64 #1 SMP PREEMPT_DYNAMIC Debian 6.4.11-1kali1 (2023-08-21) x86_64 GNU/Linux
- Docker：Docker version 20.10.25+dfsg1, build b82b9f3

## 服务部署

### 部署前的准备

1.查找 Postgres 的镜像：
```bash
┌──(leazhi㉿kali-desktop)-[~]
└─$ sudo docker search postgresql    
NAME                                   DESCRIPTION                                     STARS     OFFICIAL   AUTOMATED
postgres                               The PostgreSQL object-relational database sy…   12631     [OK]       
bitnami/postgresql                     Bitnami PostgreSQL Docker Image                 235                  [OK]
circleci/postgres                      The PostgreSQL object-relational database sy…   32                   
ubuntu/postgres                        PostgreSQL is an open source object-relation…   31                   
bitnami/postgresql-repmgr                                                              21                   
rapidfort/postgresql                   RapidFort optimized, hardened image for Post…   19                   
rapidfort/postgresql-official          RapidFort optimized, hardened image for Post…   12                   
rapidfort/postgresql12-ib              RapidFort optimized, hardened image for Post…   11                   
ckan/postgresql                        **NOTE**: These images are deprecated and no…   8                    [OK]
bitnamicharts/postgresql                                                               2                    
percona/percona-postgresql-operator    Percona Distribution for PostgreSQL Operator    1                    
sismics/postgresql                     PostgreSQL server                               1                    
bitnamicharts/postgresql-ha                                                            1                    
silintl/postgresql-backup-restore      PostgreSQL backup/restore container             1                    [OK]
litmuschaos/postgresql-client                                                          1                    
islandora/postgresql                                                                   0                    
objectscale/postgresql-repmgr                                                          0                    
pachyderm/postgresql                                                                   0                    
corpusops/postgresql                   postgresql image                                0                    
malcolmnetsec/postgresql                                                               0                    
silintl/postgresql-backup-restore-fs                                                   0                    
vmware/postgresql                                                                      0                    
dhis2/postgresql-curl                                                                  0                    
greenbone/pg-gvm                       PostgreSQL container image for the Greenbone…   0                    
vmware/postgresql-photon                                                               0                    
```

2.将官方提供的镜像拉去到本地：
```bash
┌──(leazhi㉿kali-desktop)-[~]
└─$ sudo docker pull postgres
Using default tag: latest
latest: Pulling from library/postgres
a803e7c4b030: Pull complete 
5cf7cbd17f32: Pull complete 
ddc24c6f1e18: Pull complete 
2b0f4d94850a: Pull complete 
fccb5b7554d1: Pull complete 
1dd940c0e742: Pull complete 
f641e2497276: Pull complete 
9c05395a8e66: Pull complete 
285e24d225ac: Pull complete 
3faa43a5d9fc: Pull complete 
482fc7a6b0f4: Pull complete 
29ca5fe1b2a4: Pull complete 
d3012096b6ce: Pull complete 
Digest: sha256:f86808f55807714e1cf8bc9d60b3f3230132d31ebadd4284c3b47e38d980bdcc
Status: Downloaded newer image for postgres:latest
docker.io/library/postgres:latest
```
3.查看镜像详情（主要所查看监听端口和挂载目录情况）：
```bash
┌──(leazhi㉿kali-desktop)-[~]
└─$ sudo docker inspect postgres 
[
    {
        "Id": "sha256:ec7f99c50d3c6f6a0d528b24ba6f55baa3de3af0f1c3ab3ec5d6183369f1e213",
        "RepoTags": [
            "postgres:latest"
        ],
        "RepoDigests": [
            "postgres@sha256:f86808f55807714e1cf8bc9d60b3f3230132d31ebadd4284c3b47e38d980bdcc"
        ],
        "Parent": "",
        "Comment": "",
        "Created": "2023-09-20T09:36:33.216214288Z",
        "Container": "b3ab2abed6289369197426607e8efbadf6758f82ed59b55306eb592fc02783de",
        "ContainerConfig": {
            "Hostname": "b3ab2abed628",
            "Domainname": "",
            "User": "",
            "AttachStdin": false,
            "AttachStdout": false,
            "AttachStderr": false,
            "ExposedPorts": {
                "5432/tcp": {}
            },
            "Tty": false,
            "OpenStdin": false,
            "StdinOnce": false,
            "Env": [
                "PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/lib/postgresql/16/bin",
                "GOSU_VERSION=1.16",
                "LANG=en_US.utf8",
                "PG_MAJOR=16",
                "PG_VERSION=16.0-1.pgdg120+1",
                "PGDATA=/var/lib/postgresql/data"
            ],
            "Cmd": [
                "/bin/sh",
                "-c",
                "#(nop) ",
                "CMD [\"postgres\"]"
            ],
            "Image": "sha256:d931dbe8995a94a65186529641d6111f99e39178c0627d1f6c6a287230325846",
            "Volumes": {
                "/var/lib/postgresql/data": {}
            },
            "WorkingDir": "",
            "Entrypoint": [
                "docker-entrypoint.sh"
            ],
            "OnBuild": null,
            "Labels": {},
            "StopSignal": "SIGINT"
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
                "5432/tcp": {}
            },
            "Tty": false,
            "OpenStdin": false,
            "StdinOnce": false,
            "Env": [
                "PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/lib/postgresql/16/bin",
                "GOSU_VERSION=1.16",
                "LANG=en_US.utf8",
                "PG_MAJOR=16",
                "PG_VERSION=16.0-1.pgdg120+1",
                "PGDATA=/var/lib/postgresql/data"
            ],
            "Cmd": [
                "postgres"
            ],
            "Image": "sha256:d931dbe8995a94a65186529641d6111f99e39178c0627d1f6c6a287230325846",
            "Volumes": {
                "/var/lib/postgresql/data": {}
            },
            "WorkingDir": "",
            "Entrypoint": [
                "docker-entrypoint.sh"
            ],
            "OnBuild": null,
            "Labels": null,
            "StopSignal": "SIGINT"
        },
        "Architecture": "amd64",
        "Os": "linux",
        "Size": 418497773,
        "VirtualSize": 418497773,
        "GraphDriver": {
            "Data": {
                "LowerDir": "/data/docker/overlay2/beba90f718f65c19bce8fa1a23a26378920ee26a5cd2c329230579c2e723f48a/diff:/data/docker/overlay2/d6294637bba992e6fd7f8b56966e76d08ccf61a0999a283208fba9eea008baa6/diff:/data/docker/overlay2/a01683e4a9171b67d2d384f9b2cc20ffaf0120985bf586eec92f2c043ef5963c/diff:/data/docker/overlay2/9da1e20f098d230796d8454e27be73f5edb5aef05e2e3514da990b43f271dff9/diff:/data/docker/overlay2/6ab94d37ecd63fcc6cdd62d37c4b0e68524697c719e5960c024d17a1aeb465fb/diff:/data/docker/overlay2/ac1f1f18d2b4bdd865c2413a051380f28aa6d8e5d7faabb147f256ec24dc1778/diff:/data/docker/overlay2/57ac87be3c125624eece7d90f9e591b7a501a0c5dbab78c66e9beb0c226e4f82/diff:/data/docker/overlay2/1917afc2fdc229787c26bd017d0b4c2e4fa75bcc23c068ba053190b99e973ad3/diff:/data/docker/overlay2/aca0821d4f48bbd4650a88cafc4ba7a93ba3e4c085f0d618f88e4b869ed18284/diff:/data/docker/overlay2/828a2915eb323254f09aed3e2e118aeea786fda40f6910c095da1cfe8a17ffb8/diff:/data/docker/overlay2/78ae5345ba4ecd2b8b5496db1904f446729129bbafb24ce4cf7091c1a6332e5a/diff:/data/docker/overlay2/611534c416b1c09fd0bbab971de8e6a1518730defa514182df6151f4a302fb44/diff",
                "MergedDir": "/data/docker/overlay2/2dd1d4e99b3c8b209ddd295a0db5dd3c7e70986e2e9b40459b08e759c60f4256/merged",
                "UpperDir": "/data/docker/overlay2/2dd1d4e99b3c8b209ddd295a0db5dd3c7e70986e2e9b40459b08e759c60f4256/diff",
                "WorkDir": "/data/docker/overlay2/2dd1d4e99b3c8b209ddd295a0db5dd3c7e70986e2e9b40459b08e759c60f4256/work"
            },
            "Name": "overlay2"
        },
        "RootFS": {
            "Type": "layers",
            "Layers": [
                "sha256:d310e774110ab038b30c6a5f7b7f7dd527dbe527854496bd30194b9ee6ea496e",
                "sha256:6e9a0572621249555f32ce0b0169248bf8022e862b0878655813a1337c67a18f",
                "sha256:4ae0beb46ef7c2fbab94703d6793df0d2ac225a67403102df92d32d1454f486b",
                "sha256:c07ccebfbea94338688c026acc489eb39386a76cef00e214d5c55027b0c40fa2",
                "sha256:39bedf69977302187c3907c8bcb401ab0a4d73dea6ea497a4abfda42c6a84e7a",
                "sha256:181b891be273d1a81bcf3fad4449b81cecf84ecb8c2eab29384c3fd0c1252612",
                "sha256:e967f2e0f2981c7dc3483cddc4b3d409fbaeb02d4f293ddb06a5c4ba5aff6951",
                "sha256:6d57e974a40685c261854e59e60a957114474c040520689ad6d038512402e5d7",
                "sha256:61242945d49ee3abbe7966ead3146041c08afd4eb9b3149c94f04320bcd3f081",
                "sha256:28912595661dd0a384a678c7b28dc8ffaf6ef26b656d9c28413bfcda8569ae31",
                "sha256:a4cdddd8a8927786180bac875b864d919406ed9c68a32c74f283d5f945229cfc",
                "sha256:e927761b03d995f6ca7387b7cc2e4ba5269d15364ab935a5b93de8e92968e136",
                "sha256:826ef844bbc2b33ffe9fb6566554811a2cd0aef71a95a7ce623a67a4d7329245"
            ]
        },
        "Metadata": {
            "LastTagTime": "0001-01-01T00:00:00Z"
        }
    }
]
```
**说明**：
从镜像的详情可以看出,该镜像映射的端口为: 5432，且只有一个可以挂载的目录：/var/lib/postgresql/data

### 容器部署

1.执行命令：
```bash
┌──(leazhi㉿kali-desktop)-[~]
└─$ sudo docker run -id --name postgresql --restart=always -e POSTGRES_PASSWORD=chekir  -p 5432:5432 -v /data/docker/postgres/data:/var/lib/postgresql/data postgres:latest 
25d1ab19045d8007927b6e0398e45563355b38ad5eb17e6ae6661909587c3fc8
```

2.容器启动后，查看启动状态：
```bash
┌──(leazhi㉿kali-desktop)-[~]
└─$ sudo docker ps -a |egrep postgres                                                                                                                                       
25d1ab19045d   postgres:latest           "docker-entrypoint.s…"   5 seconds ago    Up 4 seconds                0.0.0.0:5432->5432/tcp, :::5432->5432/tcp   postgresql
```

3.查看挂载的目录是否有文件：
```bash
┌──(leazhi㉿kali-desktop)-[~]
└─$ sudo ls /data/docker/postgres/data
base	pg_commit_ts  pg_hba.conf    pg_logical    pg_notify	pg_serial     pg_stat	   pg_subtrans	pg_twophase  pg_wal   postgresql.auto.conf  postmaster.opts
global	pg_dynshmem   pg_ident.conf  pg_multixact  pg_replslot	pg_snapshots  pg_stat_tmp  pg_tblspc	PG_VERSION   pg_xact  postgresql.conf	    postmaster.pid
```




















































