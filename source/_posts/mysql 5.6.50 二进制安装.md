---
title: mysql 5.6.50 二进制安装
author: leazhi
tags:
  - [mysql]
categories:
  - [mysql]
date: 2023-09-25 15:28:20
cover: 
discription: 
keywords: mysql
password: 
message: 
---

## 系统环境
- OS：CentOS-7-x86_64-Minimal-1908
- Kernal：3.10.0-1062.el7.x86_64 #1 SMP Wed Aug 7 18:08:02 UTC 2019 x86_64 x86_64 x86_64 GNU/Linux
- CPU：1
- Memory：2G
- Disk：40G


## MySQL 5.6.50 安装配置

### 基础环境

1.创建运行 mysql 用户 mysql:
```bash
sudo useradd -s /sbin/nologin mysql
```

2.创建 mysql 数据存储目录和日志存储目录：
```bash
sudo mkdir -p /data/mysql /var/log/mysql
```

### 安装 mysql

1.从 mysql 官方下载 mysql-5.6.50 二进制包到 /usr/local/src/ 目录下：
```bash
sudo wget -O /usr/local/src/mysql-5.6.50-linux-glibc2.12-x86_64.tar.gz https://cdn.mysql.com/archives/mysql-5.6/mysql-5.6.50-linux-glibc2.12-x86_64.tar.gz
```

2.将下载好的二进制文件解压到下载目录：
```bash
tar -zxf /usr/local/src/mysql-5.6.50-linux-glibc2.12-x86_64.tar.gz -C /usr/local/src/
```

3.将解压出来的目录移动到 /usr/local/ 目录下并重命名为 mysql:
```bash
sudo mv /usr/local/src/mysql-5.6.50-linux-glibc2.12-x86_64 /usr/local/mysql
```

4.进入到 /usr/local/mysql 目录：
```bash
cd /usr/local/mysql
```

5.将 mysql 安装目录，日志存放目录和数据存放目录的所属主和组都修改 mysql:
```bash
sudo chown -R mysql:mysql /usr/local/mysql /var/log/mysql /data/mysql
```

6.执行 `./scripts/mysql_install_db --datadir=${datadir} --user=mysql --skip-name-resolve` 命令初始化 mysql:
```bash
sudo ./scripts/mysql_install_db --datadir=/data/mysql --user=mysql --skip-name-resolve
```

### 配置 mysql

1.编辑 /etc/my.cnf 文件，写入以下内容：
```bash
[mysqld]
# 监听地址
bind-address = 0.0.0.0

# 监听端口
port = 3306

# pid 文件
pid-file =  /data/mysql/mysql.pid

#  程序安装目录
basedir =  /usr/local/mysql/

# 数据存放目录
datadir = /data/mysql 

# 该条配置需在[client]段同时配置
socket=/tmp/mysql.sock  

# 设定默认字符为utf8mb4
character-set-server=utf8mb4   

# 多客户访问同一数据库，该选项默认开启
symbolic-link = 0 

# 此目录被 MySQL用来保存临时文件
#tmpdir =  /usr/local/mysql/tmp/ 

# 打开时，和max_connections对比，取大数
open_files_limit = 65535 

# 错误日志
log-error = /var/log/mysql/error.log

# 开启慢查询日志相关
slow_query_log=on   

# 默认10秒
long_query_time=2   

# 慢查询日志路径
slow_query_log_file = /var/log/mysql/slow_query.log  

# 记录没有使用索引的sql
log-queries-not-using-indexes = 1   
 
# 指定默认的存储引擎
default_storage_engine=InnoDB

# InnoDB为独立表空间模式，每个数据库的每个表都会生成一个数据空间
innodb_file_per_table = on     

# 生产中要改，建议为操作系统内存的70%-80%，需重启服务生效
innodb_buffer_pool_size=4G 

# 忽略主机名解析，提高访问速度（注意配置文件中使用主机名将不能解析）    
skip_name_resolve = on        

# 忽略表单大小写
lower_case_table_names = 1     

##  查询缓存相关
# 0表示禁用缓存，1表示会缓存所有的结果，2表示只缓存SQL_CACHE缓存
query_cache_type=1  
query_cache_limit = 2M
query_cache_size = 64M  # 或32M 或128M 

##  系统资源相关
# 如果系统在一个短时间内有很多连接，则需要增大该参数的值小于512
back_log = 500  

# 默认100,生产则需要增大该参数值,最大连接数16384
max_connections = 1000 

# 二进制日志相关
server_id=1                    
log_bin=mysql-bin
#log-bin-index=master-bin.index
expire_logs_days = 7
#binlog_format = row    #默认为mix，新版中设为这两项可提高安全性
#binlog_row_image = minimal
max_binlog_size = 100m   #默认是1G
binlog_cache_size = 4m
#binlog-do-db = DBNAME #指定mysql的binlog日志只记录哪个库
max_binlog_cache_size = 512m  #生产4g
#skip-slave-start


[mysqldump]
quick

[mysql]
# 允许通过 TAB 键提示
auto-rehash

#  数据库字符集
default-character-set = utf8mb4
  
connect-timeout = 3


[client]
default-character-set = utf8mb4
port = 3306
socket = /tmp/mysql.sock

[mysqld_safe]
# 增加每个进程的可打开文件数量.确认你已经将全系统限制设定的足够高!打开大量表需要将此值设大
open-files-limit = 8192
```

