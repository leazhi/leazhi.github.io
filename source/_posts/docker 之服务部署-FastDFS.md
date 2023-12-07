---
title: docker 之服务部署-FastDFS
author: leazhi
tags:
  - [docker]
  - [fastdfs]
categories:
  - [docker]
date: 2023-09-22 11:08:46
cover: 
discription: 
keywords: fastdfs
password: 
message: 
---


## 系统环境
- OS：kali linux 2023.03
- Kernel：6.4.0-kali3-amd64 #1 SMP PREEMPT_DYNAMIC Debian 6.4.11-1kali1 (2023-08-21) x86_64 GNU/Linux
- Docker：Docker version 20.10.25+dfsg1, build b82b9f3

## 服务部署

### 部署前的准备

1.拉去 FastDFS 镜像到本地：


1.2.将gitlab 官方打包的镜像拉去下来：
```bash
┌──(leazhi㉿kali-desktop)-[~]
└─$ sudo docker pull delron/fastdfs                    
[sudo] leazhi 的密码：
Using default tag: latest
latest: Pulling from delron/fastdfs
Digest: sha256:9583cb80170c153bc12615fd077fe364a8fd5a95194b7cf9a8a32d2c11f8a49d
Status: Image is up to date for delron/fastdfs:latest
docker.io/delron/fastdfs:latest

```

