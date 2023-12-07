---
title: PG 系列-备份脚本
author: leazhi
tags:
  - [postgresql]
  - [pg]
  - [database]
  - [db]
categories:
  - [postgresql]
cover:  
discription: 
keywords: 
password: 
message: 
---

一个 PostgreSQL 数据库备份脚本，该脚本将只保留最近7天的备份文件：
```base
#!/bin/bash

# 设置备份目录和文件名
backup_dir="/data/backup/pg_data"
backup_file="db_backup_$(date +%Y%m%d).sql"

# 如果目录不存在，则创建目录：
if [ ! -f "$backup_dir" ]; then
  mkdir -p $backup_dir
fi

# 执行备份命令
pg_dump -U your_username -d your_database > "$backup_dir/$backup_file"

# 删除超过7天的备份文件
find "$backup_dir" -name "db_backup_*" -type f -mtime +7 -delete
```