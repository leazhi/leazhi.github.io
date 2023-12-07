---
title: django-small 系列004-跨域问题
author: leazhi
tags:
  - [django]
  - [python]
categories:
  - [django]
  - [python]
date: 2023-08-24 18:41:49
cover: https://hexo.linuser.com/gallery/code/1181671.jpg
discription: django, python3
keywords: django, python3
password: 
message: 
---

## 跨域

跨域（Cross-Origin）指的是在Web开发中，当一个网页的JavaScript代码试图访问不同源（Origin）的资源时所面临的限制。同源策略（Same-Origin Policy）是浏览器的一项安全机制，它限制了来自不同源的网页对当前网页的访问。源（Origin）由协议、主机名和端口号组成。 
 
跨域问题通常发生在以下情况下：  
1. 域名不同：当网页的域名与请求的资源的域名不同。   
2. 协议不同：当网页的协议与请求的资源的协议不同。   
3. 端口号不同：当网页的端口号与请求的资源的端口号不同。   
 
跨域问题会导致浏览器阻止网页中的JavaScript代码访问来自不同源的资源，如Ajax请求、跨域脚本访问、跨域数据读取等。为了解决跨域问题，可以使用一些方法，包括： 
1. JSONP（JSON with Padding）：通过在网页中插入一个 `<script>` 标签来加载跨域的JavaScript资源。    
2. CORS（Cross-Origin Resource Sharing）：在服务器端设置响应头，允许跨域请求。   
3. 代理服务器：在自己的服务器上创建一个代理，将跨域请求发送到目标服务器并返回结果。 
4. WebSocket：使用WebSocket协议进行双向通信，不受同源策略的限制。 

## 在Django中解决跨域问题

1.安装 django-cors-headers 库：
```bash
pip install django-cors-headers
```

2.**注册CORSHEADERS**：在Django项目的设置文件（settings.py）中(这里由于我们的设置文件为 dev.py,所以修改该文件)添加 corsheaders 到 INSTALLED_APPS 列表中：
```python
# haoke/haoke/settings/dev.py

...

INSTALLED_APPS = [
       ...
       'corsheaders',
       ...
]

...
```

3.**添加中间件**：同时，还需在Django项目的设置文件（settings.py）中(这里由于我们的设置文件为 dev.py,所以修改该文件)添加 corsheaders 到 MIDDLEWARE 列表中：
```python
# haoke/haoke/settings/dev.py

...

MIDDLEWARE = [
       ...
       'corsheaders.middleware.CorsMiddleware',
      #  'django.middleware.common.CommonMiddleware',
       ...
]

...
```

4.**设置白名单**：最后，还需在还需在Django项目的设置文件（settings.py）中(这里由于我们的设置文件为 dev.py,所以修改该文件)末尾添加如下配置：
```python
# haoke/haoke/settings/dev.py

...

# CORS_ORIGIN_ALLOW_ALL = True
CORS_ALLOW_CREDENTIALS = True
CORS_ORIGIN_WHITELIST = (
    'http://127.0.0.1:8000',
    'http://192.168.3.200:8000',
)
```