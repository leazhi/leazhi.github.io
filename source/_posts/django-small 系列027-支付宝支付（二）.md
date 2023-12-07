---
title:  django-small 系列027-支付宝支付（二）
author: leazhi
tags:
  - [python]
  - [django]
categories:
  - [python]
  - [django]
date: 2023-10-16 20:05:40
cover:  https://hexo.linuser.com/gallery/code/1181671.jpg
discription: 
keywords: 
password: 
message: 
---

## 订单状态

订单状态的保存

### 创建支付状态视图

编辑子应用 payment 目录下的 views.py 文件，添加支付状态试图：
```python
# payment/vuews.py

...

from django.conf import settings

...


# 修改订单的状态和保存支付宝的支付编号
class PaymentStatusView(APIView):
    def put(self, request):
        data = request.query_params.dict()
        signature = data.pop('sign')        # 删除不需要的加密数据
        # 获取支付宝发送过来的支付编号

        app_private_key_string = open('/data/gitlab/python3-django-small_haoke/haoke/haoke/apps/payment/keys/app_private_key.pem').read()
        alipay_public_key_string = open('/data/gitlab/python3-django-small_haoke/haoke/haoke/apps/payment/keys/alipay_public_key.pem').read()
        # 构造支付宝链接的调用
        alipay = AliPay(
            appid = settings.ALIPAY_APPID,
            app_notify_url = None,
            app_private_key_string = app_private_key_string,
            alipay_public_key_string = alipay_public_key_string,
            sign_type = 'RSA2',
            debug = settings.ALIPAY_DEBUG
        )

        # 找支付宝进行验证，验证支付编号是否一致
        success = alipay.verify(data, signature)

        if success:
            # 保存订单编号和支付宝编号
            order_id = data.get('out_trade_no')
            trade_id = data.get('trade_no')

            Payment.object.create(
                order_id= order_id,
                trade_id = trade_id
            )

            # 修改支付状态
            OrderInfo.objects.filter(order_id=order_id, status=OrderInfo.ORDER_STATUS_ENUM['UNPAID']).update(status=OrderInfo.ORDER_STATUS_ENUM['UNSEND'])


            return Response({'trade_id': trade_id})
```

### 创建支付状态访问路由

编辑子应用 payment 目录下的 urls.py 文件，添加访问路由：
```bash
from django.urls import path, re_path
from .import views

urlpatterns = [
    re_path('orders/(?P<order_id>\d+)/payment/', views.PaymentView.as_view()),
    path('payment/status/', views.PaymentStatusView.as_view()),
]
```