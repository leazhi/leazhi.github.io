---
title: django-small 系列024-用户订单（一）
author: leazhi
tags: 
  - [django]
  - [python]
categories: 
  - [django]
  - [python]
date: 2023-10-05 20:55:26
cover: https://hexo.linuser.com/gallery/code/1181671.jpg
discription: 
keywords: 
password: 
message: 
---


## 创建及注册用户订单子应用

### 创建子应用

进入项目子目录 apps， 执行下面的命令创建子应用 orders:
```bash
┌──(leazhi㉿kali-desktop)-[/data/gitlab/python3-django-small_haoke]
└─$ cd haoke/haoke/apps
                                                                                                       
┌──(leazhi㉿kali-desktop)-[/data/…/python3-django-small_haoke/haoke/haoke/apps]
└─$ python ../../manage.py startapp orders
```

### 注册子应用

编辑项目配置文件，在 `INSTALLED_APPS = [ .. ]` 列表中注册创建的子应用 orders:
```python
# settings/dev.py

...


INSTALLED_APPS = [
    ...
        'orders',
]

...
```

## 用户订单数据设计

### 编写模型类

编辑子应用 orders 下的 models.py 文件，在该文件中添加如下代码：

```python
# oders/models.py

from django.db import models

# Create your models here.
from django.db import models
from goods.models import SKU
from users.models import User, Address
from haoke.utils.models import BaseModel

class OrderInfo(BaseModel):
    """订单信息"""
    PAY_METHODS_ENUM = {
        "CASH": 1,
        "ALIPAY": 2
    }
    
    PAY_METHODS_CHOICES = (
        (1, "货到付款"),
        (2, "支付宝"),
    )
    
    ORDER_STATUS_ENUM = {
        "UNPAID": 1,
        "UNSEND": 2,
        "UNRECEIVED": 3,
        "UNCOMMENT": 4,
        "FINISHED": 5
    }
    
    ORDER_STATUS_CHOICES = (
        (1, "待支付"),
        (2, "待发活"),
        (3, "待收获"),
        (4, "待评价"),
        (5, "已完成"),
        (6, "已取消"),
    )
    
    order_id = models.CharField(max_length=64, primary_key=True, verbose_name="订单号")
    user = models.ForeignKey(User, on_delete=models.PROTECT, verbose_name="下单用户")
    address = models.ForeignKey(Address, on_delete=models.PROTECT, verbose_name="收货地址")
    total_count = models.IntegerField(default=1, verbose_name="商品总数")
    total_amount = models.DecimalField(max_digits=10, decimal_places=2, verbose_name="商品总金额")
    freight = models.DecimalField(max_digits=10, decimal_places=2, verbose_name="运费")
    pay_method = models.SmallIntegerField(choices=PAY_METHODS_CHOICES, default=1 ,verbose_name="支付方式")
    status = models.SmallIntegerField(choices=ORDER_STATUS_CHOICES, default=1, verbose_name="订单状态")
    
    
    class Meta:
        db_table = "tb_order_info"
        verbose_name = "订单基本信息"
        verbose_name_plural = verbose_name
        

class OrderGoods(BaseModel):
    """商品订单"""
    
    SCORE_CHOICES = (
        (0, '0分'),
        (1, '20分'),
        (2, '40分'),
        (3, '60分'),
        (4, '80分'),
        (5, '100分'),
    )
    
    oder = models.ForeignKey(OrderInfo, related_name='skus', on_delete=models.CASCADE, verbose_name="订单")
    sku = models.ForeignKey(SKU,on_delete=models.PROTECT, verbose_name="订单商品")
    count = models.IntegerField(default=1, verbose_name="数量")
    price = models.DecimalField(max_digits=10, decimal_places=2, verbose_name="单价")
    comment = models.TextField(default="", verbose_name="评价信息")
    score = models.SmallIntegerField(choices=SCORE_CHOICES, default=5 ,verbose_name="满意度评分")
    is_anonymous = models.BooleanField(default=False, verbose_name="是否匿名评价")
    is_comment = models.BooleanField(default=False, verbose_name="是否评价了")
    
    class Meta:
        db_table = "tb_order_goods"
        verbose_name = "订单商品"
        verbose_name_plural = verbose_name
```

