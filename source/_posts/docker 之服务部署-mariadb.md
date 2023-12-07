---
title: docker 之服务部署-mariadb
author: leazhi
tags:
  - [mysql]
  - [mariadb]
  -  [database]
  -  [docker]
categories:
  - [mysql]
  - [docker]
date: 2023-09-20 18:30:30
cover: 
discription: 
keywords: mariadb, docker
password: 
message: 
---

## 系统环境
- OS：kali linux 2023.03
- Kernel：6.4.0-kali3-amd64 #1 SMP PREEMPT_DYNAMIC Debian 6.4.11-1kali1 (2023-08-21) x86_64 GNU/Linux
- Docker：Docker version 20.10.25+dfsg1, build b82b9f3

## 服务部署

### 部署前的准备

1.查找 Mariadb 的镜像：
```bash
┌──(leazhi㉿kali-desktop)-[~]
└─$ sudo docker search mariadb
NAME                           DESCRIPTION                                     STARS     OFFICIAL   AUTOMATED
mariadb                        MariaDB Server is a high performing open sou…   5520      [OK]       
phpmyadmin                     phpMyAdmin - A web interface for MySQL and M…   866       [OK]       
linuxserver/mariadb            A Mariadb container, brought to you by Linux…   365                  
bitnami/mariadb                Bitnami MariaDB Docker Image                    183                  [OK]
mariadb/server                 MariaDB Server is a modern database for mode…   56                   [OK]
mariadb/maxscale               MariaDB MaxScale - The world's most advanced…   30                   [OK]
bitnami/mariadb-galera                                                         23                   
rapidfort/mariadb              RapidFort optimized, hardened image for Mari…   20                   
rapidfort/mariadb-official     RapidFort optimized, hardened image for Mari…   11                   
rapidfort/mariadb-ib           RapidFort optimized, hardened image for Mari…   8                    
wodby/mariadb                  Alpine-based MariaDB container image with or…   7                    [OK]
circleci/mariadb               CircleCI images for MariaDB                     4                    [OK]
mariadb/columnstore            MariaDB ColumnStore Engine for Analytics        4                    
clearlinux/mariadb             MariaDB relational database management syste…   3                    [OK]
mariadb/xpand-single           Single node MariaDB Xpand docker image, for …   3                    
bitnamicharts/mariadb                                                          0                    
vmware/mariadb-photon                                                          0                    
cimg/mariadb                                                                   0                    
bitnamicharts/mariadb-galera                                                   0                    
docksal/mariadb                MariaDB service images for Docksal - https:/…   0                    
jumpserver/mariadb             MariaDB Server is a high performing open sou…   0                    
uselagoon/mariadb                                                              0                    
blackflysolutions/mariadb      Mariadb for Drupal/CiviCRM                      0                    [OK]
drud/mariadb-local             ddev mariadb local container                    0                    
uselagoon/mariadb-drupal                                                       0                    
```

2.将官方提供的镜像拉去到本地：
```bash
┌──(leazhi㉿kali-desktop)-[~]
└─$ sudo docker pull mariadb:latest  
latest: Pulling from library/mariadb
44ba2882f8eb: Already exists 
08b8223d0cb6: Pull complete 
ef2696fb09d6: Pull complete 
6ae32c298a0d: Pull complete 
5dc97cb97b44: Pull complete 
a3e4bee69a58: Pull complete 
b29c582204c9: Pull complete 
f05405b8aaed: Pull complete 
Digest: sha256:5d851e999b84625ef9810589e832686cae58453452698ee69e2980041e626eb2
Status: Downloaded newer image for mariadb:latest
docker.io/library/mariadb:latest
```

