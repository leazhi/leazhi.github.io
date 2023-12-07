---
title: ubuntu 22.04 服务部署-DNS（bind9）单机部署
author: leazhi
tags:
  - [ubuntu]
  - [dns]
  - [bind9]
categories:
  - [ubuntu]
  - [dns]
  - [bind9]
date: 2023-09-03 10:30:50
cover: 
discription: 
keywords: 
password: 
message: 
---

## 系统环境
- 系统环境：ubuntu 22.04.3 LTS (Jammy Jellyfish)
- 内核版本：5.15.0-82-generic #91-Ubuntu SMP Mon Aug 14 14:14:14 UTC 2023 x86_64 x86_64 x86_64 GNU/Linux
- 软件版本：BIND 9.18.12-0ubuntu0.22.04.2-Ubuntu

## 服务部署

### 安装 bind9 及主配置文件修改

1.执行安装命令
```shell
sudo apt install bind9 bind9utils bind9-doc bind9-dnsutils
```

2.修改配置文件 `/etc/bind/named.conf.options`, 如下：
```shell
options {
	directory "/var/cache/bind";

	// If there is a firewall between you and nameservers you want
	// to talk to, you may need to fix the firewall to allow multiple
	// ports to talk.  See http://www.korg/vuls/id/800113

	// If your ISP provided one or more IP addresses for stable 
	// nameservers, you probably want to use them as forwarders.  
	// Uncomment the following block, and insert the addresses replacing 
	// the all-0's placeholder.

    // DNS 转发器，用于设定该 DNS 解析服务器无法进行当前域名解析的情况下，进行转发解析的 DNS 地址
	 forwarders {
	 	114.114.114.114;
 		8.8.8.8;
	 };

	//========================================================================
	// If BIND logs error messages about the root key being expired,
	// you will need to update your keys.  See https://www.isc.org/bind-keys
	//========================================================================
	dnssec-validation auto;
	auth-nxdomain no;

	// listen-on-v6 { any; };
};
```

3.修改 `/etc/default/named` 文件，在 `OPTIONS` 后面添加 `-4` 参数，如下：
```bash
#
# run resolvconf?
RESOLVCONF=no

# startup options for the server
OPTIONS="-u bind -4"
```

### 添加域名解析

1.修改配置文件 `/etc/bind/named.conf.local`, 如下：
```shell
//
// Do any local configuration here
//

// Consider adding the 1918 zones here, if they are not used in your
// organization
//include "/etc/bind/zones.rfc1918";

// 正向解析
zone "customer.com" {
        type master;
        file "/etc/bind/zones/db.customer.com";
};

// 反向解析
zone "168.192.in-addr.arpa" {
        type master;
        file "/etc/bind/zones/db.168.192";
};
```

2.在 `/etc/bind/` 目录下创建域名解析文件存放目录 zones：
```shell
sudo mkdir /etc/bind/zones
```
    
3.在 /etc/bind/zones 目录下创建 `db.customer.com` 和 `db.168.192` 文件,内容分别为：

3.1.正向解析文件：`db.customer.com` 配置内容如下：
```shell
;
; BIND data file for local loopback interface
;
$TTL    604800
@       IN      SOA     customer.com. root.customer.com. (  
                              2         ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
;
@       IN      NS      localhost.
@       IN      A       127.0.0.1
;@      IN      AAAA    ::1
dhcp    IN      A       102.168.1.201
ntp     IN      A       192.168.1.203
ftp     IN      A       192.168.1.204
dns     IN      A       192.168.1.202
```

3.2.反向解析文件：`db.168.192` 配置内容如下：
```shell
;
; BIND reverse data file for local loopback interface       
;
$TTL    604800
@       IN      SOA     customer.com. root.customer.com. (  
                              1         ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
;
@       IN      NS      localhost.
1.0.0   IN      PTR     localhost.
201.1     IN      PTR     dhcp
203.1     IN      PTR     ntp
204.1     IN      PTR     ftp
202.1     IN      PTR     dns
```

