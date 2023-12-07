---
title: 利用 openssl 创建 CA 、Server 及 Client 证书，并导出 p12 格式
author: leazhi
tags:
  - [ssl]
  - [openssl]
  - [libssl]
  - [ca]
categories:
  - [ssl]
date: 2023-09-15 19:52:49
cover: 
discription: 
keywords: 
password: 
message: 
---

## 部署准备

1.在服务器上安装 openssl：
```bash
sudo apt install -y openssl libssl-dev openssl-dev
```

2.在 /data/ 目录下创建 ssl/{ca, server, client} 目录：
```bash
sudo mkdir -p /data/ssl/{ca, server, client}
```

3.进入创建的 /dta/ssl 目录：
```bash
cd  /data/ssl
```

## CA 证书：
1.创建根证书颁发机构（Root CA）：
```bash
root@5f25b0f636e5:/data/ssl# openssl req -newkey rsa:2048 -nodes -keyout ca/ca-key.pem -x509 -days 3650 -out ca/ca-cert.pem 
.+.+........+.+++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++*..+............+......+..+...+..........+........+.+++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++*..+...............+..+.+..+.............+.....+.........+.+..+...+.+......+..+.+......+.....+...+...+...+.+...+........+....+......+.....+.......+............+...+...............+.....+...+.........+....+.....+.+.....+.+..+......+.......+..+.............+...+............+..+.+..+....+...........+...+.........+.+...........+.+...........................+...+........+.+...........+.......+..+.+............+...........+..........+.....+.........+............+...+.......+...+.....+................+...........+.+.....+....+.....+................+.....+...+.......+...+...........+......+....+.....+...+............................+.................+.+.........+......+.....+......+..........+.....+....+..+....+..........................+...+++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++
...+++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++*..+...........+.......+..+.+..............+.+......+.........+..+...+.........+............+++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++*..............+..............+....+..+......+..........+.........+........+......+............+.............+...........+.+.........+...+...+........+..........+..............+...+....+.....+...+..........+...+..+.+..+.......+............+......+...+.....+.+...........+...+...................+..+.+..+...+.......+..+....+.....+.+......+..+.+......+...............+........+...+............+...+...+.+.........+..+.........+...+...+....+...+........+..........+...+...........+.........+......+...+...+...+......+.+........+.+...+..+.+..............+.+...+..+.......+..+...+......+....+........+....+...+......+.....+.+..............+.+........+...+....+...+..+.+...+............+...+........................+...+...+.........+......+...........+.+..+...+..................+....+...........+++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++
-----
You are about to be asked to enter information that will be incorporated
into your certificate request.
What you are about to enter is what is called a Distinguished Name or a DN.
There are quite a few fields but you can leave some blank
For some fields there will be a default value,
If you enter '.', the field will be left blank.
-----
Country Name (2 letter code) [AU]:CN
State or Province Name (full name) [Some-State]:Hubei
Locality Name (eg, city) []:Xianning
Organization Name (eg, company) [Internet Widgits Pty Ltd]:Fuwei
Organizational Unit Name (eg, section) []:IT
Common Name (e.g. server FQDN or YOUR name) []:fuwei.com
Email Address []:admin@fuwei.com
``` 

如果不想交互 ，则直接在后面加参数：`-subj "/C=CN/ST=Hubei/L=Xianning/O=Fuwei/OU=IT/CN=Fuwei.com/emailAddress=admin@fuwei.com"`, 即：
```bash
root@5f25b0f636e5:/data/ssl# openssl req -newkey rsa:2048 -nodes -keyout ca/ca-key.pem -x509 -days 3650 -out ca/ca-cert.pem  -subj "/C=CN/ST=Hubei/L=Xianning/O=Fuwei/OU=IT/CN=Fuwei.com/emailAddress=admin@fuwei.com"
.....+++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++*....+..+++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++*..+.......+...+..+.+...+..+++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++
....+++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++*....+....+........+++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++*.......+......+.........+.+........+.......+...+..+...............+.+...+.........+..+...+......+....+.....+......+....+..+....+............+..+...+....+.....+.+.........+.........+..+............+.+.........+..+......+..........+........+.+...+..+...+......+............+...+......+.+..+...+......................+......+...........+...+...+....+...........+...+....+............+++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++
-----
```
**参数说明**