2.查看镜像的一些信息（比如挂在目录，端口等），为后面的启动参数作准备：
```bash
┌──(leazhi㉿kali-desktop)-[~]
└─$ sudo docker inspect delron/fastdfs 
[
    {
        "Id": "sha256:8487e86fc6ee1f1d2e853821b42a1ce757fdef563278ffea8e89fb0feabc0f07",
        "RepoTags": [
            "delron/fastdfs:latest"
        ],
        "RepoDigests": [
            "delron/fastdfs@sha256:9583cb80170c153bc12615fd077fe364a8fd5a95194b7cf9a8a32d2c11f8a49d"
        ],
        "Parent": "",
        "Comment": "",
        "Created": "2018-04-29T06:32:56.114547705Z",
        "Container": "cd2f12fe9c2da442aff56e095ac8e9bf878ec27bbffd4e90bd68c3030f247f8b",
        "ContainerConfig": {
            "Hostname": "cd2f12fe9c2d",
            "Domainname": "",
            "User": "",
            "AttachStdin": false,
            "AttachStdout": false,
            "AttachStderr": false,
            "ExposedPorts": {
                "22122/tcp": {},
                "23000/tcp": {},
                "8080/tcp": {},
                "8888/tcp": {}
            },
            "Tty": false,
            "OpenStdin": false,
            "StdinOnce": false,
            "Env": [
                "PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin",
                "FASTDFS_PATH=/opt/fdfs",
                "FASTDFS_BASE_PATH=/var/fdfs",
                "PORT=",
                "GROUP_NAME=",
                "TRACKER_SERVER="
            ],
            "Cmd": [
                "/bin/sh",
                "-c",
                "#(nop) ",
                "CMD [\"tracker\"]"
            ],
            "ArgsEscaped": true,
            "Image": "sha256:b97ed02866c9ddba00c85c41e593084a0f5670a4346bc4b96915e4bb80895c58",
            "Volumes": {
                "/etc/fdfs": {},
                "/var/fdfs": {}
            },
            "WorkingDir": "/tmp/nginx/nginx-1.12.2",
            "Entrypoint": [
                "/usr/bin/start1.sh"
            ],
            "OnBuild": null,
            "Labels": {
                "org.label-schema.schema-version": "= 1.0     org.label-schema.name=CentOS Base Image     org.label-schema.vendor=CentOS     org.label-schema.license=GPLv2     org.label-schema.build-date=20180402"
            }
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
                "22122/tcp": {},
                "23000/tcp": {},
                "8080/tcp": {},
                "8888/tcp": {}
            },
            "Tty": false,
            "OpenStdin": false,
            "StdinOnce": false,
            "Env": [
                "PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin",
                "FASTDFS_PATH=/opt/fdfs",
                "FASTDFS_BASE_PATH=/var/fdfs",
                "PORT=",
                "GROUP_NAME=",
                "TRACKER_SERVER="
            ],
            "Cmd": [
                "tracker"
            ],
            "ArgsEscaped": true,
            "Image": "sha256:b97ed02866c9ddba00c85c41e593084a0f5670a4346bc4b96915e4bb80895c58",
            "Volumes": {
                "/etc/fdfs": {},
                "/var/fdfs": {}
            },
            "WorkingDir": "/tmp/nginx/nginx-1.12.2",
            "Entrypoint": [
                "/usr/bin/start1.sh"
            ],
            "OnBuild": null,
            "Labels": {
                "org.label-schema.schema-version": "= 1.0     org.label-schema.name=CentOS Base Image     org.label-schema.vendor=CentOS     org.label-schema.license=GPLv2     org.label-schema.build-date=20180402"
            }
        },
        "Architecture": "amd64",
        "Os": "linux",
        "Size": 463877043,
        "VirtualSize": 463877043,
        "GraphDriver": {
            "Data": {
                "LowerDir": "/data/docker/overlay2/bd42247a0779abd2e922f772f50e40261b88ccbe7eb097c240e97d8961462187/diff:/data/docker/overlay2/a7ec77fc3f94275ed04a818563445f58a6a4657cb2404b42f8f6e2fcf067b1bd/diff:/data/docker/overlay2/a04629d302ee61716291217a772a2f88975947cc90980fa2e09c333e5b5e5e6a/diff:/data/docker/overlay2/f9582d126f83de6a55f11a9136ed89e084b29697f9a9297cd67cc373045c4f1d/diff:/data/docker/overlay2/0f657f0f5834027bc33b85075b79df351a3ff04a72c7a354687364f4cc2fc5ca/diff:/data/docker/overlay2/9fd23130e09e0ad16eee1d13652745c604e748f2cede04aebdbabf35b23321f4/diff:/data/docker/overlay2/3bddb8a3bd9c08804b55c302e13ededfabf87cf89df9edf833a31e8b6b220a85/diff:/data/docker/overlay2/efbcea0404693bc23c1414757c6c5b63416be2734dc4a75e03d35c537ace1466/diff:/data/docker/overlay2/19d26080644aa3046827e546faf7e6d0978a2f57798ef4feebcbea9e4f76f762/diff:/data/docker/overlay2/8b4fdaa4797867b39b75166b4a30116ee1e69d52aa816968946172b27267eec9/diff:/data/docker/overlay2/97f259962f14017c7d4fe34fe582ac9b12f558e6e67329ea4e4862f00db4b854/diff:/data/docker/overlay2/b51fd18384afedc7836664b1177d76626fbc81b172fcf8e21fd2e99291ccb269/diff:/data/docker/overlay2/f9ea8e4c0031a720a39843e1ca13dd65080bdac4c9bdffd0961e72206184752a/diff",
                "MergedDir": "/data/docker/overlay2/488da6b0d1e2dc49e27aca5ceda7a5b4db52bf426cc63657951842f0218ed28c/merged",
                "UpperDir": "/data/docker/overlay2/488da6b0d1e2dc49e27aca5ceda7a5b4db52bf426cc63657951842f0218ed28c/diff",
                "WorkDir": "/data/docker/overlay2/488da6b0d1e2dc49e27aca5ceda7a5b4db52bf426cc63657951842f0218ed28c/work"
            },
            "Name": "overlay2"
        },
        "RootFS": {
            "Type": "layers",
            "Layers": [
                "sha256:43e653f84b79ba52711b0f726ff5a7fd1162ae9df4be76ca1de8370b8bbf9bb0",
                "sha256:b53d7889c283cbbee179ece3bafa91231b850135f957f4cdaf7e77b827464431",
                "sha256:173e7c0101b800ee97d38de0fe75f5310cf28c2bb02c7f787ec4d2111ee2db0a",
                "sha256:86bb2d38a7fd4bbaafe341bba1cdecf3e3c8ac0aa3415c29f7e83c5417fc3b93",
                "sha256:c964e799140ecdfd8f651e49f9586a6a6366ec13d5a42880cc305bed26860570",
                "sha256:33e47575beaceb9c31f694d08e759af8731da14ab8c43370c6455dd5644134f8",
                "sha256:d98fa29adb0722ba012cbc570b3db641ebb498c3c1d71b7722beec96a30c74aa",
                "sha256:faa1ac83b5e393fc952f0c3dc586d7b8e40d961e3f9e8038b36b8aefad8397bc",
                "sha256:962bf3b9c052e295feb94dab10550f126d6718c69e1752f983def059cd40d3cf",
                "sha256:b4d8646fb53480262b8dfde3e254d6654c6fa8b9b2a4ea273317ef9b2e681e63",
                "sha256:7c4117be0eb549922f1739eaa64a0a5106b0e75295c68d550b0b827d9034b2bd",
                "sha256:cb1d1ef695104a32ec5835c05891c7baca566bff8bae0593ecb1b0d4a0497dc1",
                "sha256:ea8af36b77296b2f551024eeba9d724661bfe88f84ec7048493db4cb49129572",
                "sha256:882cf81bf175aff9dffa4cf3b43779dc10f3885470fa60a1c39eb791b2168f43"
            ]
        },
        "Metadata": {
            "LastTagTime": "0001-01-01T00:00:00Z"
        }
    }
]
```


### 容器部署

1.启动 tracker 调度器容器：
```bash
sudo docker run --restart=always -idt --network=host --name fastdfs-tracker -v /data/docker/fastdfs/tracker:/var/fdfs delron/fastdfs tracker
```

2.启动 storage 存储容器
```bash
sudo docker run --restart=always -itd --network=host --name fastdfs-storage -e TRACKER_SERVER=192.168.3.200:22122 -v /data/docker/fastdfs/storage:/var/fdfs delron/fastdfs storage
```

3.命令执行完成后，查看容器状态：
```bash
leazhi@ubuntu2204-001:~$ docker ps -a
CONTAINER ID   IMAGE            COMMAND                  CREATED          STATUS                    PORTS     NAMES
eb0369695515   delron/fastdfs   "/usr/bin/start1.sh …"   30 seconds ago   Up 30 seconds                       fastdfs-storage
e6b074861b94   delron/fastdfs   "/usr/bin/start1.sh …"   41 seconds ago   Up 41 seconds                       fastdfs-tracker
```










