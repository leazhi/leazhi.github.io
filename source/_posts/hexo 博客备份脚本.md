---
title: hexo 博客备份脚本
author: leazhi
tags: 
  - [hexo]
  - [shell]
categories: 
  - [hexo]
date: 2023-10-02 13:48:24
cover: 
discription: 
keywords: 
password: 
message: 
---

## 脚本内容：
```bash
#!/bin/bash

# 博客目录
blog_dir="/data/hexo/blog"

# 备份目录
backup_dir="/data/backup/hexo"

# 备份文件名格式
backup_filename=$(date +%Y%m%d_%H%M%S)

# 备份
echo "开始备份博客..."
tar -czvf ${backup_dir}/${backup_filename}.tar.gz ${blog_dir}
echo "备份成功"

# 删除7天前的备份
echo "删除7天前的备份..."
find ${backup_dir} -type f -mtime +7 -exec rm -rf {} \;
echo "删除成功"
```


## 定时执行
```bash
# 每晚2点执行一次
0 2 * * * /bin/bash /path/to/backup.sh
```