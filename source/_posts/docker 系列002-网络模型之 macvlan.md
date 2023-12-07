---
title: Docker 系列002-网络模型之 macvlan
author: leazhi
tags:
  - [docker]
  - [windows]
  - [linux]
  - [container]
  - [容器]
  - [虚拟化]
categories:
  - [docker]
  - [windows]
  - [linux]
date: 2023-09-08 17:28:30
cover: 
discription: docker, container, 容器, 虚拟化, macvlan
keywords: 
password: 
message: 
---

## macvlan 模型的说明
Macvlan 模型是一种将容器连接到宿主机的物理网络上的网络模型。它允许容器直接访问宿主机的网络，从而获得更好的网络性能。与 Host 模型相比，Macvlan 模型可以实现容器与宿主机网络隔离，但不支持容器之间的跨主机通信。与 Bridge 模型相比，Macvlan 模型可以避免容器无法直接访问宿主机网络的问题，但配置较为复杂。与 Overlay 模型相比，Macvlan 模型可以获得更好的网络性能，但不支持跨主机通信。

| 网络模型 | 描述 | 优点 | 缺点 | 
| --- | --- | --- | --- | 
| Bridge | 将容器连接到宿主机的桥接网络上，容器可以相互通信 | 简单易用，容器之间网络隔离 | 容器无法直接访问宿主机的网络 | 
| Host | 容器与宿主机共享网络，容器可以直接访问宿主机的网络 | 网络性能好，容器与宿主机网络隔离 | 容器之间网络隔离不彻底 | 
| Overlay | 容器连接到覆盖网络上，容器之间可以跨主机通信 | 网络隔离好，容器可以跨主机通信 | 网络性能较差 | 
| Macvlan | 容器连接到宿主机的物理网络上，容器可以直接访问宿主机的网络 | 网络性能好，容器与宿主机网络隔离 | 配置复杂，不支持跨主机通信 | 

## 实例展示

本案例在 Windows 10 pro 系统中的 Hyper-V 虚拟机中安装 Ubuntu 18.04 系统，并使用 docker 进行配置。

- OS 版本：Kali linux 2023.03
- Kernel 版本：6.4.0-kali3-amd64 #1 SMP PREEMPT_DYNAMIC Debian 6.4.11-1kali1 (2023-08-21) x86_64 GNU/Linux
- Dokcer 版本：Docker version 20.10.25+dfsg1, build b82b9f3

宿主机IP及网卡详情：
```bash
──(leazhi㉿kali-desktop)-[~]
└─$ ip a
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host noprefixroute 
       valid_lft forever preferred_lft forever
2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc mq state UP group default qlen 1000
    link/ether 3c:7c:3f:b9:66:31 brd ff:ff:ff:ff:ff:ff
    inet 192.168.3.200/24 brd 192.168.3.255 scope global dynamic noprefixroute eth0
       valid_lft 85190sec preferred_lft 85190sec
    inet6 fe80::3e7c:3fff:feb9:6631/64 scope link noprefixroute 
       valid_lft forever preferred_lft forever
3: docker0: <NO-CARRIER,BROADCAST,MULTICAST,UP> mtu 1500 qdisc noqueue state DOWN group default 
    link/ether 02:42:7c:e0:d1:5e brd ff:ff:ff:ff:ff:ff
    inet 172.17.0.1/16 brd 172.17.255.255 scope global docker0
       valid_lft forever preferred_lft forever
```

## 需求：

**为容器分配宿主机同段IP（容器需要有独立的同宿主机网段的IP），且要求能相互通信**

## 方案：
### 创建 macvlan 网络

1.执行命令，创建 macvlan 模型：
```bash
┌──(leazhi㉿kali-desktop)-[~]
└─$ sudo docker network create -d macvlan --subnet=192.168.3.0/24 --gateway=192.168.3.1 -o parent=eth0 pubnet
[sudo] leazhi 的密码：
5f893f6bd5859cd67e782cbddb72987d875d3a938ac7481ca604f332e0dab2ef
```

2.创建完成，可以查看下本机有几个网络模式：
```bash
┌──(leazhi㉿kali-desktop)-[~]
└─$ sudo docker network ls                                                                                   
NETWORK ID     NAME      DRIVER    SCOPE
c7ba5669af66   bridge    bridge    local
81975d1e4257   host      host      local
98dc51fc63f5   none      null      local
5f893f6bd585   pubnet    macvlan   local
```

3.使用 inspect 命令查看下网络的配置：
```bash
┌──(leazhi㉿kali-desktop)-[~]
└─$ sudo docker network inspect pubnet 
[
    {
        "Name": "pubnet",
        "Id": "5f893f6bd5859cd67e782cbddb72987d875d3a938ac7481ca604f332e0dab2ef",
        "Created": "2023-09-17T16:47:05.306539418+08:00",
        "Scope": "local",
        "Driver": "macvlan",
        "EnableIPv6": false,
        "IPAM": {
            "Driver": "default",
            "Options": {},
            "Config": [
                {
                    "Subnet": "192.168.3.0/24",
                    "Gateway": "192.168.3.1"
                }
            ]
        },
        "Internal": false,
        "Attachable": false,
        "Ingress": false,
        "ConfigFrom": {
            "Network": ""
        },
        "ConfigOnly": false,
        "Containers": {},
        "Options": {
            "parent": "eth0"
        },
        "Labels": {}
    }
]
```

### 使用 macvlan 网络启动容器