3.查看镜像详情（主要所查看监听端口和挂载目录情况）：
```bash
┌──(leazhi㉿kali-desktop)-[~]
└─$ sudo docker inspect mariadb                
[
    {
        "Id": "sha256:871a9153c1842e3b480cb1be691c64151bc96a5de2cc8039470d31e32ff0a177",
        "RepoTags": [
            "mariadb:latest"
        ],
        "RepoDigests": [
            "mariadb@sha256:5d851e999b84625ef9810589e832686cae58453452698ee69e2980041e626eb2"
        ],
        "Parent": "",
        "Comment": "",
        "Created": "2023-09-02T01:00:25.783115724Z",
        "Container": "af23a5afee248d2e8a1999ffd0a5554f22ed5987408c3820b4e75f566acf899e",
        "ContainerConfig": {
            "Hostname": "af23a5afee24",
            "Domainname": "",
            "User": "",
            "AttachStdin": false,
            "AttachStdout": false,
            "AttachStderr": false,
            "ExposedPorts": {
                "3306/tcp": {}
            },
            "Tty": false,
            "OpenStdin": false,
            "StdinOnce": false,
            "Env": [
                "PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin",
                "GOSU_VERSION=1.14",
                "LANG=C.UTF-8",
                "MARIADB_VERSION=1:11.1.2+maria~ubu2204"
            ],
            "Cmd": [
                "/bin/sh",
                "-c",
                "#(nop) ",
                "CMD [\"mariadbd\"]"
            ],
            "Image": "sha256:2225d6a519df0af162ece2335dc7d5f8cfcd70dde9150bc446cf47734b05140a",
            "Volumes": {
                "/var/lib/mysql": {}
            },
            "WorkingDir": "",
            "Entrypoint": [
                "docker-entrypoint.sh"
            ],
            "OnBuild": null,
            "Labels": {
                "org.opencontainers.image.authors": "MariaDB Community",
                "org.opencontainers.image.base.name": "docker.io/library/ubuntu:jammy",
                "org.opencontainers.image.description": "MariaDB Database for relational SQL",
                "org.opencontainers.image.documentation": "https://hub.docker.com/_/mariadb/",
                "org.opencontainers.image.licenses": "GPL-2.0",
                "org.opencontainers.image.ref.name": "ubuntu",
                "org.opencontainers.image.source": "https://github.com/MariaDB/mariadb-docker",
                "org.opencontainers.image.title": "MariaDB Database",
                "org.opencontainers.image.url": "https://github.com/MariaDB/mariadb-docker",
                "org.opencontainers.image.vendor": "MariaDB Community",
                "org.opencontainers.image.version": "11.1.2"
            }
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
                "3306/tcp": {}
            },
            "Tty": false,
            "OpenStdin": false,
            "StdinOnce": false,
            "Env": [
                "PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin",
                "GOSU_VERSION=1.14",
                "LANG=C.UTF-8",
                "MARIADB_VERSION=1:11.1.2+maria~ubu2204"
            ],
            "Cmd": [
                "mariadbd"
            ],
            "Image": "sha256:2225d6a519df0af162ece2335dc7d5f8cfcd70dde9150bc446cf47734b05140a",
            "Volumes": {
                "/var/lib/mysql": {}
            },
            "WorkingDir": "",
            "Entrypoint": [
                "docker-entrypoint.sh"
            ],
            "OnBuild": null,
            "Labels": {
                "org.opencontainers.image.authors": "MariaDB Community",
                "org.opencontainers.image.base.name": "docker.io/library/ubuntu:jammy",
                "org.opencontainers.image.description": "MariaDB Database for relational SQL",
                "org.opencontainers.image.documentation": "https://hub.docker.com/_/mariadb/",
                "org.opencontainers.image.licenses": "GPL-2.0",
                "org.opencontainers.image.ref.name": "ubuntu",
                "org.opencontainers.image.source": "https://github.com/MariaDB/mariadb-docker",
                "org.opencontainers.image.title": "MariaDB Database",
                "org.opencontainers.image.url": "https://github.com/MariaDB/mariadb-docker",
                "org.opencontainers.image.vendor": "MariaDB Community",
                "org.opencontainers.image.version": "11.1.2"
            }
        },
        "Architecture": "amd64",
        "Os": "linux",
        "Size": 403892817,
        "VirtualSize": 403892817,
        "GraphDriver": {
            "Data": {
                "LowerDir": "/data/docker/overlay2/e64c9918cb2c4ae55b7af37c58a4a9efdca82f90173060eba675869d4c2c3801/diff:/data/docker/overlay2/f22e25ac147bfba786fae4d18be0ab94ae5c11c87fbe9abaef189966906215a8/diff:/data/docker/overlay2/5ca1794495a53868b285204663dcacb8c825ca12330c6bcfb989e52994b9f32e/diff:/data/docker/overlay2/a731baf6623e80f56f40fdd9feda5d298687879764750f04ad2f498869a28f55/diff:/data/docker/overlay2/18bc279f7866622ca6e8a063c912f1d4f617d4aef7633a1b1e3da2cc70417870/diff:/data/docker/overlay2/6c68fbf61cb29f0d3ebcdaebd003569d418991fe7ba5dd783b7244f48674ade7/diff:/data/docker/overlay2/6683fdd2d4a8ee6f185b4cd973303cb0603e3013479a5ed88a2c185d419883ee/diff",
                "MergedDir": "/data/docker/overlay2/c78494707373ba9cd12cb27e2d66e55ec6297cc52ad1d20a6d6220c145920ae8/merged",
                "UpperDir": "/data/docker/overlay2/c78494707373ba9cd12cb27e2d66e55ec6297cc52ad1d20a6d6220c145920ae8/diff",
                "WorkDir": "/data/docker/overlay2/c78494707373ba9cd12cb27e2d66e55ec6297cc52ad1d20a6d6220c145920ae8/work"
            },
            "Name": "overlay2"
        },
        "RootFS": {
            "Type": "layers",
            "Layers": [
                "sha256:dc0585a4b8b71f7f4eb8f2e028067f88aec780d9ab40c948a8d431c1aeadeeb5",
                "sha256:b98774c4f9f3c3ea36a821da80765cce90c3a3845050e7a57e2ac31c62f7dea4",
                "sha256:c59473bf006b01112b8fdf8949ae666d7b1da4dc9304d359a5cafb0512d51231",
                "sha256:5e31e6c6a34f7b6e454756a201a89ec90ee7fc6694fef27b029809b158e55029",
                "sha256:3dec444dd9d3f6943fba9641695c80c0f42d95c9d632becf0fa649a26c25c2b6",
                "sha256:7fd75d6714c05e5154c8a826f700afe8aa36e002727284310adc69da3748c3ee",
                "sha256:0691396ab7f7d7ff5b8051871975de414a165939e5ac49c4465958723c9e8cf3",
                "sha256:c94b74e8251f91e31ad456ba8ac40b0b58e47b67a5773e91df300ea14592ec45"
            ]
        },
        "Metadata": {
            "LastTagTime": "0001-01-01T00:00:00Z"
        }
    }
]
```

