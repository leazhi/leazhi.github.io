---
title: django 设置静态文件访问路径
author: leazhi
tags:
  - [python]
  - [django]
categories:
  - [django]
  - [python]
date: 2023-07-18 12:10:30
cover: https://hexo.linuser.com/gallery/code/1181671.jpg
discription: 
keywords: 
password: 
message: 
---

{% note info simple %}
Django 默认在项目包目录下的 settings.py 文件中有定义静态文件的存放目录 `STATIC_URL='/static/'`,但这里仅仅是配置静态文件的存放目录。而如果直接在 URL 中访问该目录中的静态文件是无法访问到的。这是因为，还没有配置静态文件的访问路径。
{% endnote %}

## 配置静态文件的访问路径

打开项目包目录中的 settings.py 文件，在最后添加：
```python
STATICFILES_DIRS = [
     # 注意：这里的写法（BASE_DIR 是 settings.py 文件中定义的项目主目录路径），`/` 在URL中起连接作用， 'static' 是静态文件目录名称， 必须和 `STATIC_URL=` 中定义的一致

    BASE_DIR / 'static'                            
```

再次在 URL 中访问： http://127.0.0.1/static/001.jpg
