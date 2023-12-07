---
title: django 系列002:ubuntu 22.04 下安装配置 python 虚拟环境.md
author: leazhi
tags:
  - [python]
  - [django]
categories:
  - [python]
  - [django]
date: 2023-07-20 10:45:53
cover: 
discription: 
keywords: 
password: 
message: 
---



# 方法一：通过 pip3 安装 virtualenv 和 virtualenvwrapper 的方式:

## 安装虚拟环境包：

1.打开终端命令行工具，执行安装：
```bash
$ pip3 install virtualenv virtualenvwrapper -i https://pypi.douban.com/simple
```

## 配置：
1.创建虚拟环境存放目录：
```bash
$ mkdir ~/.env
```

2.编辑当前用户家目录下的 .bashrc 文件，在最底部加入如下配置：
```bash
# 指定虚拟环境目录：
export WORKON_HOME=/home/leazhi/.env

# 指定 python 解释器及版本
export VIRTUALENVWRAPPER_PYTHON=/usr/bin/python3

# 指定 virtualenv 命令的路径（leazhi@ubuntuhome:~$ whereis virtualenvwrapper.sh）
export VIRTUALENVWRAPPER_VIRTUALENV=/home/leazhi/.local/bin/virtualenv
source ~/.local/bin/virtualenvwrapper.sh
```

3.执行下 source 命令，加载虚拟环境配置：
```bash
leazhi@ubuntuhome:~$ source ~/.bashrc
virtualenvwrapper.user_scripts creating /home/leazhi/.env/premkproject
virtualenvwrapper.user_scripts creating /home/leazhi/.env/postmkproject
virtualenvwrapper.user_scripts creating /home/leazhi/.env/initialize
virtualenvwrapper.user_scripts creating /home/leazhi/.env/premkvirtualenv
virtualenvwrapper.user_scripts creating /home/leazhi/.env/postmkvirtualenv
virtualenvwrapper.user_scripts creating /home/leazhi/.env/prermvirtualenv
virtualenvwrapper.user_scripts creating /home/leazhi/.env/postrmvirtualenv
virtualenvwrapper.user_scripts creating /home/leazhi/.env/predeactivate
virtualenvwrapper.user_scripts creating /home/leazhi/.env/postdeactivate
virtualenvwrapper.user_scripts creating /home/leazhi/.env/preactivate
virtualenvwrapper.user_scripts creating /home/leazhi/.env/postactivate
virtualenvwrapper.user_scripts creating /home/leazhi/.env/get_env_details
```

## 创建虚拟环境
1.执行命令:
```bash
leazhi@ubuntuhome:~$ mkvirtualenv -p python3 web12              # 因为 Linux 有包含 python2 和 python3 ，所以使用 -p 参数指定使用哪个版本的 python 
created virtual environment CPython3.10.6.final.0-64 in 273ms
  creator CPython3Posix(dest=/home/leazhi/.env/web12, clear=False, no_vcs_ignore=False, global=False)
  seeder FromAppData(download=False, pip=bundle, setuptools=bundle, wheel=bundle, via=copy, app_data_dir=/home/leazhi/.local/share/virtualenv)
    added seed packages: pip==23.1.2, setuptools==67.7.2, wheel==0.40.0
  activators BashActivator,CShellActivator,FishActivator,NushellActivator,PowerShellActivator,PythonActivator
virtualenvwrapper.user_scripts creating /home/leazhi/.env/web12/bin/predeactivate
virtualenvwrapper.user_scripts creating /home/leazhi/.env/web12/bin/postdeactivate
virtualenvwrapper.user_scripts creating /home/leazhi/.env/web12/bin/preactivate
virtualenvwrapper.user_scripts creating /home/leazhi/.env/web12/bin/postactivate
virtualenvwrapper.user_scripts creating /home/leazhi/.env/web12/bin/get_env_details
(web12) leazhi@ubuntuhome:~$
```


## 管理虚拟环境：

### 列出所有虚拟环境：
直接在命令行执行 `workon` ：
```bash
leazhi@ubuntuhome:~$ workon
web12
```

### 进入指定的虚拟环境：
1.默认创建虚拟环境的时候就自动进入了该虚拟环境中。如果需要手动进入的话，则执行：
```bash
workon 虚拟环境名称
```


### 在虚拟环境中管理包：
1.执行命令 `pip3 list` 查看虚拟环境下所有包：
```bash
(web12) leazhi@ubuntuhome:~$ pip3 list
Package           Version
----------------- -------
asgiref           3.7.2
async-timeout     4.0.2
Django            3.1.7
django-redis      5.2.0
pip               23.1.2
pytz              2023.3
redis             4.6.0
setuptools        67.7.2
sqlparse          0.4.4
typing_extensions 4.7.0
wheel             0.40.0
```

