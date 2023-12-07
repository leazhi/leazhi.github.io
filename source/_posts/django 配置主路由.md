---
title: django 配置主路由.md
author: leazhi
tags:
  - [python]
  - [django]
categories:
  - [python]
  - [django]
date: 2023-07-18 12:14:15
cover: https://hexo.linuser.com/gallery/code/1181671.jpg
discription: 
keywords: 
password: 
message: 
---


django 主路由的配置如下：

1.在项目包目录下编辑 urls.py 文件，配置如下：
```python
from django.contrib import admin
from django.urls import path
from users.views import login           # 导入子应用 users 下视图文件 views.py 中的 login 方法

urlpatterns = [
    """
    第一个参数是字符串，路由名称，可以随意定义，后面以这个定义的名称进行访问；
    第二个参数是指定的函数或者类，决定了路由返回的内容
    """
    # 可以直接在 URL 中： http://127.0.0.1/admin (cd 子应用目录，执行 python manage.py migrations)
    path('admin/', admin.site.urls),   

    # 'login/'定义的路由名称，也就是要在 url 中访问的名称；login 是子应用 users 中视图文件 views.py 中的函数(顶部有导入)
    path('login/', login)              
]
```

2.然后在子应用包下面的 views.py 文件中定义 login 方法：
```python
from django.shortcuts import render
from django.http import HttpResponse            # 需要导入该
# Create your views here.


def login(request):                             # 访问的时候： http://127.0.0.1:/login
    return HttpResponse('欢迎登录')
```