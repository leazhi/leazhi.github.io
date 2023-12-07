---
title: django-small 系列017-商品列表页
author: leazhi
tags:
  - [python]
  - [django]
categories:
  - [python]
  - [django]
date: 2023-09-20 07:33:54
cover: https://hexo.linuser.com/gallery/code/1181671.jpg
discription: 
keywords: django, python
password: 
message: 
---


## 创建生成商品列表的计划任务

### 创建计划任务

1.在计划任务包目录 celery_tasks 目录下新建一个名为 html 的包目录;


2.在新建的 html 包目录下新建一个 tasks.py 文件，内容为：
```bash
import os
# from haoke.apps.goods.utils import get_categories
from goods.utils import get_categories
from celery_tasks.main import app
from django.conf import settings
from django.template import loader

@app.task(name="generate_static_list_search_html")

def generate_static_list_search_html():
    """
    生成静态的商品列表页
    """

    categories = get_categories()

    # 渲染模板
    context = {
        'categories': categories
    }

    # 获取指定的模板
    template = loader.get_template('list.html')

    # 把数据写入到指定的模板中
    html_text = template.render(context)

    # 把模板中的数据写道前端的静态服务器里面的 index.html 文件里
    file_path = os.path.join(settings.GENERATED_STATIC_HTML_FILES_DIR, 'list.html')

    with open(file_path, 'w', encoding='utf-8') as f:
        f.write(html_text)
```

### 注册计划任务

1.编辑计划任务包目录 celery_tasks 目录下的 main.py 文件，在 `app.autodiscover_tasks(..)` 元祖中添加 `html` 计划任务;
```python
# celery_tasks/main.py

#!/usr/bin/env python
# -*- encoding: utf-8 -*-

'''
# @Time: 8/26/23 8:30 PM
# @Author: leazhi
# @Emal: leazhi@outlook.com
# @Filename: main.py
# @Project: python3
'''

from celery import Celery
import os

# 为 celery 使用 Django 配置
if not os.getenv('DJANGO_SETTINGS_MODULE'):
    os.environ['DJANGO_SETTINGS_MODULE'] = 'haoke.settings.dev'

# 创建爱女 celery 应用
app = Celery('haoke')

# 添加 selery 配置信息
app.config_from_object('celery_tasks.config')

# 注册任务
app.autodiscover_tasks(['celery_tasks.sms', 'celery_tasks.email', 'celery_tasks.html'])
```

### 改写商品子应用

1.在 goods 子应用下新建一个 utils.py 的文件，将查询的商品列表部分代码抽出来，写入到该文件中，便于上面的计划任务调用：
```python
# googs/utils.py

from collections import  OrderedDict
from .models import GoodsChannel

def get_categories():
    categories = OrderedDict()  # 商品类别转换成字典类型

    # 查询:查询集
    channels = GoodsChannel.objects.order_by('group_id', 'sequence')  # 查询排序

    for channel in channels:
        # 组号
        group_id = channel.group_id
        # 利用组号查询类别
        if group_id not in categories:
            categories[group_id] = {'channel': [], 'sub_cats': []}
        # 当前频道的类别
        cat1 = channel.category
        categories[group_id]['channel'].append({
            'id': cat1.id,
            'name': cat1.name,
            'url': channel.url
        })

        # cat2 = cat1.goodscategory_set.all()        # 二级类别
        for cat2 in cat1.goodscategory_set.all():
            cat2.sub_cats = []
            for cat3 in cat2.goodscategory_set.all():
                cat2.sub_cats.append(cat3)
            categories[group_id]['sub_cats'].append(cat2)
    return categories
```

### 调用商品列表页异步任务