### 生成数据表

执行命令生成数据迁移记录
```bash
┌──(leazhi㉿kali-desktop)-[/data/…/python3-django-small_haoke/haoke/haoke/apps]
└─$ python ../../manage.py makemigrations
Migrations for 'orders':
  orders/migrations/0001_initial.py
    - Create model OrderInfo
    - Create model OrderGoods
```

执行命令创建数据表
```bash
┌──(leazhi㉿kali-desktop)-[/data/…/python3-django-small_haoke/haoke/haoke/apps]
└─$ python ../../manage.py migrate       
Operations to perform:
  Apply all migrations: admin, areas, auth, contents, contenttypes, goods, orders, sessions, users
Running migrations:
  Applying orders.0001_initial... OK
```

## 展示订单

**查询订单数据**

### 创建商品订单序列化器

在子应用 orders 目录下面创建序列化器文件  serializers.py ,内容为：
```python
# orders/serializers.py

from rest_framework import serializers
from goods.models import SKU
class CartSKUSerializers(serializers.ModelSerializer):

    count = serializers.IntegerField(label="数量")
    class Meta:
        model = SKU
        fields = ('id', 'name', 'default_image_url', 'price', 'count')


class OrderSettlementSerializers(serializers.Serializer):
    """订单结算"""
    freight = serializers.DecimalField(max_digits=10, decimal_places=2, label="运费")

    skus = CartSKUSerializers(many=True)
```

### 创建商品订单查询视图方法

编辑子应用 orders 目录下面的 views.py 文件，添加如下代码：
```python
# orders/views.py

from django.shortcuts import render

# Create your views here.
from rest_framework.views import APIView
from rest_framework.permissions import IsAuthenticated
from decimal import Decimal
from django_redis import get_redis_connection
from rest_framework.response import Response
from goods.models import SKU
from .serializers import OrderSettlementSerializers


class OrderSettlementView(APIView):
    # 验证用户是否为认证的用户
    permission_classes = (IsAuthenticated,)

    def get(self, request):
        user = request.user

        # 从购物车里面获取用户勾选的商品信息
        redis_conn = get_redis_connection("carts")
        redis_cart = redis_conn.hgetall('cart_%d' % user.id)
        cart_selected = redis_conn.smembers('selected_%d' % user.id)
        # 把 count 数量的属性放到 sku 里面，给 sku 里面添加一个 数量 的属性
        cart = {}
        for sku_id in cart_selected:
            # 类似于 {sku_id: sku_id} === > {1: 1, 2: 2}
            cart[int(sku_id)] = int(redis_cart(sku_id))
        skus = SKU.objects.filter(id__in=cart.keys())
        # count 属性
        for sku in skus:
            sku.count = cart[sku.id]
        freight = Decimal('10.00')
        ser = OrderSettlementSerializers({'freight':freight , 'skus': skus})
        return Response(ser.data)
```

### 创建商品订单访问路由

在子应用 orders 目录下面创建路由 urls.py 文件，内容如下：
```python
# orders/urls.py

from django.urls import path, re_path
from . import views

urlpatterns = [
    path('orders/settlement/', views.OrderSettlementView.as_view())
]
```

### 项目总路由注册 orders 子路由

编辑项目总路由配置文件 urls.py ，在 `urlpatterns = [ .. ]` 列表中添加子应用 orders 的访问路由：
```bash
# haoke/urls.py

...

urlpatterns = [
    ...
    path('', include('orders.urls')),
]
```

## 提交订单

**保存订单数据**

### 创建商品订单保存的序列化器

