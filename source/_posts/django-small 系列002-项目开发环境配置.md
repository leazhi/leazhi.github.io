---
title: django-small 系列002-项目开发环境配置
author: leazhi
tags:
  - [django]
  - [python]
categories:
  - [django]
  - [python]
date: 2023-08-22 14:58:52
cover: https://hexo.linuser.com/gallery/code/1181671.jpg
discription: django, python3
keywords: django, python3
password: 
message: 
---

## 创建 python 虚拟开发环境

由于我们是在 Linux 环境下开发，而 Linux 系统一般都侑 python2 和 python3 两个 python 环境，所以,我们在创建 python 开发环境时需要使用参数 -p 指定 python 版本，否则可能会出现版本不兼容的问题。

执行下面的命令创建虚拟环境:
```bash
leazhi@ubuntuhome:~$ mkvirtualenv -p python3 haoke_small
created virtual environment CPython3.10.12.final.0-64 in 342ms
  creator CPython3Posix(dest=/home/leazhi/.env/haoke_small, clear=False, no_vcs_ignore=False, global=False)
  seeder FromAppData(download=False, pip=bundle, setuptools=bundle, wheel=bundle, via=copy, app_data_dir=/home/leazhi/.local/share/virtualenv)
    added seed packages: pip==23.2.1, setuptools==68.0.0, wheel==0.41.1
  activators BashActivator,CShellActivator,FishActivator,NushellActivator,PowerShellActivator,PythonActivator
virtualenvwrapper.user_scripts creating /home/leazhi/.env/haoke_small/bin/predeactivate
virtualenvwrapper.user_scripts creating /home/leazhi/.env/haoke_small/bin/postdeactivate
virtualenvwrapper.user_scripts creating /home/leazhi/.env/haoke_small/bin/preactivate
virtualenvwrapper.user_scripts creating /home/leazhi/.env/haoke_small/bin/postactivate
virtualenvwrapper.user_scripts creating /home/leazhi/.env/haoke_small/bin/get_env_details
```

## 安装必要的开发包

python 虚拟环境创建好后,默认会自动进入该虚拟环境.所以我们直接在当前命令行下安装所需要的
开发包即可.

### 安装 django

执行命令: `pip3 install django==3.1.10`.这里最好指定下版本
```bash
(haoke_small) leazhi@ubuntuhome:~$ pip3 install django==3.1.10
Looking in indexes: https://pypi.tuna.tsinghua.edu.cn/simple
Collecting django==3.1.10
  Downloading https://pypi.tuna.tsinghua.edu.cn/packages/6f/78/77e58e30f8e8528a244663ea8551f5a741c8b9fcbc6993935bd802c676d1/Django-3.1.10-py3-none-any.whl (7.8 MB)
     ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ 7.8/7.8 MB 12.9 MB/s eta 0:00:00
Collecting asgiref<4,>=3.2.10 (from django==3.1.10)
  Using cached https://pypi.tuna.tsinghua.edu.cn/packages/9b/80/b9051a4a07ad231558fcd8ffc89232711b4e618c15cb7a392a17384bbeef/asgiref-3.7.2-py3-none-any.whl (24 kB)
Collecting pytz (from django==3.1.10)
  Using cached https://pypi.tuna.tsinghua.edu.cn/packages/7f/99/ad6bd37e748257dd70d6f85d916cafe79c0b0f5e2e95b11f7fbc82bf3110/pytz-2023.3-py2.py3-none-any.whl (502 kB)
Collecting sqlparse>=0.2.2 (from django==3.1.10)
  Using cached https://pypi.tuna.tsinghua.edu.cn/packages/98/5a/66d7c9305baa9f11857f247d4ba761402cea75db6058ff850ed7128957b7/sqlparse-0.4.4-py3-none-any.whl (41 kB)
Collecting typing-extensions>=4 (from asgiref<4,>=3.2.10->django==3.1.10)
  Using cached https://pypi.tuna.tsinghua.edu.cn/packages/ec/6b/63cc3df74987c36fe26157ee12e09e8f9db4de771e0f3404263117e75b95/typing_extensions-4.7.1-py3-none-any.whl (33 kB)
Installing collected packages: pytz, typing-extensions, sqlparse, asgiref, django
Successfully installed asgiref-3.7.2 django-3.1.10 pytz-2023.3 sqlparse-0.4.4 typing-extensions-4.7.1
```

### 安装 pymysql

执行命令: `pip3 install pymysql`
```bash
(haoke_small) leazhi@ubuntuhome:~$ pip3 install pymysql
Looking in indexes: https://pypi.tuna.tsinghua.edu.cn/simple
Collecting pymysql
  Using cached https://pypi.tuna.tsinghua.edu.cn/packages/e5/30/20467e39523d0cfc2b6227902d3687a16364307260c75e6a1cb4422b0c62/PyMySQL-1.1.0-py3-none-any.whl (44 kB)
Installing collected packages: pymysql
Successfully installed pymysql-1.1.0
```