1.编辑子应用 goods 下面的 admin.py 文件，在该文件中添加商品修改类，然后在该类中调用生成商品列表异步任务：
```python
goods/admin.py

from django.contrib import admin

from celery_tasks.html.tasks import generate_static_list_search_html            # 导入计划任务
from .models import *
# Register your models here.

# generate_static_list_search_html
# 商品列表调用:当用户在后台修改或者添加商品类别的生活，就现保存修改或者添加的数据，然后调用商品列表生成异步任务生成商品列表页
class GoodsCategoryAdmin(admin.ModelAdmin):
    def save_model(self, request, obj, form, change):
        """
        实现修改和添加模型类数据
        obj:获取修改或者添加的数据
        """
        obj.save()
        generate_static_list_search_html()

    def delete_model(self, request, obj):
        obj.delete()
        generate_static_list_search_html()

admin.site.register(Goods, GoodsCategoryAdmin)
admin.site.register(Brand)
admin.site.register(SKU)
admin.site.register(SKUImage)
admin.site.register(SKUSpecification)
admin.site.register(GoodsChannel)
admin.site.register(GoodsCategory)
admin.site.register(GoodsSpecification)
admin.site.register(SpecificationOption)
```

### 在命令行测试调用异步任务

打开命令行终端，进入项目跟目录，然后执行;
```bash
┌──(leazhi㉿kali-desktop)-[/data/gitlab/python3-django-small_haoke/haoke]
└─$ celery -A celery_tasks.main worker -l info
 
 -------------- celery@kali-desktop v5.3.4 (emerald-rush)
--- ***** ----- 
-- ******* ---- Linux-6.4.0-kali3-amd64-x86_64-with-glibc2.37 2023-09-20 08:14:50
- *** --- * --- 
- ** ---------- [config]
- ** ---------- .> app:         haoke:0x7faea6b4fb90
- ** ---------- .> transport:   redis://192.168.3.254:22652/14
- ** ---------- .> results:     disabled://
- *** --- * --- .> concurrency: 16 (prefork)
-- ******* ---- .> task events: OFF (enable -E to monitor tasks in this worker)
--- ***** ----- 
 -------------- [queues]
                .> celery           exchange=celery(direct) key=celery
                

[tasks]
  . generate_static_list_search_html
  . send_sms_code
  . send_varify_email

[2023-09-20 08:14:51,011: WARNING/MainProcess] /home/leazhi/.local/lib/python3.11/site-packages/celery/worker/consumer/consumer.py:507: CPendingDeprecationWarning: The broker_connection_retry configuration setting will no longer determine
whether broker connection retries are made during startup in Celery 6.0 and above.
If you wish to retain the existing behavior for retrying connections on startup,
you should set broker_connection_retry_on_startup to True.
  warnings.warn(
...
```
**参数说明**：
- worker -l info：指定调试模式下输入的日志级别；


## 采用异步任务的方法改写首页

1.编辑商品子应用 goods 目录下爱的 utils.py 文件，将首页静态化的方法从子应用你哦个 contents 目录下的 crons 文件中抽离代该文件中，并 return:
```python
# goods/utils.py

from collections import  OrderedDict
from .models import GoodsChannel
from ..contents.models import ContentCategory


def get_categories():
    categories = OrderedDict()  # 商品类别转换成字典类型

    # 查询:查询集
    channels = GoodsChannel.objects.order_by('group_id', 'sequence')  # 查询排序

    for channel in channels:
        # 组号
        group_id = channel.group_id
        # 利用组号查询类别
        if group_id not in categories:
            categories[group_id] = {'channel': [], 'sub_cats': []}
        # 当前频道的类别
        cat1 = channel.category
        categories[group_id]['channel'].append({
            'id': cat1.id,
            'name': cat1.name,
            'url': channel.url
        })

        # cat2 = cat1.goodscategory_set.all()        # 二级类别
        for cat2 in cat1.goodscategory_set.all():
            cat2.sub_cats = []
            for cat3 in cat2.goodscategory_set.all():
                cat2.sub_cats.append(cat3)
            categories[group_id]['sub_cats'].append(cat2)

    return categories


def get_index_context():
    categories = OrderedDict()              # 商品类别转换成字典类型

    # 查询:查询集
    channels = GoodsChannel.objects.order_by('group_id', 'sequence')                # 查询排序

    for channel in channels:
        # 组号
        group_id = channel.group_id
        # 利用组号查询类别
        if group_id not in categories:
            categories[group_id] = {'channel': [], 'sub_cats': []}
        # 当前频道的类别
        cat1 = channel.category
        categories[group_id]['channel'].append({
            'id': cat1.id,
            'name': cat1.name,
            'url': channel.url
        })

        # cat2 = cat1.goodscategory_set.all()        # 二级类别
        for cat2 in cat1.goodscategory_set.all():
            cat2.sub_cats = []
            for cat3 in cat2.goodscategory_set.all():
                cat2.sub_cats.append(cat3)
            categories[group_id]['sub_cats'].append(cat2)

    ## 广告内容
    contents = {}

    # 使用广告类别查询广告内容
    content_categories = ContentCategory.objects.all()
    for cat in content_categories:
        contents[cat.key] = cat.content_set.filter(status=True).order_by('sequence')

    ## 渲染模板
    context = {
        'contents': contents,
        'catetories': categories
    }

    return context
```

