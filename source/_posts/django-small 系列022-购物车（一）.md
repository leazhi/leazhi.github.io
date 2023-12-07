---
title: django-small 系列022-购物车（一）
author: leazhi
tags:
  - [python]
  - [django]
categories:
  - [python]
  - [django]
date: 2023-09-26 06:50:41
cover: https://hexo.linuser.com/gallery/code/1181671.jpg
discription: 
keywords: python, django
password: 
message: 
---

## 购物车子应用的创建及配置

### 创建购物车子应用

1.打开命令行终端，进入项目主目录，执行命令 `python manage.py startapp carts`  创建购物车子应用
```bash
┌──(leazhi㉿kali-desktop)-[/data/gitlab/python3-django-small_haoke]
└─$ cd haoke/haoke/apps 

┌──(leazhi㉿kali-desktop)-[/data/…/python3-django-small_haoke/haoke/haoke/apps]
└─$ python ../../manage.py startapp carts
```

2.编辑项目的配置文件 dev.py，在该文件中注册购物车子应用
```python
# settings/dev.py

...

INSTALLED_APPS = [
    ...
    'carts',
]
...
```

## 使用 redis 保存购物车数据

编辑项目配置文件 dev.py， 在该文件中的 redis 配置字典中添加购物车子应用的 redis 配置，如下：

```python
# settings.dev.py

...

CACHES = {
    ...
    'carts': {
        "BACKEND": "django_redis.cache.RedisCache",
        'LOCATION': 'redis://192.168.3.254:22652/2',
        'OPTIONS': {
            'CLIENT_CLASS': 'django_redis.client.DefaultClient',
            "PASSWORD": "Zmi2hU4hGkdroBl7wWm/DszVaWWqrgNuWwD2tmHO",
        }
    },
}

...
```

## 实现购物车添加商品

### 创建购物车序列化器
在子应用 carts 目录下新建序列化器文件 serializers.py ，添加如下内容：
```bash
from rest_framework import serializers
from goods.models import SKU
class CartSerializers(serializers.Serializer):
    """
    购物车数据序列化器
    """

    # 校验数据
    sku_id = serializers.IntegerField(label="sku_id", min_value=1)
    count = serializers.IntegerField(label='count', min_value=1)
    # 勾选的商品
    selected = serializers.BooleanField(label="是否勾选", default=True)

    # 验证:查询数据是否存在。 data 序列化后的数据 --Serializer 教研后的数据
    def validate(self, data):
        try:
            # sku = SKU.objects.get(id=self.sku_id)
            sku = SKU.objects.get(id=data['sku_id'])
        except SKU.DoesNotExits:
            raise serializers.ValidationError('商品不存在')

        return data

# 勾选的商品 default=True ，在已经查询出来了的数据上做事情
class CartSelectAllSerializers(serializers.Serializer):
    """勾选商品的返回"""
    # 勾选的商品
    selected = serializers.BooleanField(label="全选")
```

### 添加购物车视图

编辑子应用 carts 目录下的 views.py 文件，添加如下内容：
```bash
from django.shortcuts import render
from rest_framework.views import APIView
from serializers import CartSerializers
from django_redis import get_redis_connection
from rest_framework.response import Response
from rest_framework import status

# Create your views here.

class CartView(APIView):
    """购物车的增删改查"""

    def post(self, request):
        # sku_id, count
        # 获取数据（request.data)并校验数据
        ser = CartSerializers(data = request.data)
        # 校验
        ser.is_valid(raise_exception=True)
        # 获取校验后的数据保存后再返回
        sku_id = ser.validated_data.get('sku_id')
        count = ser.validated_data.get('count')
        selected = ser.validated_data.get('selected')
        # 判断用户是否登录  request.user 获取用户数据， 判断一下用户是否提供认证的用户
        user = request.user
        # is_authenticated 校验用户是否为认证后的用户数据
        if user and user.is_authenticated:
            redis_conn = get_redis_connection("carts")
            # 哈希类型
            pl = redis_conn.pipeline()
            # cart_user_id      ---> cart_1, cart_2, ..
            pl.hincrby("cart_%d" % user.id, sku_id, count)
            # 勾选商品, 保存到 set 集合中
            if selected:
                pl.sadd('selected_%d' % user.id, sku_id)
                # 执行管道
                pl.execute()
                # 返回
                return  Response(ser.data, status=status.HTTP_201_CREATED)
```

