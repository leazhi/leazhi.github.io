---
title: mysql 5.6.40 配置文件 my.cnf
author: leazhi
tags:
  - [mysql]
categories:
  - [mysql]
date: 2023-09-25 15:05:16
cover: 
discription: 
keywords: mysql
password: 
message: 
---

```my.cnf
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

参考：
- 1.[MySQL 中 my.cnf 常用配置](https://blog.csdn.net/mshxuyi/article/details/93881939)