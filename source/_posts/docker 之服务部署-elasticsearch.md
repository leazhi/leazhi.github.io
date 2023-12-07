---
title: docker 之服务部署-elasticsearch
author: leazhi
tags:
  - [docker]
  - [elasticsearch]
categories:
  - [docker]
date: 2023-09-21 19:36:18
cover: 
discription: 
keywords: 
password: 
message: 
---


## 系统环境
- OS：kali linux 2023.03
- Kernel：6.4.0-kali3-amd64 #1 SMP PREEMPT_DYNAMIC Debian 6.4.11-1kali1 (2023-08-21) x86_64 GNU/Linux
- Docker：Docker version 20.10.25+dfsg1, build b82b9f3

## 服务部署

### 部署前的准备

1.查找 elasticsearch 的镜像：
```bash
┌──(leazhi㉿kali-desktop)-[~]
└─$ sudo docker search elasticsearch
NAME                                     DESCRIPTION                                     STARS     OFFICIAL   AUTOMATED
elasticsearch                            Elasticsearch is a powerful open source sear…   6170      [OK]       
kibana                                   Kibana gives shape to any kind of data — str…   2653      [OK]       
itzg/elasticsearch                       Provides an easily configurable Elasticsearc…   71                   [OK]
bitnami/elasticsearch                    Bitnami Docker Image for Elasticsearch          69                   [OK]
rapidfort/elasticsearch                  RapidFort optimized, hardened image for Elas…   10                   
bitnami/elasticsearch-exporter           Bitnami Elasticsearch Exporter Docker Image     7                    [OK]
dtagdevsec/elasticsearch                 T-Pot Elasticsearch                             4                    [OK]
rancher/elasticsearch-conf                                                               2                    
itzg/elasticsearch-curator               Provides an image of the latest pip installe…   2                    [OK]
eucm/elasticsearch                       Elasticsearch 1.7.5 Docker Image                1                    [OK]
wikibase/elasticsearch                   Elasticsearch with custom plugin for Wikimed…   1                    
onlyoffice/elasticsearch                                                                 1                    
rancher/elasticsearch                                                                    1                    
rancher/elasticsearch-bootstrap                                                          1                    
bitnami/elasticsearch-curator-archived   A copy of the container images of the deprec…   0                    
couchbase/elasticsearch-connector        Couchbase Elasticsearch Connector               0                    
corpusops/elasticsearch                  https://github.com/corpusops/docker-images/     0                    
bitnamicharts/elasticsearch                                                              0                    
wodby/elasticsearch                                                                      0                    
vulhub/elasticsearch                                                                     0                    
uselagoon/elasticsearch-7                                                                0                    
securecodebox/elasticsearch                                                              0                    
rapidfort/elasticsearch-official         RapidFort optimized, hardened image for Elas…   0                    
ilios/elasticsearch                                                                      0                    
uselagoon/elasticsearch-6                                                                0                              
```

