---
title: django-small 系列023-购物车（二）
author: leazhi
tags:
  - [python]
  - [django]
categories:
  - [python]
  - [django]
date: 2023-09-30 20:33:50
cover: https://hexo.linuser.com/gallery/code/1181671.jpg
discription: 
keywords: python, django
password: 
message: 
---

## 购物车商品修改 

### 添加视图方法

编辑子应用 carts 目录下的 views.py 文件，在 CartView() 视图类下添加商品修改方法代码，如下：
```python
#  carts/views.py

...

class CartView(APIView):

    ...

    def put(self, request):
      # 获取数据
      data = request.data
      # 校验数据
      ser = CartSerializers(data=data)
      ser.is_valid(raise_exception=True)
      # 修改数据 --- 获取验证通过后的数据
      sku_id = ser.validated_data['sku_id']
      count = ser.validated_data['count']
      selected = ser.validated_data['selected']

      # 验证用户是否登录
      user = request.user
      if user and user.is_authenticated:
          # 修改 redis 里面的数据
          redis_conn = get_redis_connection("carts")
          pl = redis_conn.pipeline()
          # 修改商品数量 -- 哈希值（没有修改的方法， 可以使用 hset 设置方法，来作为覆盖原来的数据）
          pl.hset('cart_%d' % user.id, sku_id, count)

          if selected:
              pl.sadd('selected_%d' % user.id, sku_id)
          else:
              # 取消了勾选
              pl.srem('selected_%d' % user.id, sku_id)

          pl.execute()
      return Response(ser.data)

```


## 购物车商品删除

获取要删除的数据 -- 校验数据是否存在 -- 执行删除 -- 返回删除状态码

### 添加视图方法

编辑子应用 carts 目录下的 views.py 文件，在 CartView() 视图类下添加商品修改方法代码，如下：
```python
#  carts/views.py

...

class CartView(APIView):

    ...

    def delete(self, request):
        data = request.data

        # 获取要删除的商品
        sku_id = data.get('sku_id')

        # 校验数据是否存在
        # sku = SKU.objects.get(id=sku_id)
        # redis 里面查询
        user = request.user
        if user and user.is_authenticated:
            redis_conn = get_redis_connection("carts")
            pl = redis_conn.pipeline()

            pl.hdel('cart_%d' % user.id, sku_id)
            pl.srem('selected_%d' % user.id, sku_id)

            pl.execute()

        return Response(status=status.HTTP_206_PARTIAL_CONTENT)
```


## 购物车商品全选

### 添加视图方法

编辑子应用 carts 目录下的 views.py 文件，在该文件的最后添加商品全选类代码，如下：
```python
#  carts/views.py

...

from .serializers import CartSerializers, SKUCartSerializers,CartSelectAllSerializers

...

class CartSelectedAllView(APIView):
    """全选："""
    def put(self, request):
        ser = CartSelectAllSerializers(data=request.data)
        # 数据校验
        ser.is_valid(raise_exception=True)
        selected = ser.validated_data['selected']
        redis_conn = get_redis_connection("carts")

        user = request.user

        # sku_id
        cart_redis_dict = redis_conn.hgetall('cart_%d' % user.id)

        # 获取里面的所有的键
        sku_id = cart_redis_dict.keys()

        if selected:
            # 如果是 True ,就添加到 set 集合
            redis_conn.sadd('selected_%d' % user.id, *sku_id)
        else:
            redis_conn.srem('selected_%d' % user.id, *sku_id)

        return Response(ser.data ,status=status.HTTP_204_NO_CONTENT)
```

### 添加商品全选路由

编辑子应用 carts 目录下的 urls.py 文件，在 urlpatterns = [ ... ] 中添加商品全选访问路由，如下:
```python
# carts/urls.py

from . import views
from django.urls import path, re_path

urlpatterns = [
    path('carts/', views.CartView.as_view()),
    path('carts/selection/', views.CartSelectedAllView.as_view()),
]
```