---
title: django-small 系列003-项目搭建及设置
author: leazhi
tags:
  - [django]
  - [python]
categories:
  - [django]
  - [python]
date: 2023-08-22 13:38:47
cover: https://hexo.linuser.com/gallery/code/1181671.jpg
discription: django, python3
keywords: django, python3
password: 
message: 
---

## 项目说明

1.项目采用前后端分离的方式;   
2.前端采用 vue3 + axios;   
3.后端采用 django3.1.10 + djangorestframework;   

## 创建项目

1.进入指定目录，打开终端命令行工具，执行以下命令创建 django 项目：
```python

# 进入指定目录：
┌──(leazhi㉿localhost)-[~]
└─$ cd ~/Desktop/python3/Django/项目/small 

# 创建 django 项目：             
┌──(leazhi㉿localhost)-[~/…/python3/Django/项目/small]
└─$ django-admin startproject haoke     
```

2.项目创建完成后，使用 linux 下的命令 tree 查看下创建的 django 目录结构：
```bash
┌──(leazhi㉿localhost)-[~/…/python3/Django/项目/small]
└─$ tree haoke 
haoke
├── haoke
│   ├── asgi.py
│   ├── __init__.py
│   ├── settings.py
│   ├── urls.py
│   └── wsgi.py
└── manage.py

2 directories, 6 files
```

{% note simple warning %}  
**注意:**
- 项目的根路径并非 small(该目录只是一个存放 django 项目的目录), 而是我们创建的项目名称目录(即 haoke ) ,且同时会在根目录下创建同名的子目录;
- 项目根目录下会创建 manage.py 文件, 该文件是 django 项目启动的入口文件;
- 项目根目录下会创建 haoke 子目录, 该目录是 django 项目的配置文件目录;      

{% endnote %}


## 环境配置

### 生产环境和开发环境的配置

1.在项目根目录下的子目录 haoke 目录下创建 settings 包,然后在该包下创建 2 个文件，分别为 dev.py（开发环境配置文件） 和 pro.py（生产环境配置文件） , 创建后的项目目录结构为：

{% note simple warning %}
**下面及后期的开发环境如果没有特殊说明,则全部使用 dev.py 的配置进行开发**
{% endnote %}
```bash
┌──(leazhi㉿localhost)-[~/…/python3/Django/项目/small]
└─$ tree haoke 
haoke
├── haoke
│   ├── asgi.py
│   ├── __init__.py
│   ├── settings
│   │   ├── dev.py
│   │   ├── __init__.py
│   │   └── pro.py
│   ├── settings.py
│   ├── urls.py
│   └── wsgi.py
└── manage.py

3 directories, 9 files
```

2.将项目根目录下的子目录 haoke 目录下settings.py 的内容复制到创建的 settings 包下的 dev.py 文件里：
```bash
┌──(leazhi㉿localhost)-[~/…/python3/Django/项目/small]
└─$ cat haoke/haoke/settings.py > haoke/haoke/settings/dev.py 
```

3.编辑项目根目录下的 manage.py 文件，将其修改为：
```python
#!/usr/bin/env python
"""Django's command-line utility for administrative tasks."""
import os
import sys


def main():
    """Run administrative tasks."""
    # 默认使用的配置是 settings.py 
    # os.environ.setdefault('DJANGO_SETTINGS_MODULE', 'haoke.settings.dev')

    # 现在，将开发环境设置成我们创建的 dev.py
    os.environ.setdefault('DJANGO_SETTINGS_MODULE', 'haoke.settings.dev')
    try:
        from django.core.management import execute_from_command_line
    except ImportError as exc:
        raise ImportError(
            "Couldn't import Django. Are you sure it's installed and "
            "available on your PYTHONPATH environment variable? Did you "
            "forget to activate a virtual environment?"
        ) from exc
    execute_from_command_line(sys.argv)


if __name__ == '__main__':
    main()
```


### 配置 ALLOWED_HOSTS:

1.编辑创建的 dev.py 文件,找到 `ALLOWED_HOSTS = [...]` 的配置,将其修改为:
```python
# haoke/haoke/settings/dev.py

...

ALLOWED_HOSTS = ['*']

...
```

### 注册 rest_framework:

1.编辑创建的 dev.py 文件, 找到 `INSTALLED_APPS` 选项, 添加 `rest_framework`:
```python
# haoke/haoke/settings/dev.py

...

INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',  
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    'rest_framework',               # 注册 rest_framework
]

...
```

### 配置与 mysql 交互
1.编辑创建的 dev.py 文件,找到 `DATABASES = [...]` 的配置,将其修改为:
```python
# haoke/haoke/settings/dev.py

...

DATABASES = {
    'default': {
        # 'ENGINE': 'django.db.backends.sqlite3',
        # 'NAME': BASE_DIR / 'db.sqlite3',
        'ENGINE': 'django.db.backends.mysql',
        'NAME': 'small',
        'HOST': '127.0.0.1',
        'PORT': '3306',
        'USER': 'root',
        'PASSWORD': 'chekir0214'
    }
}

...
```