2.在虚拟环境中安装包：
```bash
(web12) leazhi@ubuntuhome:~$ pip3 install django-redis django==3.1.7 -i https://pypi.douban.com/simple
Looking in indexes: https://pypi.douban.com/simple
Collecting django-redis
  Downloading https://pypi.doubanio.com/packages/75/59/35ac380419a4eafcc30660eaf142cbb334b19922c7250254636e406b8b82/django_redis-5.2.0-py3-none-any.whl (30 kB)
Collecting django==3.1.7
  Downloading https://pypi.doubanio.com/packages/b8/6f/9a4415cc4fe9228e26ea53cf2005961799b2abb8da0411e519fdb74754fa/Django-3.1.7-py3-none-any.whl (7.8 MB)
     ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ 7.8/7.8 MB 103.3 kB/s eta 0:00:00
Collecting asgiref<4,>=3.2.10 (from django==3.1.7)
  Using cached https://pypi.doubanio.com/packages/9b/80/b9051a4a07ad231558fcd8ffc89232711b4e618c15cb7a392a17384bbeef/asgiref-3.7.2-py3-none-any.whl (24 kB)
Collecting pytz (from django==3.1.7)
  Using cached https://pypi.doubanio.com/packages/7f/99/ad6bd37e748257dd70d6f85d916cafe79c0b0f5e2e95b11f7fbc82bf3110/pytz-2023.3-py2.py3-none-any.whl (502 kB)
Collecting sqlparse>=0.2.2 (from django==3.1.7)
  Using cached https://pypi.doubanio.com/packages/98/5a/66d7c9305baa9f11857f247d4ba761402cea75db6058ff850ed7128957b7/sqlparse-0.4.4-py3-none-any.whl (41 kB)
Collecting redis!=4.0.0,!=4.0.1,>=3 (from django-redis)
  Downloading https://pypi.doubanio.com/packages/20/2e/409703d645363352a20c944f5d119bdae3eb3034051a53724a7c5fee12b8/redis-4.6.0-py3-none-any.whl (241 kB)
     ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ 241.1/241.1 kB 194.2 kB/s eta 0:00:00
Collecting typing-extensions>=4 (from asgiref<4,>=3.2.10->django==3.1.7)
  Using cached https://pypi.doubanio.com/packages/7e/4d/b0185d077dc1cd070a56859a0ba3bb6e76618393ec693e59faf1368da8f6/typing_extensions-4.7.0-py3-none-any.whl (33 kB)
Collecting async-timeout>=4.0.2 (from redis!=4.0.0,!=4.0.1,>=3->django-redis)
  Using cached https://pypi.doubanio.com/packages/d6/c1/8991e7c5385b897b8c020cdaad718c5b087a6626d1d11a23e1ea87e325a7/async_timeout-4.0.2-py3-none-any.whl (5.8 kB)
Installing collected packages: pytz, typing-extensions, sqlparse, async-timeout, redis, asgiref, django, django-redis
Successfully installed asgiref-3.7.2 async-timeout-4.0.2 django-3.1.7 django-redis-5.2.0 pytz-2023.3 redis-4.6.0 sqlparse-0.4.4 typing-extensions-4.7.0
```

### 退出虚拟环境：

直接在命令行执行 `deactivate` ：
```bash
(web12) leazhi@ubuntuhome:~$ deactivate
leazhi@ubuntuhome:~$
```


# 方法二：通过使用 apt 安装 python3-virtualenv

1.安装虚拟环境包：
```bash
$ sudo apt install -y python3-virtualenv
```

2.创建虚拟环境目录：
```bash
$ mkdir ~/venv
```

3.进入创建的虚拟环境目录中：
```bash
$ cd ~/venv
```

4.执行 `virtualenv venv` 命令（注意：这是在创建的虚拟目录中执行的），生成虚拟环境：
```bash
leazhi@ubuntuhome:~/venv$ virtualenv venv
created virtual environment CPython3.10.6.final.0-64 in 220ms
  creator CPython3Posix(dest=/home/leazhi/venv/venv, clear=False, no_vcs_ignore=False, global=False)
  seeder FromAppData(download=False, pip=bundle, setuptools=bundle, wheel=bundle, via=copy, app_data_dir=/home/leazhi/.local/share/virtualenv)
    added seed packages: pip==23.1.2, setuptools==68.0.0, wheel==0.40.0
  activators BashActivator,CShellActivator,FishActivator,NushellActivator,PowerShellActivator,PythonActivator
```

5.执行命令 `source venv/bin/activate` 命令激活虚拟环境：
```bash
leazhi@ubuntuhome:~/venv$ source venv/bin/activate
(venv) leazhi@ubuntuhome:~/venv$
```

6.执行命令 `deactive` 退出虚拟环境：
```bash
(venv) leazhi@ubuntuhome:~/venv$ deactivate
leazhi@ubuntuhome:~/venv$
```