1.创建一个容器，并连接到 pubnet 网络：
```bash
┌──(leazhi㉿kali-desktop)-[~]
└─$ sudo docker run -id --name alpine --restart always --network=pubnet --ip=192.168.3.44 alpine /sbin/init
Unable to find image 'alpine:latest' locally
latest: Pulling from library/alpine
7264a8db6415: Pull complete 
Digest: sha256:7144f7bab3d4c2648d7e59409f15ec52a18006a128c733fcff20d3a4a54ba44a
Status: Downloaded newer image for alpine:latest
6ff31b6acfc02e2d98ece716669fce8df7d8bcab70af6bf5c038d0644878f533
```

2.查看容器的IP地址：
```bash
┌──(leazhi㉿kali-desktop)-[~]
└─$ sudo docker inspect alpine |egrep -i ddr 
            "LinkLocalIPv6Address": "",
            "SecondaryIPAddresses": null,
            "SecondaryIPv6Addresses": null,
            "GlobalIPv6Address": "",
            "IPAddress": "",
            "MacAddress": "",
                        "IPv4Address": "192.168.3.44"
                    "IPAddress": "192.168.3.44",
                    "GlobalIPv6Address": "",
                    "MacAddress": "02:42:c0:a8:03:2c",
```

3.测试从宿主机 ping 容器：
```bash
┌──(leazhi㉿kali-desktop)-[~]
└─$ sudo docker exec alpine ping 192.168.3.200
PING 192.168.3.200 (192.168.3.200): 56 data bytes
^C

```

### 添加路由（启用容器到主机通信）

1.执行命令：
```bash
# 新增一个 macvlan  类型的虚拟网卡并使用桥接模式
┌──((leazhi㉿kali-desktop)-[~]
└─$ sudo ip link add mac0 link eth0 type macvlan mode bridge
                                                                                                    # 为新增的网卡分配 IP 或者网段（同宿主机同段）
┌──(leazhi㉿kali-desktop)-[~]
└─$ sudo ip addr add 192.168.3.0/24 dev mac0                
                                                                                                    # 启用新增的虚拟网卡                   
┌──(leazhi㉿kali-desktop)-[~]
└─$ sudo ifconfig mac0 up            
```

### 测试通信

1.测试从宿主机 ping 容器:
```bash
──(leazhi㉿kali-desktop)-[~]
└─$ ping 192.168.3.44           
PING 192.168.3.44 (192.168.3.44) 56(84) bytes of data.
64 bytes from 192.168.3.44: icmp_seq=1 ttl=64 time=0.094 ms
64 bytes from 192.168.3.44: icmp_seq=2 ttl=64 time=0.078 ms
^C
--- 192.168.3.44 ping statistics ---
2 packets transmitted, 2 received, 0% packet loss, time 1012ms
rtt min/avg/max/mdev = 0.078/0.086/0.094/0.008 ms
```

2.反向，测试从容器 ping 宿主机：
```bash
┌──(leazhi㉿kali-desktop)-[~]
└─$ sudo docker exec -it alpine ping 192.168.3.200
PING 192.168.3.200 (192.168.3.200): 56 data bytes
64 bytes from 192.168.3.200: seq=0 ttl=64 time=0.029 ms
64 bytes from 192.168.3.200: seq=1 ttl=64 time=0.095 ms
64 bytes from 192.168.3.200: seq=2 ttl=64 time=0.112 ms
^C
--- 192.168.3.200 ping statistics ---
3 packets transmitted, 3 packets received, 0% packet loss
round-trip min/avg/max = 0.029/0.078/0.112 ms
```

**注意**

如果你使用了桥接网络，可能到上一步依然无法互通。此时，需要为新增的虚拟网卡 mac0 新增一条路由，如下：
```bash
┌──(leazhi㉿kali-desktop)-[~/Downloads]
└─$ sudo ip route add 192.168.3.44 dev mac0                 # 这里的 IP 是启动容器时分配的IP
```

### 永久生效

1.因为重启宿主机，就会导致上述网卡、路由配置丢失。所以，这里写一个脚本 `/usr/local/sbin/docker_macvlan.sh`，让其开机也能生效，如下：
```bash
#!/usr/bin/env bash

# 以桥接模式新增macvlan 类型的虚拟网卡
ip link add mac0 link eth0 type macvlan mode bridge

# 为新增的虚拟网卡分配IP或网段
ip addr add 192.168.3.0/24 dev mac0            

# 启用新增的网卡
# ifconfig mac0 up  
ip link set mac0 up

# 添加路由
ip route add 192.168.3.44 dev mac0   
```

2.将脚本的权限设置为 777:
```bash
sudo chmod 777 /usr/local/sbin/docker_macvlan.sh
```

3.在`/lib/systemd/system/` 目录下新建启动脚本 docker_macvlan.service，内容如下：
```bash
[Unit]
Description=Run a Custom Script at Startup
After=default.target
 
[Service]
ExecStart=/usr/local/sbin/docker_macvlan.sh
 
[Install]
WantedBy=default.target
```
4.执行命令加载脚本，然后将服务设置成随机启动即可！
```bash
sudo systemctl daemon-reload && sudo systemctl enable docker_macvlan.service
```

## 参考：
- 1.[Docker 部署的 openWrt 软路由, 并解决无法与宿主机通信问题](https://www.treesir.pub/post/n1-docker/)
- 2.[【2022.04.21】在docker中搭建openwrt软路由系统，实现多网口以及主路由功能 ](https://www.cnblogs.com/mokou/p/16173553.html)
- 3.[Docker跨主机通信之macvlan](http://dockeradv.baoshu.red/advanced_network/macvlan.html)