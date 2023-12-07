---
title: django-small 系列021-历史记录
author: leazhi
tags:
  - [django]
  - [python]
categories:
  - [django]
  - [python]
date: 2023-09-23 13:02:34
cover: https://hexo.linuser.com/gallery/code/1181671.jpg
discription: 
keywords: python, django
password: 
message: 
---

## 用 redis 存浏览记录

编辑项目配置文件 dev.py , 在文件中的 CACHES = { ... } 字典中增加 保存浏览记录的配置：
```python
# settings/dev.py

...

CACHES = {
    'default': {
        'BACKEND': 'django_redis.cache.RedisCache',
        'LOCATION': 'redis://192.168.3.254:22652/9',
        'OPTIONS': {
            'CLIENT_CLASS':'django_redis.client.DefaultClient',
            "PASSWORD": "Zmi2hU4hGkdroBl7wWm/DszVaWWqrgNuWwD2tmHO",
        }
    },
    'code': {
        'BACKEND': 'django_redis.cache.RedisCache',
        'LOCATION': 'redis://192.168.3.254:22652/10',
        'OPTIONS': {
            'CLIENT_CLASS':'django_redis.client.DefaultClient',
            "PASSWORD": "Zmi2hU4hGkdroBl7wWm/DszVaWWqrgNuWwD2tmHO",
        }
    },
    'verify_code': {
        "BACKEND": "django_redis.cache.RedisCache",
        'LOCATION': 'redis://192.168.3.254:22652/2',
        'OPTIONS': {
            'CLIENT_CLASS': 'django_redis.client.DefaultClient',
            "PASSWORD": "Zmi2hU4hGkdroBl7wWm/DszVaWWqrgNuWwD2tmHO",
        }
    },
    # 保存浏览历史记录
    'history': {
        "BACKEND": "django_redis.cache.RedisCache",
        'LOCATION': 'redis://192.168.3.254:22652/2',
        'OPTIONS': {
            'CLIENT_CLASS': 'django_redis.client.DefaultClient',
            "PASSWORD": "Zmi2hU4hGkdroBl7wWm/DszVaWWqrgNuWwD2tmHO",
        }
    }
}

...
```

## 后端实现

### 编写用户序列化器

编辑子应用 users 目录下 serializers.py 文件，在该文件中添加：
```python
# users/serializers.py

...
from goods.models import  SKU

...


class AddUserBrowsingHistorySerializer(serializers.Serializer):
    """
    添加用户浏览历史序列化器
    """
    sku_id = serializers.IntegerField(label="商品 SKU 编号", min_value=1)
    # def validate_sku_id(self, value):
    #     """
    #     检查 sku_id 是否存在
    #     """
    #     try:
    #         SKU.objects.get(id=value)
    #     except SKU.DoesNotExist:
    #         raise serializers.ValidationError('该商品不存在')
    #     return value

    def create(self, validated_data):
        """保存"""
        # 查看商品是否存在
        try:
            SKU.objects.get(id=validated_data['sku_id'])
        except SKU.DoesNotExist:
            raise serializers.ValidationError('该商品不存在')

        # 根据当前登录的用户去保存对应的商品数据（sku_id 对应的数据）
        # self.context 是一个字典对象。 保存的是请求参数中的所有数据
        user_id = self.context['request'].user
        sku_id = validated_data['sku_id']

        redis_conn = get_redis_connection("history")
        pl = redis_conn.pipeline()

        # 移除已经存在的本商品浏览记录
        pl.lrem("history_%s" % user_id, 0, sku_id)

        # 添加新的记录
        pl.lpush("history_%s" % user_id, sku_id)

        # 只保存最多5条记录
        # pl.ltrim("history_%s" % user_id, 0, constants.USER_BROWSING_HISTORY_COUNTS_LIMIT-1)
        pl.ltrim("history_%s" % user_id, 0, 4)

        pl.execute()

        return validated_data
```

### 编写用户视图类

编辑子应用  users 目录下的 views.py 文件，添加：
```python
# users/view.py

...

from rest_framework.generics import CreateAPIView
from .serializers import AddUserBrowsingHistorySerializer

...

class UserBrowsingHistoryView(CreateAPIView):
    serializer_class = AddUserBrowsingHistorySerializer
    permission_classes = [IsAuthenticated]
```

### 编写访问路由：
编辑子应用目录 users 下的 urls.py 文件，添加访问路由：
```python
# users/urls.py

from django.urls import path, re_path
from . import views
from rest_framework_jwt.views import obtain_jwt_token
from rest_framework.routers import DefaultRouter

urlpatterns = [
    # path('register/', views.RegisterView.as_view(), name='register'),
    re_path(r'^usernames/(?P<username>\w{5,20})/count/$', views.UsernameCountView.as_view()),
    re_path(r'^mobiles/(?P<mobile>)1[3-9]\d{9}/count/$', views.MobileCountView.as_view()),
    path('users/',views.UserView.as_view()),        # users 路由
    path('authorizations/', obtain_jwt_token),
    path('user/', views.UserDetailView.as_view()),
    path('email/', views.EmailView.as_view()),
    path('emails/verification/', views.VerifyEmailView.as_view()),
    path('password/', views.PassWord.as_view()),
    # 添加历史记录路由
    path('browse_histories/', views.UserBrowsingHistoryView.as_view())
]

# 因为是是视图集，
# 路由需要使用DefaultRouter
router = DefaultRouter()
router.register('addresses', views.AddressViewSet, basename='addresses')
urlpatterns += router.urls
```

## 记录展示

编辑子应用  users 目录下的 views.py 文件，在 浏览历史记录类下面添加历史数据展示方法：
```python
# users/views.py

...

from rest_framework.generics import CreateAPIView, GenericAPIView
from .serializers import AddUserBrowsingHistorySerializer
from django_redis import get_redis_connection
from goods.models import SKU
from goods.serializers import SKUSerializers

...

class UserBrowsingHistoryView(CreateAPIView):
    serializer_class = AddUserBrowsingHistorySerializer
    permission_classes = [IsAuthenticated]

    # 历史记录展示--从 redis 里面查询，不能使用 django 子类或扩展类
    def get(self, request):
        """
        查询
        """
        user_id = request.user
        redis_conn = get_redis_connection("history")

        # 获取浏览记录
        history = redis_conn.lrange("history_%s" % user_id, 0, 4)

        # 查询数据中的商品数据
        skus = []
        for sku_id in history:
            sku = SKU.objects.get(id=sku_id)
            skus.append(sku)

            # 根据 id ---sku_id 去查询其他的字段数据 --- 字典类型
        ser = SKUSerializers(skus, many=True)
        # 返回数据 --- 序列化里面的数据
        return Response(ser.data, satus=200)
```