2.在 `/etc/profile.d/` 目录下创建 mysql.sh 文件，配置 mysql 系统环境：
```bash
sudo echo 'PATH=$PATH:/usr/local/mysql/bin' > /etc/profile.d/mysql.sh
```

3.执行命令 `source /etc/profile.d/mysql.sh` 使其立即生效：
```bash
source /etc/profile.d/mysql.sh
```

4.执行命令 `mysql --version` 看能否正常输入 Mysql 版本，如果能，则说明系统环境配置没问题：
```bash
[root@localhost mysql]$ mysql --version
mysql  Ver 14.14 Distrib 5.6.50, for linux-glibc2.12 (x86_64) using  EditLine wrapper
```

### 启动 mysql
1.在 /lib/systemd/system/ 目录下创建 mysql 启动脚本文件 mysql.service ，内容如下：
```bash
[Unit]
Description=MySQL Server
Documentation=man:mysqld(8)
Documentation=http://dev.mysql.com/doc/refman/en/using-systemd.html
After=network.target

[Service]
ExecStart=/usr/local/mysql/bin/mysqld_safe --datadir=/data/mysql
User=mysql
Group=mysql
TimeoutSec=300
#PrivateTmp=true

[Install]
WantedBy=multi-user.target
```

2.执行命令加载脚本：
```
sudo systemctl daemon-reload
```

3.启动 mysql 服务并将其设置开机启动：
```bash
sudo systemctl enable --now mysql.service
```

4.查看启动状态：
```bash
[root@localhost mysql]# systemctl status mysql.service
● mysql.service - MySQL Server
   Loaded: loaded (/usr/lib/systemd/system/mysql.service; disabled; vendor preset: disabled)
   Active: active (running) since Mon 2023-09-25 03:27:10 EDT; 19min ago
     Docs: man:mysqld(8)
           http://dev.mysql.com/doc/refman/en/using-systemd.html
 Main PID: 26940 (mysqld_safe)
   CGroup: /system.slice/mysql.service
           ├─26940 /bin/sh /usr/local/mysql/bin/mysqld_safe --datadir=/data/mysql
           └─27409 /usr/local/mysql/bin/mysqld --basedir=/usr/local/mysql/ --datadir=/data/mysql --plugin-dir=/usr/local/mysql//lib/plugin --log-error=/var...

Sep 25 03:27:10 localhost.localdomain systemd[1]: Stopped MySQL Server.
Sep 25 03:27:10 localhost.localdomain systemd[1]: Started MySQL Server.
Sep 25 03:27:10 localhost.localdomain mysqld_safe[26940]: 230925 03:27:10 mysqld_safe Logging to '/var/log/mysql/error.log'.
Sep 25 03:27:10 localhost.localdomain mysqld_safe[26940]: 230925 03:27:10 mysqld_safe Starting mysqld daemon with databases from /data/mysql
```


