2.登录 mysql ,创建数据库 small:
```bash
leazhi@ubuntuhome:~$ mysql -uroot -p 
Enter password: 
Welcome to the MariaDB monitor.  Commands end with ; or \g.
Your MariaDB connection id is 800
Server version: 10.10.2-MariaDB-log MariaDB Server

Copyright (c) 2000, 2018, Oracle, MariaDB Corporation Ab and others.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

(root@localhost (none) 02:00:)>create database if not exists small charset=utf8;
Query OK, 1 row affected (0.006 sec)
```

### 配置与 redis 交互

1.编辑创建的 dev.py 文件,找到 `DATABASES = [...]` 的配置,在其下面添加 redis 的相关配置,如下:
```python
# haoke/haoke/settings/dev.py

...

DATABASES = {
    'default': {
        # 'ENGINE': 'django.db.backends.sqlite3',
        # 'NAME': BASE_DIR / 'db.sqlite3',
        'ENGINE': 'django.db.backends.mysql',
        'NAME': 'small',
        'HOST': '127.0.0.1',
        'PORT': '3306',
        'USER': 'root',
        'PASSWORD': 'chekir0214'
    }
}

CACHES = {
    'default': {
        'BACKEND': 'django_redis.cache.RedisCache',
        'LOCATION': 'redis://127.0.0.1:22652/9',
        'OPTIONS': {
            'CLIENT_CLASS':'django_redis.client.DefaultClient',
            "PASSWORD": "Zmi2hU4hGkdroBl7wWm/DszVaWWqrgNuWwD2tmHO",
        }
    },
    'code': {
        'BACKEND': 'django_redis.cache.RedisCache',
        'LOCATION': 'redis://127.0.0.1:22652/10',
        'OPTIONS': {
            'CLIENT_CLASS':'django_redis.client.DefaultClient',
            "PASSWORD": "Zmi2hU4hGkdroBl7wWm/DszVaWWqrgNuWwD2tmHO",
        }
    },
}
...
```

### 配置语言:

1.默认, django 是使用的英文环境.作为一个中文用户, 肯定希望使用中文.

2.编辑创建的 dev.py 文件, 找到 `LANGUAGE_CODE` 选项, 将其值改为 zh-hans:
```python
# haoke/haoke/settings/dev.py

...

LANGUAGE_CODE = 'zh-hans'

...
```

### 配置时区:

1.默认, django 是使用的 UTC 时区.作为一个中文用户, 肯定希望使用北京时间.

2.编辑创建的 dev.py 文件, 找到 `TIME_ZONE` 选项, 将其值改为 `Asia/Shanghai`:
```python
# haoke/haoke/settings/dev.py

...

TIME_ZONE = 'Asia/Shanghai'

...
```

### 配置 logs

1.编辑创建的 dev.py 文件,在文件的最后添加如下内容:
```python
# haoke/haoke/settings/dev.py

from pathlib import Path
import os                   # 注意：这里需要导入 OS 模块，因为日志文件需要写入到指定的目录
...

LOGGING = {
    'version': 1,
    # 是否禁用已经存在的日志器
    'disable_existing_loggers': False,    
    # 日志信息显示的格式  
    'formatters': {
        'verbose': {
            'format': '%(levelname)s %(asctime)s %(module)s %(lineno)d %(message)s'
        },
        'simple': {
            'format': '%(levelname)s %(module)s %(lineno)d %(message)s'
        },
    },
    # 对日志进行过滤
    'filters': {
        # django 在 debug 模式下才输出日志
        'require_debug_true': {
            '()': 'django.utils.log.RequireDebugTrue',
        },
    },
    # 日志处理方法
    'handlers': {
        # 向终端输出日志
        'console': {
            'level': 'DEBUG',
            'filters': ['require_debug_true'],
            'class': 'logging.StreamHandler',
            'formatter': 'simple'
        },
        # 向文件中输出日志
        'file': {
            'level': 'INFO',
            'class': 'logging.handlers.RotatingFileHandler',
            'filename': os.path.join(BASE_DIR, 'logs/haoke.log'),     # 日志输出文件
            'maxBytes': 300 * 1024 * 1024,
            'backupCount': 10,
            'formatter': 'verbose'
        },
    },
    # 日志器
    'loggers': {
        # 定义一个名为 django 的日志器
        'django': {
            # 可以同时向文件和终端输出日志
            'handlers': ['console', 'file'],
            # 是否继续传递日志信息
            'propagate': True,
            # 日志器接受的最低日志级别
            'level': 'INFO',
        },
    }
}
```

2.在项目子目录 haoke（非根目录）下创建 logs文件夹，用于存放日志文件:
```bash
┌──(leazhi㉿localhost)-[~/…/python3/Django/项目/small]
└─$ mkdir haoke/haoke/logs    
```