编辑子应用 orders 下的 serializers.py 文件,添加商品订单保存序列化器代码:
```bash
# orders/serializers.py

...

from datetime import datetime
from django.db import transaction
from django_redis import get_redis_connection
from decimal import Decimal
from .models import OrderInfo, OrderGoods

...

class SaveOrderSerializers(serializers.ModelSerializer):
    """下单数据序列化器"""
    class Meta:
        model = OrderInfo
        fields = ('order_id', 'address', 'pay_method')
        read_only_fields = ('order_id')
        extra_kwargs = {
            'address': {
                'write_only': True,
                'required': True
            },
            'pay_method': {
                'write_only': True,
                'required': True
            }
        }

    def create(self, validated_data):
        """
        保存订单

        这里涉及以下表：订单信息表， sku, spu, 订单商品表。其中涉及库存问题(用户抢购商品)
        """
        # 获取当前用户
        user = self.context['request'].user

        # 生成订单编号 --- 当前的时间和在用户的 id 前面加 9个数字
        order_id = datetime.now().strftime('%Y%m%d%H%M%S') + '%09d' % user.id

        # 收货地址
        address = validated_data.get('address')

        # 支付方式
        pay_method = validated_data['pay_method']

        # 订单状态
        status = ( OrderInfo.ORDER_STATUS_ENUM['UNPAID'] if pay_method == OrderInfo.PAY_METHODS_ENUM['ALIPAY'] else OrderInfo.ORDER_STATUS_ENUM['UNSEND'])

        # 开启事务
        with transaction.atomic():
            # 创建保存点
            save_point = transaction.savepoint()
            try:
                # 保存订单基本信息数据
                orderInfo = OrderInfo.objects.create(
                    order_id = order_id,
                    user = user,
                    address = address,
                    total_count = 0,
                    total_amount = Decimal('0.00'),
                    freight = Decimal('0.00'),
                    pay_method = pay_method,
                    status = status,
                )

                # 从 redis 中获取购物车结算商品数据 sku, 修改 sku 中库存销量
                redis_conn = get_redis_connection("carts")
                cart_dict_redis = redis_conn.hgetall('cart_%d' % user.id)
                selected_ids = redis_conn.smsmbers('selected_%d' % user.id)

                # 遍历结算商品
                for sku_id_bytes in selected_ids:
                    while True: # 让用户对同一个商品有无限次下单的机会,直到库存不足为止
                        # 获取 sku 的数据
                        sku = SKU.objects.get(id = sku_id_bytes)

                        # 获取当前商品的数量
                        but_count = int(cart_dict_redis[sku_id_bytes])

                        # 查询一下当前的库存和当前的销量
                        origin_sales = sku.sales
                        origin_stock = sku.stock

                        # 判断商品库存是否充足
                        if but_count > origin_stock:
                            raise serializers.ValidationError('库存不足')

                        # 减少商品库存,增加商品销量
                        # 计算新的库:origin_stock -- 存销量:origin_sales
                        new_sales = origin_sales + but_count
                        new_stock = origin_stock - but_count

                        # 更新 sku 的数据和 sku 中的销量
                        result = SKU.objects.filter(stock = origin_stock, id=sku_id_bytes).update(stock = new_stock, sales = new_sales)
                        if result == 0:
                            continue

                        # spu 中的销量
                        spu = sku.goods
                        spu.sales = sku.sales + but_count
                        spu.save()

                        # 保存订单数据
                        OrderGoods.objects.create(
                            order = orderInfo,
                            sku = sku,
                            count = but_count,
                            price = sku.price
                        )

                        orderInfo.total_count += but_count
                        orderInfo.total_amount += (sku.price * but_count)

                        break

                    orderInfo.total_amount += orderInfo.freight
                    orderInfo.save()
            except Exception:
                # 进行回滚
                transaction.savepoint_rollback(save_point)
                raise serializers.ValidationError('库存不足')
            else:
                transaction.savepoint_commit(save_point)

        pl = redis_conn.pipeline()
        pl.hdel('cart_%d' % user.id, *selected_ids )
        pl.srem('selected_%d' % user.id, *selected_ids)
        pl.execute()

        return orderInfo
```

### 创建商品订单保存的视图

编辑子应用 orders 目录下的 views.py 文件,添加:
```python
# orders/views.py

...

from .serializers import OrderSettlementSerializers, SaveOrderSerializers
from rest_framework.generics import CreateAPIView

...

class SaveOrderView(CreateAPIView):
    permission_classes = [IsAuthenticated]
    serializer_class = SaveOrderSerializers
```

### 创建商品订单保存的访问路由

在子应用 orders 目录下面创建路由 urls.py 文件，内容如下：
```python
# orders/urls.py

from django.urls import path, re_path
from . import views

urlpatterns = [
    path('orders/settlement/', views.OrderSettlementView.as_view()),
    path('orders/', views.SaveOrderView.as_view()),
]
```