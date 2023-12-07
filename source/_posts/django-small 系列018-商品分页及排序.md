---
title: django-small 系列018-商品分页及排序
author: leazhi
tags:
  - [python]
  - [django]
categories:
  - [python]
  - [django]
date: 2023-09-20 09:12:21
cover: https://hexo.linuser.com/gallery/code/1181671.jpg
discription: 
keywords: django, python
password: 
message: 
---

## 改写 rest_framework 自带的分页器

1.在包目录 utils 下新建 pagination.py 文件，改写分页器：
```python
# utils/pagination.py

from rest_framework.pagination import PageNumberPagination

class StandarPageNumberPagination(PageNumberPagination):
    page_size = 10
    max_page_size = 20
    page_size_query_param = 'page_size'
```

2.编辑项目配置文件 dev.py ，在 `REST_FREAMWORK =  { .. }` 列表中添加分页器：
```bash
# settings./dev.py

...

REST_FRAMEWORK = {
    'DEFAULT_AUTHENTICATION_CLASSES': (
        'rest_framework_jwt.authentication.JSONWebTokenAuthentication',
        'rest_framework.authentication.SessionAuthentication',
        'rest_framework.authentication.BasicAuthentication',
    ),
    # 分页器
    "DEFAULT_PAGINATION_CLASS":"haoke.utils.pagination.StandarPageNumberPagination"
    # 'DEFAULT_PERMISSION_CLASSES': (
    #     'rest_framework.permissions.IsAuthenticated',
    # ),
    # 'DEFAULT_PAGINATION_CLASS': 'rest_framework.pagination.PageNumberPagination',
    # 'PAGE_SIZE': 10
}
...
```

## 创建分页视图类、序列化器和路由

### 创建序列化器

在子应用 goods 目录下新建 serializers.py 文件，内容为：
```python
# goods/serializers.py

from rest_framework import serializers
from .models import SKU
class SKUSerializers(serializers.ModelSerializer):
    class Meta:
        model = SKU

        fields = ('id', 'name', 'price', 'comments', 'default_image_url')
```

### 创建视图

编辑子应用 goods 目录下的视图文件  views.py,添加如下内容：
```python
# goods/views.py

from django.shortcuts import render
from rest_framework.filters import OrderingFilter
from rest_framework.generics import ListAPIView
from .serializers import SKUSerializers
# Create your views here.

class SKUListView(ListAPIView):
    serializer_class = SKUSerializers
    #
    filter_backends = (OrderingFilter)

    ordering_fields = ('create_time', 'price', 'comments')
    def get_queryset(self):

        #获取前端发送过来的查询数据
        category_id = self.kwargs['category_id']
         # 返回查询出来的数据
        return SKU.objects.filter(category_id=category_id, is_launched=True)
```

### 创建路由

在子应用 goods 目录下新建 urls.py 文件，添加：
```python
# goods/urls.py

from . import views
from django.urls import path, re_path

urlpatterns = [
    re_path('^categories/(?P<category_id>\d+)/skus/$', views.SKUListView.as_view()),
]
```

### 总路由注册子路由
编辑项目目录下的 urls.py 文件，在路由列表中添加：
```python
# haoke/urls.py

from django.contrib import admin
from django.urls import path, include

urlpatterns = [
    path('admin/', admin.site.urls),
    path('', include('users.urls')),
    path('', include('verifications.urls')),
    path('', include('areas.urls')),
    path('ckeditor/', include('ckeditor_uploader.urls')),
    # 商品路由注册
    path('', include('goods.urls')),                                  
]
```