**说明**：
从镜像的详情可以看出,该镜像映射的端口为: 3306,只有一个可以挂载的目录： /var/lib/mysql

### 容器部署

1.执行命令：
```bash
┌──(leazhi㉿kali-desktop)-[~]
└─$ sudo docker run -id --name mariadb --restart always -v /data/docker/mariadb/data:/var/lib/mysql -p 3306:3306 -e MARIADB_ROOT_PASSWORD="chekir"  mariadb:latest    

1fbec4c0a3001ff3e676663b2a8f85db462e2a40b081be5b367d4fdf9e35a251
```

2.容器启动后，查看启动状态：
```bash
┌──(leazhi㉿kali-desktop)-[~]
└─$ sudo docker ps -a |egrep mariadb
1fbec4c0a300   mariadb:latest            "docker-entrypoint.s…"   13 hours ago   Up 3 seconds                0.0.0.0:3306->3306/tcp, :::3306->3306/tcp   mariadb
```

3.查看挂载的目录是否有文件：
```bash
┌──(leazhi㉿kali-desktop)-[~]
└─$ sudo ls /data/docker/mariadb/data/
3764f47ea28f.pid   aria_log_control	    ddl_recovery.log  ibdata1	   ibtmp1		 multi-master.info  performance_schema	undo001  undo003
aria_log.00000001  ddl_recovery-backup.log  ib_buffer_pool    ib_logfile0  mariadb_upgrade_info  mysql		    sys			undo002
```

## 思考

需求：假如我现在要把 mariadb 的配置目录 /etc/mysql 和 日志目录 /var/log/mysql 也挂载出来，那么该怎么操作呢？

**首先**：按照上面的步骤想将 mariadb 正常部署一遍（上面有部署步骤，这里就不再重复部署了）：

**其次**：从启动的容器中 cp 出 mariadb 容器里面的配置目录和日志目录:
```bash
┌──(leazhi㉿kali-desktop)-[~]
└─$ sudo docker cp mariadb:/etc/mysql /data/docker/mariadb/etc                                          

┌──(leazhi㉿kali-desktop)-[~]
└─$ sudo docker cp mariadb:/var/log/mysql /data/docker/mariadb/log
```

**接着**：删除运行的 mariadb 容器：
```bash
┌──(leazhi㉿kali-desktop)-[~]
└─$ sudo docker rm -f mariadb
mariadb
```

**最后**：重新以挂在的方式启动容器
```bash
┌──(leazhi㉿kali-desktop)-[~]
└─$ sudo docker run -id --name mariadb --restart always -v /data/docker/mariadb/data:/var/lib/mysql -v /data/docker/mariadb/etc:/etc/mysql -v /data/docker/mariadb/log:/var/log/mysql -p 3306:3306 -e MARIADB_ROOT_PASSWORD="chekir"  mariadb:latest  
603c35919526e30d16dd67dd94a1ad320df79224981e01ad7127c9041a2e94b0
```

启动后，查看下状态：
```bash
┌──(leazhi㉿kali-desktop)-[~]
└─$ sudo docker ps -a |egrep mariadb
603c35919526   mariadb:latest            "docker-entrypoint.s…"   17 seconds ago   Up 16 seconds               0.0.0.0:3306->3306/tcp, :::3306->3306/tcp   mariadb
```