### 添加购物车访问路由

在子应用 carts 目录下新建 urls.py 文件，添加如下内容：
```bash
from . import views
from django.urls import path, re_path


urlpatterns = [
    re_path('carts/', views.CartView.as_view()),
]
```

### 项目总路由添加购物车路由

编辑项目总路由文件，添加子应用 carts 的访问路由
```python
# haoke/urls.py

...


from django.contrib import admin
from django.urls import path, include

urlpatterns = [
    path('admin/', admin.site.urls),
    path('', include('users.urls')),
    path('', include('verifications.urls')),
    path('', include('areas.urls')),
    path('ckeditor/', include('ckeditor_uploader.urls')),
    path('', include('goods.urls')),
    # 购物车子应用
    path('', include('carts.urls')),
]
```

## 购物车商品展示

### 数据查询

#### 添加视图方法
编辑子应用 carts 目录下的 views.py 文件，在视图类 CartView()  里面添加如下内容：

```python
# carts/views.py

from django.shortcuts import render
from rest_framework.views import APIView
from .serializers import CartSerializers, SKUCartSerializers
from django_redis import get_redis_connection
from rest_framework.response import Response
from rest_framework import status
from goods.models import SKU

# Create your views here.

class CartView(APIView):
    """购物车的增删改查"""

    def post(self, request):
        # sku_id, count
        # 获取数据（request.data)并校验数据
        ser = CartSerializers(data = request.data)
        # 校验
        ser.is_valid(raise_exception=True)
        # 获取校验后的数据保存后再返回
        sku_id = ser.validated_data.get('sku_id')
        count = ser.validated_data.get('count')
        selected = ser.validated_data.get('selected')
        # 判断用户是否登录  request.user 获取用户数据， 判断一下用户是否提供认证的用户
        user = request.user
        # is_authenticated 校验用户是否为认证后的用户数据
        if user and user.is_authenticated:
            redis_conn = get_redis_connection("carts")
            # 哈希类型
            pl = redis_conn.pipeline()
            # cart_user_id      ---> cart_1, cart_2, ..
            pl.hincrby("cart_%d" % user.id, sku_id, count)
            # 勾选商品, 保存到 set 集合中
            if selected:
                pl.sadd('selected_%d' % user.id, sku_id)
                # 执行管道
                pl.execute()
                # 返回
                return  Response(ser.data, status=status.HTTP_201_CREATED)

    # 展示购物车商品
    def get(self,request):
        # 用户是否登录
        user = request.user
        if user and user.is_authenticated:
            # 查询哪些数据（SKU） 返回的是什么类型   json  ---> 序列化器作一个序列化输出
            # 从 redis里面拿 sku_id
            redis_conn = get_redis_connection("carts")
            # 获取数据
            cart_redis_dict = redis_conn.hgetall('cart_%d' % user.id)
            # 获取 set 勾选的数据
            selecteds = redis_conn.smembers('selected_%d' % user.id)

            cart_dict = {}
            # for i in cart_redis_dict.key() :
            for sku_id_bytes, count_bytes in cart_redis_dict.items():
                cart_dict[int(sku_id_bytes)] = {
                    'count': int(count_bytes),
                    'selected': sku_id_bytes in selecteds
                }
                skus = SKU.objects.filter(id__in=cart_dict.keys())

                for sku in skus:
                    # 临时添加属性
                    # sku.count = cart_redis_dict[sku.id]['count']
                    sku.count = cart_dict[sku.id]['count']
                    sku.selected = selecteds[sku.id]['selected']
                    # sku.selected = selecteds
                ser = SKUCartSerializers(skus, many=True)
                return Response(ser.data)
```

#### 添加序列化器
编辑子应用 carts 目录下的 serializers.py 文件，添加如下内容：
```python
#  carts/serializers.py

...

class SKUCartSerializers(serializers.ModelSerializer):
    count = serializers.IntegerField(label='数量')
    selectd = serializers.BooleanField(label='是否勾选')
    class Meta:
        model = SKU
        # 指定序列化后的数据
        fields = ('id','count', 'name', 'default_image_url', 'selected', 'price')
```