### 检查配置及启动服务
1.执行命令，检查正向解析和反向解析 配置文件是否正确：
```shell
# 主配置文件检查
leazhi@ubuntu2204-001:~$ sudo named-checkconf /etc/bind/named.conf

# 正向解析文件配置检查
leazhi@ubuntu2204-001:~$ sudo named-checkzone customer.com /etc/bind/zones/db.customer.com 
zone customer.com/IN: loaded serial 2
OK

# 反向解析文件配置检查
leazhi@ubuntu2204-001:~$ sudo named-checkzone 168.192.in-addr.arpa /etc/bind/zones/db.customer.com 
zone 168.192.in-addr.arpa/IN: loaded serial 2
OK
```

2.启动bind服务：
```shell
sudo systemctl enable --now named.service
```

3.检查bind服务是否启动：
```shell
leazhi@ubuntu2204-001:~$ sudo systemctl status named.service
● named.service - BIND Domain Name Server
     Loaded: loaded (/lib/systemd/system/named.service; enabled; vendor preset: enabled)
     Active: active (running) since Sun 2023-09-03 02:12:12 UTC; 37min ago
       Docs: man:named(8)
    Process: 15851 ExecStart=/usr/sbin/named $OPTIONS (code=exited, status=0/SUCCESS)
   Main PID: 15852 (named)
      Tasks: 4 (limit: 935)
     Memory: 4.3M
        CPU: 188ms
     CGroup: /system.slice/named.service
             └─15852 /usr/sbin/named -u bind -4
```

## 解析测试

1.编辑本地的 /etc/resolv.conf 文件，添加如下内容：
```shell
nameserver 127.0.0.1
```

2.测试解析：

2.1.nslookup 命令测试：
```shell
# 正向解析测试：
leazhi@ubuntu2204-001:~$ nslookup ftp.customer.com
Server:		127.0.0.1
Address:	127.0.0.1#53

Name:	ftp.customer.com
Address: 192.168.1.204


# 反向解析测试：
leazhi@ubuntu2204-001:~$ nslookup 192.168.1.204
204.1.168.192.in-addr.arpa	name = ftp.168.192.in-addr.arpa.
```

2.2.dig 命令测试：
```shell
# 正向解析测试：
leazhi@ubuntu2204-001:~$ dig ntp.customer.com

; <<>> DiG 9.18.12-0ubuntu0.22.04.2-Ubuntu <<>> ntp.customer.com
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 29507
;; flags: qr aa rd ra; QUERY: 1, ANSWER: 1, AUTHORITY: 0, ADDITIONAL: 1

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 1232
; COOKIE: 96fcfa9c323165c90100000064f3fc2ffa9a4d1f0d01e293 (good)
;; QUESTION SECTION:
;ntp.customer.com.		IN	A

;; ANSWER SECTION:
ntp.customer.com.	604800	IN	A	192.168.1.203

;; Query time: 0 msec
;; SERVER: 127.0.0.1#53(127.0.0.1) (UDP)
;; WHEN: Sun Sep 03 03:23:27 UTC 2023
;; MSG SIZE  rcvd: 89


# 反向解析测试：
leazhi@ubuntu2204-001:~$ dig -x 192.168.1.203

; <<>> DiG 9.18.12-0ubuntu0.22.04.2-Ubuntu <<>> -x 192.168.1.203
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 17870
;; flags: qr aa rd ra; QUERY: 1, ANSWER: 1, AUTHORITY: 0, ADDITIONAL: 1

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 1232
; COOKIE: 2e3d235566ef03400100000064f3fc374bbe3a1eac2f0324 (good)
;; QUESTION SECTION:
;203.1.168.192.in-addr.arpa.	IN	PTR

;; ANSWER SECTION:
203.1.168.192.in-addr.arpa. 604800 IN	PTR	ntp.168.192.in-addr.arpa.

;; Query time: 0 msec
;; SERVER: 127.0.0.1#53(127.0.0.1) (UDP)
;; WHEN: Sun Sep 03 03:23:35 UTC 2023
;; MSG SIZE  rcvd: 121
```