- req：证书请求的子命令
- -newkey rsa:2048 -keyout ca/ca-key.pem：表示生成私钥(PKCS8格式)
- -nodes：表示私钥不加密，若不带参数将提示输入密码
- -x509 ：直接输出证书
- -days3650：为证书有效期
- -subj：取消交互，执行自动输入；
- C：代表国家
- ST：省份
- L：城市
- O：公司
- OU：部门
- CN：产品名（计算机名，域名或者IP 地址）
- emailAddress：邮箱
 
## Server 证书：

1.创建服务器证书签名请求（CSR 文件）： 
```bash
root@5f25b0f636e5:/data/ssl# openssl req -newkey rsa:2048 -nodes -keyout server/server-key.pem -out server/server-req.csr 
........+...+......+.......+.........+...+..+...+......+++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++*...+...+...+....+.....+......+..........+.....+...+......+...+++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++*.+.+........+...+....+......+....................+............+.......+++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++
....+.....+...+...+.......+........+...+............+.......+..+.........+.......+...+.....+....+...........+..........+.........+++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++*.+....+.....+++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++*.+...............+......+.................+....+......+.........+......+...+............+..............+...............+....+..+............+...+....+.....+...+...+...+.+......+++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++
-----
You are about to be asked to enter information that will be incorporated
into your certificate request.
What you are about to enter is what is called a Distinguished Name or a DN.
There are quite a few fields but you can leave some blank
For some fields there will be a default value,
If you enter '.', the field will be left blank.
-----
Country Name (2 letter code) [AU]:CN
State or Province Name (full name) [Some-State]:Hubei
Locality Name (eg, city) []:Xianning
Organization Name (eg, company) [Internet Widgits Pty Ltd]:Fuwei
Organizational Unit Name (eg, section) []:IT
Common Name (e.g. server FQDN or YOUR name) []:server.fuwei.com
Email Address []:server@fuwei.com

Please enter the following 'extra' attributes
to be sent with your certificate request
A challenge password []:
An optional company name []:
```

不想交互的话就加: `--subj 参数指定`
```bash
root@5f25b0f636e5:/data/ssl# openssl req -newkey rsa:2048 -nodes -keyout server/server-key.pem -out server/server-req.csr  -subj "/C=CN/ST=Hubei/L=Xianning/O=Fuwei/OU=IT/CN=server.fuwei.com/emailAddress=server@fuwei.com"
..+...................+..+...+...+......................+.......................+......................+............+++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++*...+.....+......+.+++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++*...............+..+....+......+...+.........+++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++
......+++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++*....+...+............+..........+++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++*.....+......+...+......+.+..................+...+.....+...+....+.....+++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++
-----
```

2.使用根证书颁发机构（Root CA）签署服务器证书： 
```bash
root@5f25b0f636e5:/data/ssl# openssl x509 -req -in server/server-req.csr -out server/server-cert.pem -CA ca/ca-cert.pem -CAkey ca/ca-key.pem -CAcreateserial -days 3650 
Certificate request self-signature ok
subject=C = CN, ST = Hubei, L = Xianning, O = Fuwei, OU = IT, CN = server.fuwei.com, emailAddress = server@fuwei.com
```