### 安装 django-redis

执行命令: `pip3 install django-redis`
```bash
(haoke_small) leazhi@ubuntuhome:~$ pip3 install django-redis
Looking in indexes: https://pypi.tuna.tsinghua.edu.cn/simple
Collecting django-redis
  Downloading https://pypi.tuna.tsinghua.edu.cn/packages/6f/eb/4906ebd94805665d41ec067bec6520b7e47b95a648ea7d919c0e9e294e42/django_redis-5.3.0-py3-none-any.whl (30 kB)
Collecting Django>=3.2 (from django-redis)
  Using cached https://pypi.tuna.tsinghua.edu.cn/packages/7f/9e/fc6bab255ae10bc57fa2f65646eace3d5405fbb7f5678b90140052d1db0f/Django-4.2.4-py3-none-any.whl (8.0 MB)
Collecting redis!=4.0.0,!=4.0.1,>=3 (from django-redis)
  Downloading https://pypi.tuna.tsinghua.edu.cn/packages/df/b2/dfdc17f701f7b587f6c89c2b9b6b5978c87a8a785555efc810b064c875de/redis-5.0.0-py3-none-any.whl (250 kB)
     ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ 250.1/250.1 kB 2.1 MB/s eta 0:00:00
Requirement already satisfied: asgiref<4,>=3.6.0 in ./.env/haoke_small/lib/python3.10/site-packages (from Django>=3.2->django-redis) (3.7.2)
Requirement already satisfied: sqlparse>=0.3.1 in ./.env/haoke_small/lib/python3.10/site-packages (from Django>=3.2->django-redis) (0.4.4)
Collecting async-timeout>=4.0.2 (from redis!=4.0.0,!=4.0.1,>=3->django-redis)
  Downloading https://pypi.tuna.tsinghua.edu.cn/packages/a7/fa/e01228c2938de91d47b307831c62ab9e4001e747789d0b05baf779a6488c/async_timeout-4.0.3-py3-none-any.whl (5.7 kB)
Requirement already satisfied: typing-extensions>=4 in ./.env/haoke_small/lib/python3.10/site-packages (from asgiref<4,>=3.6.0->Django>=3.2->django-redis) (4.7.1)
Installing collected packages: async-timeout, redis, Django, django-redis
  Attempting uninstall: Django
    Found existing installation: Django 3.1.10
    Uninstalling Django-3.1.10:
      Successfully uninstalled Django-3.1.10
Successfully installed Django-4.2.4 async-timeout-4.0.3 django-redis-5.3.0 redis-5.0.0
```

### 安装 djangorestframework

执行命令: `pip3 install djangorestframework`
```bash
(haoke_small) leazhi@ubuntuhome:~$ pip3 install djangorestframework
Looking in indexes: https://pypi.tuna.tsinghua.edu.cn/simple
Collecting djangorestframework
  Using cached https://pypi.tuna.tsinghua.edu.cn/packages/ff/4b/3b46c0914ba4b7546a758c35fdfa8e7f017fcbe7f23c878239e93623337a/djangorestframework-3.14.0-py3-none-any.whl (1.1 MB)
Requirement already satisfied: django>=3.0 in ./.env/haoke_small/lib/python3.10/site-packages (from djangorestframework) (4.2.4)
Requirement already satisfied: pytz in ./.env/haoke_small/lib/python3.10/site-packages (from djangorestframework) (2023.3)
Requirement already satisfied: asgiref<4,>=3.6.0 in ./.env/haoke_small/lib/python3.10/site-packages (from django>=3.0->djangorestframework) (3.7.2)
Requirement already satisfied: sqlparse>=0.3.1 in ./.env/haoke_small/lib/python3.10/site-packages (from django>=3.0->djangorestframework) (0.4.4)
Requirement already satisfied: typing-extensions>=4 in ./.env/haoke_small/lib/python3.10/site-packages (from asgiref<4,>=3.6.0->django>=3.0->djangorestframework) (4.7.1)
Installing collected packages: djangorestframework
Successfully installed djangorestframework-3.14.0
```

### 检查安装的包:
执行命令: `pip3 list`
```bash
(haoke_small) leazhi@ubuntuhome:~$ pip3 list
Package             Version
------------------- -------
asgiref             3.7.2
async-timeout       4.0.3
Django              4.2.4
django-redis        5.3.0
djangorestframework 3.14.0
pip                 23.2.1
PyMySQL             1.1.0
pytz                2023.3
redis               5.0.0
setuptools          68.0.0
sqlparse            0.4.4
typing_extensions   4.7.1
wheel               0.41.1
```