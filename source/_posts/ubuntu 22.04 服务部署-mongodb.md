---
title: ubuntu 22.04 服务部署-mongodb
author: leazhi
tags:
  - [ubuntu]
  - [mongodb]
categories:
  - [ubuntu]
  - [mongodb]
date: 2023-10-06 14:15:53
cover: 
discription: 
keywords: MongoDB
password: 
message: 
---

## 系统环境：
- OS：Ubuntu 22.04.3 LTS
- Kernel：5.15.0-84-generic #93-Ubuntu SMP Tue Sep 5 17:16:10 UTC 2023 x86_64 x86_64 x86_64 GNU/Linux

- MongoDB：v6.0.10

## 部署 MongoDB

1.从 [MongoDB 官方站点](https://www.mongodb.com/try/download/community) 下载与系统对应的免费的社区版本包到服务器指定目录：
```bash
sudo wget -O /usr/local/src/mongodb-org-server_6.0.10_amd64.deb https://repo.mongodb.org/apt/ubuntu/dists/jammy/mongodb-org/6.0/multiverse/binary-amd64/mongodb-org-server_6.0.10_amd64.deb

```

2.安装：
```bash
sudo dpkg -i /usr/local/src/mongodb-org-server_6.0.10_amd64.deb
```

3.创建数据和日志存放目录
```bash
sudo mkdir -p /data/mongodb/{data, logs}
```

4.将 /data/mongodb 目录的拥有者改为 mongodb 用户
```bash
sudo chown -R mongodb:mongodb /data/mongodb
```

5.修改mongodb配置文件，主要是指定数据存放路径和日志存放路径，如下：
```bash
root@ubuntu2204:~# egrep -v '^$|^#' /etc/mongod.conf
storage:
  dbPath: /data/mongodb/data
systemLog:
  destination: file
  logAppend: true
  path: /data/mongodb/logs/mongod.log
net:
  port: 27017
  bindIp: 127.0.0.1
processManagement:
  timeZoneInfo: /usr/share/zoneinfo
```

6.启动 mongodb:
```bash
sudo systemctl restart mongod.service

```

7.查看 mongodb 状态
```bash
root@ubuntu2204:~# systemctl status mongod.service
● mongod.service - MongoDB Database Server
     Loaded: loaded (/lib/systemd/system/mongod.service; disabled; vendor preset: enabled)
     Active: active (running) since Fri 2023-10-06 13:54:02 UTC; 3min 20s ago
       Docs: https://docs.mongodb.org/manual
   Main PID: 1977 (mongod)
     Memory: 68.2M
        CPU: 960ms
     CGroup: /system.slice/mongod.service
             └─1977 /usr/bin/mongod --config /etc/mongod.conf

Oct 06 13:54:02 ubuntu2204 systemd[1]: Started MongoDB Database Server.
Oct 06 13:54:02 ubuntu2204 mongod[1977]: {"t":{"$date":"2023-10-06T13:54:02.262Z"},"s":"I",  "c":"CONTROL",  "id":7484500, "ctx":"-","msg":"Environment variable MONGODB_CONFIG_O>
lines 1-12/12 (END)
```