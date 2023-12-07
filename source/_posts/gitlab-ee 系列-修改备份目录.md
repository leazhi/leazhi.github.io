---
title: gitlab-ee 系列-修改备份目录
author: leazhi
tags:
  - [gitlab]
categories:
  - [git]
  - [ubuntu]
date: 2023-08-22 15:42:14
cover: https://hexo.linuser.com/gallery/code/985805.png
discription: gitlab 数据备份
keywords: gitlab
password: 
message: 
---

{% note simple default %}
**gitlab 默认备份目录:**/var/opt/gitlab/backups/
{% endnote %}

1.编辑 /etc/gitlab/gitlab.rb 文件,找到 backup_path 配置项,修改备份目录    
```ruby
...

gitlab_rails['backup_path'] = "/data/gitlab/backups" 

...
```

2.创建备份目录:
```bash
sudo mkdir -p /data/gitlab/backups
```
