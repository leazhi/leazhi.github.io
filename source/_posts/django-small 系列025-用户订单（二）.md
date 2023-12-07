---
title: django-small 系列025-用户订单（二）
author: leazhi
tags:
  - [django]
  - [python]
categories:
  - [django]
  - [python]
date: 2023-10-07 09:06:25
cover: https://hexo.linuser.com/gallery/code/1181671.jpg
discription: 
keywords: 
password: 
message: 
---

## 用户中心的订单查询

### 创建序列化器

编辑子应用 orders 目录下的 serializers.py 文件,加入:
```python
# orders/serializers.py

...

class GoodsSKUSerializers(serializers.ModelSerializer):
    """
    SKU 序列化器
    """
    class Meta:
        model = SKU
        fields = ('id', 'name', 'default_image_url', 'price')

# 订单商品表
class OrderGoodsSKUSerializers(serializers.ModelSerializer):
    sku = GoodsSKUSerializers(read_only=True)
    class Meta:
        model = OrderGoods
        fields = ('count', 'price', 'sku', 'is_commented')

# 订单信息表
class OrderInfoGoodsSKUSerializers(serializers.ModelSerializer):
    """

    """
    skus = OrderGoodsSKUSerializers(many=True, read_only=True)
    create_time = serializers.DateTimeField(format='%Y-%m-%d %H:%M:%S')
    class Meta:
        model = OrderInfo
        fields = ('order_id', 'create_time', 'total_amount', 'freight', 'pay_method', 'status', 'skus')
```

### 创建视图

编辑子应用 orders 目录下的 views.py 文件,添加:

```python
# orders/views.py

...

from .serializers import OrderSettlementSerializers, SaveOrderSerializers,OrderInfoGoodsSKUSerializers
from . models import OrderInfo

...

class OrderAllView(ListAPIView):
    """
    我的订单视图 -- 全部订单商品 --- 查询
    """
    permission_classes = [IsAuthenticated]

    serializer_class = OrderInfoGoodsSKUSerializers

    # 分页\排序
    filter_backends = (OrderingFilter,)
    ordering_fields = ('create_time',)

    def get_queryset(self):
        # 目的是为了指定获取当强登录用户的所有订单
        orders = OrderInfo.objects.filter(user=self.request.user).order_by('-create_time')

        return orders
```


### 添加访问路由

编辑子应用 orders 目录下的  urls.py 文件，添加“
```python
# orders/urls.py

from django.urls import path, re_path
from . import views

urlpatterns = [
    path('orders/settlement/', views.OrderSettlementView.as_view()),
    path('orders/', views.SaveOrderView.as_view()),
    path('orders/all/', views.OrderAllView.as_view())
]
```