2.编辑异步任务 html 包目录下的 tasks.py 文件，添加首页静态化任务：
```python
# html/tasks.py

import os
from haoke.apps.goods.utils import get_categories
# from goods.utils import get_categories
from celery_tasks.main import app
from django.conf import settings
from django.template import loader

from haoke.apps.goods.utils import get_index_context

# 商品列表页异步任务
@app.task(name="generate_static_list_search_html")
def generate_static_list_search_html():
    """
    生成静态的商品列表页
    """

    categories = get_categories()

    # 渲染模板
    context = {
        'categories': categories
    }

    # 获取指定的模板
    template = loader.get_template('list.html')

    # 把数据写入到指定的模板中
    html_text = template.render(context)

    # 把模板中的数据写道前端的静态服务器里面的 index.html 文件里
    file_path = os.path.join(settings.GENERATED_STATIC_HTML_FILES_DIR, 'list.html')

    with open(file_path, 'w', encoding='utf-8') as f:
        f.write(html_text)


# 首页静态化异步任务
@app.task(name="generate_static_index_html")
def generate_static_index_html():

    context = get_index_context()
    # 获取指定的模板
    template = loader.get_template('index.html')

    # 把数据写入到指定的模板中
    html_text = template.render(context)

    # 把模板中的数据写道前端的静态服务器里面的 index.html 文件里
    file_path = os.path.join(settings.GENERATED_STATIC_HTML_FILES_DIR, 'index.html')

    with open(file_path, 'w', encoding='utf-8') as f:
        f.write(html_text)
```

3.编辑子应用 goods 目录下的 admin.py 文件，在该文件中的 GoodsCategoryAdmin() 类中调用首页静态化的异步任务 generate_static_index_html():
```python
from django.contrib import admin

from celery_tasks.html.tasks import generate_static_list_search_html, generate_static_index_html
from .models import *
# Register your models here.


# generate_static_list_search_html
# 商品列表调用:当用户在后台修改或者添加商品类别的生活，就现保存修改或者添加的数据，然后调用商品列表生成异步任务生成商品列表页
class GoodsCategoryAdmin(admin.ModelAdmin):
    def save_model(self, request, obj, form, change):
        """
        实现修改和添加模型类数据
        obj:获取修改或者添加的数据
        """
        obj.save()
        # 调用商品列表页异步任务
        generate_static_list_search_html()

        # 调用首页静态化异步任务
        generate_static_index_html()

    def delete_model(self, request, obj):
        obj.delete()
        generate_static_list_search_html()
        generate_static_index_html()


admin.site.register(Goods, GoodsCategoryAdmin)
admin.site.register(Brand)
admin.site.register(SKU)
admin.site.register(SKUImage)
admin.site.register(SKUSpecification)
admin.site.register(GoodsChannel)
admin.site.register(GoodsCategory)
admin.site.register(GoodsSpecification)
admin.site.register(SpecificationOption)
```