2.将官方提供的镜像拉去到本地：
```bash
┌──(leazhi㉿kali-desktop)-[~]
└─$ sudo docker pull delron/elasticsearch-ik:2.4.6-1.0
2.4.6-1.0: Pulling from delron/elasticsearch-ik
c73ab1c6897b: Pull complete 
1ab373b3deae: Pull complete 
b542772b4177: Pull complete 
0bcc3741ab14: Pull complete 
421d624d778d: Pull complete 
26ad58237506: Pull complete 
8dbabc90b2b8: Pull complete 
982930be204d: Pull complete 
dd3701aa40a6: Pull complete 
0208b5993a36: Pull complete 
bd0cc92b3fbc: Pull complete 
321b7f780a51: Pull complete 
50e4ba24b25e: Pull complete 
5bc0b341038a: Pull complete 
97d0f8ec7bef: Pull complete 
d11a11e9292d: Pull complete 
ea4bf27b0f68: Pull complete 
87fb9f66651a: Pull complete 
Digest: sha256:c24dbb28fef2f4223fd2784935238773cb25edd9c93fe68f8af86a376e701dbe
Status: Downloaded newer image for delron/elasticsearch-ik:2.4.6-1.0
docker.io/delron/elasticsearch-ik:2.4.6-1.0
```
3.查看镜像详情（主要所查看监听端口和挂载目录情况）：
```bash
┌──(leazhi㉿kali-desktop)-[~]
└─$ sudo docker inspect delron/elasticsearch-ik:2.4.6-1.0

[
    {
        "Id": "sha256:095b6487fb779674e06682f1f407f91dabb38aca3dcc8fe6335eef6731dad880",
        "RepoTags": [
            "delron/elasticsearch-ik:2.4.6-1.0"
        ],
        "RepoDigests": [
            "delron/elasticsearch-ik@sha256:c24dbb28fef2f4223fd2784935238773cb25edd9c93fe68f8af86a376e701dbe"
        ],
        "Parent": "",
        "Comment": "",
        "Created": "2018-04-28T06:46:03.860224934Z",
        "Container": "af942161a74819055cdccd3ef7d4a2fdbcfd7cb204fff7a4ab4bdee15508a9f4",
        "ContainerConfig": {
            "Hostname": "",
            "Domainname": "",
            "User": "",
            "AttachStdin": false,
            "AttachStdout": false,
            "AttachStderr": false,
            "ExposedPorts": {
                "9200/tcp": {},
                "9300/tcp": {}
            },
            "Tty": false,
            "OpenStdin": false,
            "StdinOnce": false,
            "Env": [
                "PATH=/usr/share/elasticsearch/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin",
                "LANG=C.UTF-8",
                "JAVA_HOME=/docker-java-home/jre",
                "JAVA_VERSION=8u162",
                "JAVA_DEBIAN_VERSION=8u162-b12-1~deb9u1",
                "CA_CERTIFICATES_JAVA_VERSION=20170531+nmu1",
                "GOSU_VERSION=1.10",
                "ELASTICSEARCH_VERSION=2.4.6",
                "ELASTICSEARCH_DEB_VERSION=2.4.6"
            ],
            "Cmd": [
                "/bin/sh",
                "-c",
                "tar -xvf /tmp/elasticsearch-analysis-ik-1.10.6.tar.gz -C /tmp &&     cd /tmp/elasticsearch-analysis-ik-1.10.6 &&         mvn package &&             unzip target/releases/elasticsearch-analysis-ik-1.10.6.zip -d /usr/share/elasticsearch/plugins/analysis-ik &&                 cd / &&                     rm -rf /tmp/elasticsearch-analysis-ik*"
            ],
            "ArgsEscaped": true,
            "Image": "sha256:96a1b69e374ad3b91c578c8b5c8150e566bbcea7ec816fafa0fd6920294570a9",
            "Volumes": {
                "/usr/share/elasticsearch/data": {}
            },
            "WorkingDir": "/usr/share/elasticsearch",
            "Entrypoint": null,
            "OnBuild": [],
            "Labels": null
        },
        "DockerVersion": "18.03.1-ce",
        "Author": "Delron Kung \"delron.kung@gmail.com\"",
        "Config": {
            "Hostname": "",
            "Domainname": "",
            "User": "",
            "AttachStdin": false,
            "AttachStdout": false,
            "AttachStderr": false,
            "ExposedPorts": {
                "9200/tcp": {},
                "9300/tcp": {}
            },
            "Tty": false,
            "OpenStdin": false,
            "StdinOnce": false,
            "Env": [
                "PATH=/usr/share/elasticsearch/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin",
                "LANG=C.UTF-8",
                "JAVA_HOME=/docker-java-home/jre",
                "JAVA_VERSION=8u162",
                "JAVA_DEBIAN_VERSION=8u162-b12-1~deb9u1",
                "CA_CERTIFICATES_JAVA_VERSION=20170531+nmu1",
                "GOSU_VERSION=1.10",
                "ELASTICSEARCH_VERSION=2.4.6",
                "ELASTICSEARCH_DEB_VERSION=2.4.6"
            ],
            "Cmd": [
                "elasticsearch"
            ],
            "ArgsEscaped": true,
            "Image": "sha256:96a1b69e374ad3b91c578c8b5c8150e566bbcea7ec816fafa0fd6920294570a9",
            "Volumes": {
                "/usr/share/elasticsearch/data": {}
            },
            "WorkingDir": "/usr/share/elasticsearch",
            "Entrypoint": [
                "/docker-entrypoint.sh"
            ],
            "OnBuild": [],
            "Labels": null
        },
        "Architecture": "amd64",
        "Os": "linux",
        "Size": 688929420,
        "VirtualSize": 688929420,
        "GraphDriver": {
            "Data": {
                "LowerDir": "/data/docker/overlay2/d450977c4408ec6adba9dfe5fb0d6517e04612292a98fdb111a97c1cf703f360/diff:/data/docker/overlay2/eb17d210216cbbed5e05987850fae654799448e5f6c4a370550a507afd81da9d/diff:/data/docker/overlay2/ccf637762d9ff52e7af9f6869371c810a4f85f5a1ce7882b5bdd436931190852/diff:/data/docker/overlay2/3f2ae8199910fa1e6becacd95d3715a9fa5ee338d2a3ee2d21cbd322123f34bc/diff:/data/docker/overlay2/7bfea164346d4aa6454c557967c57540068ff4acb2079dd8be38ddead91c5d3d/diff:/data/docker/overlay2/8603ba370f092cfa8554280d7136aeee836808aeeef2b5009d26a4f76794f597/diff:/data/docker/overlay2/6579b9edb43d477068893b2f586254d916aeeca6383848b8905a6510324f416a/diff:/data/docker/overlay2/ba6ecfdd1b881cad17e74c0b2d8aea80dea3e94cfdb5408ae46d2bde8167c1db/diff:/data/docker/overlay2/34fa7e8396468f858046833849b9002c6b873dead10471b4a3e198ec6a3f8a24/diff:/data/docker/overlay2/0f537ccc4f08a536366fbbc21721ce153f190f449040d6fbbabe1c96eb087a47/diff:/data/docker/overlay2/7aa7863690da5a1449ba4a61138fb3ca55bdf4672646d8ac63231318cc1b09e3/diff:/data/docker/overlay2/cfb19725eedca7cc8ee03c163c7e877351928b9f4702c88ed73c790f571a2523/diff:/data/docker/overlay2/015b3fc31027d92c0ebe5f23145d46e25565ab7b6f9368ab80a4ab7be45b3ad9/diff:/data/docker/overlay2/26869628527b5db6d74c66d32b1cacc17c76a6ffe106e31096f06fe204c1e2b6/diff:/data/docker/overlay2/dc8f714bfde99bce38a1865cd44aef4a54bbeb8c599038088ffc7a6329b3a4a4/diff:/data/docker/overlay2/70414b4368ba535de414589f4d8ee0a07ce1f02c6592bd318e3a23238a6fee75/diff:/data/docker/overlay2/ae282f311721dc1dc11167a344c18a5be2295298dc4da29d3612bff4ace9737e/diff",
                "MergedDir": "/data/docker/overlay2/568eb2b874008a32b4ee666203558bc3c1144bd48979388d63204a5c4d0dc05c/merged",
                "UpperDir": "/data/docker/overlay2/568eb2b874008a32b4ee666203558bc3c1144bd48979388d63204a5c4d0dc05c/diff",
                "WorkDir": "/data/docker/overlay2/568eb2b874008a32b4ee666203558bc3c1144bd48979388d63204a5c4d0dc05c/work"
            },
            "Name": "overlay2"
        },
        "RootFS": {
            "Type": "layers",
            "Layers": [
                "sha256:e1df5dc88d2cc2cd9a1b1680ec3cb92a2dc924a0205125d85da0c61083b4e87d",
                "sha256:bcbe43405751dd3c9b9d0b348504e30fc66e3bb1edd53d8ba0b3919a0368f4b1",
                "sha256:61c06e07759a0119829dfabc36c925028f4ba7436942a64e7d876acc87006f3f",
                "sha256:341d865c1c22fd099270b3e0e47bbcf771b64682e779d956610e0ea5dfc65be4",
                "sha256:a1a8b7f7efaccaa75f99614ffe1dc821b422304a6efd87318ec15c969bdde820",
                "sha256:00439e7d6354b383f23fdc17e641a8f2fb0727822cf6664c12b7befa28e7ea6f",
                "sha256:6a47dae912f74b31175a19f295dc1a1e04572f055dde1e84128f1bc7fbe1b7a5",
                "sha256:148268bf14bec027a98d1b0668b12c8a7e7d8ec85389adb67296f23b4703261e",
                "sha256:a2e243de3b25c50e630adc3251c8f6e68edef9f4266048328809d52490c0cc03",
                "sha256:de627896dca34784f76eede38200bc982471c6e5fc670649d8730bd3c8d45282",
                "sha256:17f91151269600bbf4f927af2b3a591411f7eb53b31f677390edbeb6c8bc0f52",
                "sha256:b2fe7244d7f12871e70bd0b2ebd1262e6fba3f1a7faf729bbc9b4c8afce67cf9",
                "sha256:d855c2aa2bdc6cde9b47858a079ac5e43aa4b72060cbb413c8f677113ae3cc32",
                "sha256:452c678e50f26aa581357bd763e87cae7b73c7048db31b4b837b87cc2d46f364",
                "sha256:7ad04f8ff0ef43c92028b4881832eaa6f384b8fe36a7c6487817f22695147e01",
                "sha256:1b795c17892b87aec95a07c093b321ca654b5557e03e01f4eb8568a7cd42ea20",
                "sha256:40d78660ae738fde1b3882d4f096087033c3e30854ca0812e9eec14788bfa31f",
                "sha256:b2a133652b1178c62ac5859a9f42227649a06be1e8b8a08d170516b61c65da22"
            ]
        },
        "Metadata": {
            "LastTagTime": "0001-01-01T00:00:00Z"
        }
    }
]
```
**说明**：
从镜像的详情可以看出,该镜像映射的端口为: 9200, 9300，且只有一个可以挂载的目录：/usr/share/elasticsearch/data

### 容器部署

1.执行命令：
```bash
┌──(leazhi㉿kali-desktop)-[~]
└─$ sudo docker run -id --network=host --name elasticsearch -p 9200:9200 delron/elasticsearch-ik:2.4.6-1.0 -Des.network.publish_host="192.168.3.200"

WARNING: Published ports are discarded when using host network mode
a3ab9ed757b1246faebdee1f626fdd0362ef9831a32c5799f6cf2b63d7b4dc16
```

2.容器启动后，查看启动状态：
```bash
┌──(leazhi㉿kali-desktop)-[~]
└─$ sudo docker ps -a |egrep elasticsearch                                   
CONTAINER ID   IMAGE                               COMMAND                  CREATED              STATUS                      PORTS                                       NAMES
a3ab9ed757b1   delron/elasticsearch-ik:2.4.6-1.0   "/docker-entrypoint.…"   About a minute ago   Up About a minute                                                       elasticsearch

```

3.打开浏览器，输入本季机P和映射的端口进行访问：
![](../static/img/20230921/20230921001.jpeg)