## Client 证书：
1.创建客户端证书签名请求（CSR 文件）： 
```bash
root@5f25b0f636e5:/data/ssl# openssl req -newkey rsa:2048 -nodes -keyout client/client-key.pem -out client/client-req.csr 
..+.+++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++*....+.....+++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++*........+......+.........+.+......+.....+.+..+...+.........+...+...+.............+............+.....+.........+.............+...+........+....+..+...+.+......+.....+.+.....+....+.....+......+....+.....+.+...+.....+..........+..+............+.+......+........+.....................+.+...........+....+...........+..........+.....+.......+......+......+.........+.....+.........+.+..+.............+...........+...+.+.....+.+...+..+......+...+.+++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++
.+........+.......+...+......+...+..+....+......+...+...+.....+...+.+......+...+.....+..........+.....+++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++*.....+...+...+.....+.+.....+...................+..+....+...........+....+...+...............+.....+.+..+.......+++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++*.+.+++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++
-----
You are about to be asked to enter information that will be incorporated
into your certificate request.
What you are about to enter is what is called a Distinguished Name or a DN.
There are quite a few fields but you can leave some blank
For some fields there will be a default value,
If you enter '.', the field will be left blank.
-----
Country Name (2 letter code) [AU]:CN
State or Province Name (full name) [Some-State]:Hubei
Locality Name (eg, city) []:Xianning
Organization Name (eg, company) [Internet Widgits Pty Ltd]:Fuwei
Organizational Unit Name (eg, section) []:IT
Common Name (e.g. server FQDN or YOUR name) []:client.fuwei.com
Email Address []:client@fuwei.com

Please enter the following 'extra' attributes
to be sent with your certificate request
A challenge password []:
An optional company name []:
```

不想交互的话就加: `--subj 参数指定`
```bash
root@5f25b0f636e5:/data/ssl# openssl req -newkey rsa:2048 -nodes -keyout client/client-key.pem -out client/client-req.csr -subj "/C=CN/ST=Hubei/L=Xianning/O=Fuwei/OU=IT/CN=client.fuwei.com/emailAddress=client@fuwei.com"
.....+..+++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++*................+.+............+..+...+++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++*.......+++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++
.........+.....+.......+..+.+..+......+.+.....+.+.....+..........+...+..+.......+.....+...+....+...+........+.......+...+++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++*.+..+.+..+.......+.....+.+..+....+...+...+.....+..........+........+++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++*...........+..+.+..+.+......+...+.....+.............+...+.....+.+...+......+......+++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++
-----
```

2.使用根证书颁发机构（Root CA）签署客户端证书： 
```bash
root@5f25b0f636e5:/data/ssl# openssl x509 -req -in client/client-req.csr -out client/client-cert.pem -CA ca/ca-cert.pem -CAkey ca/ca-key.pem -CAcreateserial -days 3650 
Certificate request self-signature ok
subject=C = CN, ST = Hubei, L = Xianning, O = Fuwei, OU = IT, CN = client.fuwei.com, emailAddress = client@fuwei.com
```

3.导出客户端证书为 P12 格式： 
```bash
root@5f25b0f636e5:/data/ssl# openssl pkcs12 -export -in client/client-cert.pem -inkey client/client-key.pem -out client/client.p12 
Enter Export Password:                                          # 密码为空
Verifying - Enter Export Password:
```

**最后，生成的证书目录结构及文件**
```bash
root@5f25b0f636e5:/data/ssl# tree .
.
|-- ca
|   |-- ca-cert.pem
|   `-- ca-key.pem
|-- client
|   |-- client-cert.pem
|   |-- client-key.pem
|   |-- client-req.csr
|   `-- client.p12
`-- server
    |-- server-cert.pem
    |-- server-key.pem
    `-- server-req.csr

3 directories, 9 files
```

## 参考
- 1.[OpenSSL创建生成CA证书、服务器、客户端证书及密钥](https://blog.csdn.net/qq153471503/article/details/109524764)
- 2.[基于OpenSSL创建证书](https://www.aflyingfish.top/articles/d9a29d96e0ce/)
- 3.[linux系统openssl证书生成，ca, server和client](https://blog.csdn.net/u012603457/article/details/97265744)
- 4.[OpenSSL生成根证书CA及签发子证书](https://developer.aliyun.com/article/521651)
- 5.[在Ubuntu下使用OpenSSL搭建CA ](https://www.cnblogs.com/blingblingXY/p/13875510.html)