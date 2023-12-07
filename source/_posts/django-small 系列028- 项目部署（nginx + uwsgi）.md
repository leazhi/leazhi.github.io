---
title: django-small 系列028- 项目部署（nginx + uwsgi）
author: leazhi
tags:
  - [python]
  - [django]
categories:
  - [python]
  - [django]
date: 2023-10-16 20:14:01
cover: https://hexo.linuser.com/gallery/code/1181671.jpg
discription: 
keywords: 
password: 
message: 
---

##  部署前的准备

### 将开发环境配置成上线模式

之前我们一直是在开发模式下进行开发。现在项目要上线了，所以我们要将环境切换成生产环境，具体配置如下：

#### 主配置文件切换

1.复制项目同名子目录下的 settings/dev.py 为 settings/pro.py
```bash
┌──(leazhi㉿kali-desktop)-[/data/gitlab/python3-django-small_haoke/haoke/haoke]
└─$ cp settings/dev.py settings/pro.py 
```

2.编辑生产环境配置文件 pro.py , 主要修改如下几个地方：
```python
# settings/pro.py 

...

DEBUG = False
ALLOWED_HOSTS = ['*']
..

CORS_ORIGIN_WHITELIST = (
    'http://127.0.0.1:8000',
    'http://127.0.0.1:8001',
    'http://127.0.0.1:8080',
    'http://192.168.3.200:8000',
)

...
```

#### 项目 wsgi.py 的修改 

项目同名子目录下的 wsgi.py ,将配置文件指定到生产环境的配置文件：
```python
import os

from django.core.wsgi import get_wsgi_application

os.environ.setdefault('DJANGO_SETTINGS_MODULE', 'haoke.settings.pro')

application = get_wsgi_application()
```

### 将 django 项目静态文件统一生成

1.编辑项目主配置文件 settings/dev.py，在该文件中添加如下配置：
```python
STATIC_ROOT  = os.path.join(os.path.dirname(os.path.dirname(BASE_DIR)), 'front_edn_pc/static')
```

2.回到项目同名子目录下，执行命令
```bash
python manager.py collectstatic 
```

### nginx 服务安装

该部分安装省略 

### uwsgi 服务安装

执行命令
```bash
pip install uwsgi
```

## 项目部署

### uwsgi 服务配置

1.在 django 项目目录下新建  uwsgi.ini ,写入如下内容
```ini
# haoke/uwsgi.ini

# 使用 nginx 连接时使用，django 程序所在服务器地在
# 该IP 可以是服务器的IP地址
socket=192.168.3.254:8002

# 直接作 web 服务器使用；
# 这里是用做 uwsgi 与 django 通信是否注册的时候使用的。一般在 nginx 访问后端的接口报错的时候可以使用，很少用
; http=192.168.3.254:8001

# 项目目录
chdir=/data/gitlab/python3-django-small_haoke/haoke

# 项目中 uwsgi.py 文件所在目录
wsgi-file=haoke/uwsgi.py

# 进程数
processe=4

# 线程数
threads=2

# uwsgi 服务器角色
master=True

# 存放进程编号的文件
pidfile=uwsgi.pid

# 日志文件，因为 uwsgi 可以脱离终端在后台运行，日志看不见，我们以前的 runserver 是依赖终端的
# 方便查看报错信息
daemonize =uwsgi.log

# 指定依赖的虚拟环境
virtualenv=

# 最大运行内存
buffer-size=65536
```

2.启动 uwsgi 服务器，在 uwsgi.ini 目录下执行
```bash
uwsgi --ini uwsgi.ini
```

### nginx 服务配置i

修改 nginx 主配置文件，注释掉该文件中的 server 段配置，然后将在 http 段配置的最后一行添加 `include /etc/nginx/conf.d/*.conf;`

#### 创建项目前段访问配置

在nginx 新建的 conf.d 目录下创建 haoke_frontend.conf 文件，配置内容如下：
```conf
server {
    listen 8000;

    server_name _;

    location / {
        root /data/gitlab/python3-django-small_haoke/front_end_pc;
        index index.htm index.html;
        add_header Access-Control-Allow-Origin *;
        add_header 'Access-Control-Allow-Header' 'Content-Type, *';
        add_header Access-Control-Allow-Methods "GET, POST, OPTIONS";
    }

    location ~.*(js|css|png|gif|jpg|mp4|ogg)$ {
        root /data/gitlab/python3-django-small_haoke/front_end_pc
    }

    error_page 500 502 503 504 /50x.html;

    location = /50x.html {
        root html;
    }
}
```

#### 创建项目后端访问配置

在 nginx 新建的 conf.d 目录下创建 haoke_backend.conf 文件，配置内容如下：
```conf
upstream backend {
    server 192.168.3.254:8002;
}

server {
    listen 8001;

    server_name _;

    location / {
        include uwsgi_params;
        uwsgi_pass backend;
    }

    location /static/ {
        alias /data/gitlab/python3-django-small_haoke/front_end_pc/static;
    }
}
```











