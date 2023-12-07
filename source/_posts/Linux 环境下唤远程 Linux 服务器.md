---
title: Linux 环境下唤远程 Linux 服务器
author: leazhi
tags:
  - [ubuntu]
  - [wakeonlan]
categories:
  - [ubuntu]
date: 2023-07-22 07:37:40
cover: 
discription: 
keywords: 网络唤醒,wakeonlan, ubuntu
password: 
message: 
---

{% note success simple %}
1.想使用此功能，则目标主机的主板必须支持网络唤醒功能，且必须在 BIOS 中已启用网络唤醒。否则，下面的一切都是徒劳；
2.如果源主机和目标主机在都没有公网的情况下，那么源主机和目标主机需要在同一网段内；
{% endnote %}

## 源主机：192.168.3.254  Ubuntu 22.04

1.安装唤醒工具 wake on lan
```bash
root@ubuntuhome:~# sudo apt install -y wakeonlan
```

## 目标主机： 192.168.3.200  Kali 2023.1

1.安装 ethtool（一般系统都自动安装好了的）：

2.查看本地网卡 IP 地址以及 MAC 地址（主要是记录该主机的 MAC 地址，后面网络唤醒需要使用它进行）：
```bash
┌──(leazhi㉿localhost)-[~]
└─$ ip a
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host
       valid_lft forever preferred_lft forever
2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc mq state UP group default qlen 1000
    link/ether 3c:7c:3f:b9:66:31 brd ff:ff:ff:ff:ff:ff          
    inet 192.168.3.200/24 brd 192.168.3.255 scope global noprefixroute eth0
       valid_lft forever preferred_lft forever
    inet6 fe80::64e8:3888:4a5:46c1/64 scope link noprefixroute
       valid_lft forever preferred_lft forever
```

3.执行 `ethtool 网卡名称` 命令，查看本地网卡是否开启了网络唤醒：
```bash
┌──(leazhi㉿localhost)-[~]
└─$ sudo ethtool eth0               # 这里一定要用 root 用户运行,否则无法查看到网络唤醒状态的配置
[sudo] password for leazhi:
Settings for eth0:
        Supported ports: [  ]
        Supported link modes:   10baseT/Half 10baseT/Full
                                100baseT/Half 100baseT/Full
                                1000baseT/Full
                                2500baseT/Full
        Supported pause frame use: Symmetric
        Supports auto-negotiation: Yes
        Supported FEC modes: Not reported
        Advertised link modes:  10baseT/Half 10baseT/Full
                                100baseT/Half 100baseT/Full
                                1000baseT/Full
                                2500baseT/Full
        Advertised pause frame use: Symmetric
        Advertised auto-negotiation: Yes
        Advertised FEC modes: Not reported
        Speed: 1000Mb/s
        Duplex: Full
        Auto-negotiation: on
        Port: Twisted Pair
        PHYAD: 0
        Transceiver: internal
        MDI-X: off (auto)
        Supports Wake-on: pumbg
        Wake-on: g                                  # g 代表网络唤醒功能已开启
        Current message level: 0x00000007 (7)
                               drv probe link
        Link detected: yes
```
{% note warning simple %}
说明：如果这里的 Wake-on 为 d，则代表网络唤醒被禁用。需要使用命令: `ethtool -s 网卡名称 wakeonlan g` 进行开启
{% endnote %}


## 测试：

1.在源主机上新开一个终端或者是用其它同网段的机器先 ping 下目标主机，看是否在线（我这里是用的同网段的另一台 windows 主机 ping 的）：
```cmd
C:\Users\leazh>ping 192.168.3.200 -t

正在 Ping 192.168.3.200 具有 32 字节的数据:
来自 192.168.3.69 的回复: 无法访问目标主机。
来自 192.168.3.69 的回复: 无法访问目标主机。
来自 192.168.3.69 的回复: 无法访问目标主机。
```

2.在源主机上执行远程唤醒命令（注意：一定要在 root 用户下执行）：
```bash
root@ubuntuhome:~# wakeonlan 3c:7c:3f:b9:66:31
Sending magic packet to 255.255.255.255:9 with 3c:7c:3f:b9:66:31
```

{% note success simple %}
这里我们就可以在源主机上将网络唤醒命令写成一个小脚本，如下：
```bash
#!/usr/bin/env bash

echo -e "执行该脚本则代表网络唤醒 MAC 地址为 3c:7c:3f:b9:66:31 的同网段 IP 的主机，其 IP 地址为： 192.168.3.200" 
wakeonlan 3c:7c:3f:b9:66:31
```
{% endnote %}

3.再次 ping 目标主机 IP 地址：
```bash
C:\Users\leazh>ping 192.168.3.200 -t

正在 Ping 192.168.3.200 具有 32 字节的数据:
来自 192.168.3.69 的回复: 无法访问目标主机。
来自 192.168.3.69 的回复: 无法访问目标主机。
来自 192.168.3.69 的回复: 无法访问目标主机。
来自 192.168.3.69 的回复: 无法访问目标主机。
来自 192.168.3.69 的回复: 无法访问目标主机。
来自 192.168.3.200 的回复: 字节=32 时间=704ms TTL=64
来自 192.168.3.200 的回复: 字节=32 时间=2ms TTL=64
来自 192.168.3.200 的回复: 字节=32 时间=3ms TTL=64
来自 192.168.3.200 的回复: 字节=32 时间=2ms TTL=64
来自 192.168.3.200 的回复: 字节=32 时间=2ms TTL=64
来自 192.168.3.200 的回复: 字节=32 时间=2ms TTL=64
来自 192.168.3.200 的回复: 字节=32 时间=3ms TTL=64
```