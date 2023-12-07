---
title: django 系列003:python 虚拟环境管理命令.md
author: leazhi
tags:
  - [python]
  - [django]
categories:
  - [python]
  - [django]
date: 2023-07-20 10:45:13
cover: 
discription: 
keywords: python, django
password: 
message: 
---



{% note default simple %}
由于我们需要隔离 Python 环境，所以，在开始之前，我们需要先查看系统是否有配置虚拟环境
{% endnote %}


# python 虚拟环境的管理及列出


### 列出所有 python 虚拟环境
打开 cmd 命令行，执行：
```cmd
C:\Users\leazh>workon

Pass a name to activate one of the following virtualenvs:
==============================================================================
env03
env04
env05
env06
```

### 进入某个虚拟环境
执行命令：
```cmd
C:\Users\leazh>workon env06
(env06) C:\Users\leazh>
```

### 查看当前虚拟环境下有哪些包：
执行命令：
```cmd
(env06) C:\Users\leazh>pip3 list
Package    Version
---------- -------
pip        23.1.2
setuptools 67.8.0
wheel      0.40.0

[notice] A new release of pip is available: 23.1.2 -> 23.2
[notice] To update, run: python.exe -m pip install --upgrade pip
```

# 安装指定版本的 django

{% note info simple %}
从上面的配置信息可以知道，我们现在处于 python 虚拟环境名为 env06 的虚拟环境中，所以，接下来，我们直接在该环境下安装指定版本的 django
{% endnote %}

### 安装 django:
打开 cmd 命令行，执行：
```cmd
pip3 install django==3.1.7 -i https://pypi.douban.com/simple
```

### 安装完成后，再次列出该 python 虚拟环境下有安装哪些包，可以和没装之前做下对比
执行命令：
```cmd
(env06) C:\Users\leazh>pip3 list
Package    Version
---------- -------
asgiref    3.7.2
Django     3.1.7
pip        23.1.2
pytz       2023.3
setuptools 67.8.0
sqlparse   0.4.4
wheel      0.40.0

[notice] A new release of pip is available: 23.1.2 -> 23.2
[notice] To update, run: python.exe -m pip install --upgrade pip
```

