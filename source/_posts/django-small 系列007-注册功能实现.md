---
title: django-small 系列007-注册功能实现
author: leazhi
tags:
  - [django]
  - [python]
categories:
  - [django]
  - [python]
date: 2023-08-28 20:13:09
cover: https://hexo.linuser.com/gallery/code/1181671.jpg
discription: 
keywords: 
password: 
message: 
---

## 实现用户注册

### 序列化器
```python
# users/serializers.py

import re
from users.models import User
from django_redis import get_redis_connection
from rest_framework import serializers

class CreateUserSerializer(serializers.ModelSerializer):
    password = serializers.CharField(write_only=True, label='password)
    password2 = serializers.CharField(write_only=True, label='password2')
    sms_code = serializers.CharField(write_only=True, label='sms_code')
    allow = serializers.CharField(write_only=True, label='allow')

    class Meta:
        model = User
        fields = ('id','username', 'password', 'password2', 'sms_code', 'allow', 'mobile')
    
        # 对用户名进行校验
        extar_kwargs = {
            'username': {
                'min_length': 5,
                'max_length': 20,
                'error_messages': {
                    'min_length': '用户名长度不能小于5',
                    'max_length': '用户名长度不能大于20',
                }
            }
        }
    # 保存数据
    def create(self, validated_data):

        ## 1.获取数据
        # username = validated_data['username']
        # password = validated_data['password']
        # password2 = validated_data['password2']
        mobile = validated_data['mobile']
        # sms_code = validated_data['sms_code']
        # allow = validated_data['allow']

        ## 2.校验数据

        # 验证手机号是否符合要求
        if not re.match(r'1[3-9]\d{9}$', validated_data['mobile']):
            # raise 返回数据（不会将数据返回显示到前端）。 return 返回数据(它会返回出去给前段)
            raise serializers.ValidationError('手机号格式错误')
        # 判断手机号是否注册
        try:    
            user = User.objects.get(mobile=mobile)
        except User.DoesNotExist:
            pass
        else:
            raise serializers.ValidationError('手机号已注册')

        # 同意用户勾选
        if validated_data['allow'] != 'true':
            raise serializers.ValidationError('请同意用户协议')

        # 密码校验
        if validated_data['password'] != validated_data['password2']:
            raise serializers.ValidationError('两次密码不一致')

        # 验证码验证
        redis_conn = get_redis_connection('verify_codes')   
        # 获取短信验证码
        redis_sms_code = redis_conn.get('sms_%s' % mobile)
        if redis_sms_code.decode() != validated_data['sms_code']:
            raise serializers.ValidationError('短信验证码错误')

        ## 3.保存数据

        # 删除不需要保存的数据
        del validated_data['password2']
        del validated_data['sms_code']
        del validated_data['allow']


        # 获取反序列化后的数据
        # super() 继承序列化器类
        # 创建对象
        user=super().create(validated_data)     
        # 生成token

        # 密码保存之前需要进行加密处理
        # 加密后的密码保存到数据库
        user.set_password(validated_data['password'])
        user.save()

        # # 生成token
        # serializer = TokenSerializer(data={
        #     'user': user.id
        # })
        # serializer.is_valid(raise_exception=True)
        # token = serializer.validated_data['token']

        ## 4.返回数据
        return user
        # return {
        #     'mobile': user.mobile,
        #     'username': user.username,
        #     'token': token
        # }
```


### 路由
```python
# users/urls.py

from django.urls import path, re_path
from . import views

urlpatterns = [
    # path('register/', views.RegisterView.as_view(), name='register'),
    re_path(r'^usernames/(?P<username>\w{5,20})/count/$', views.UsernameCountView.as_view()),
    re_path(r'^mobiles/(?P<mobile>)1[3-9]\d{9}/count/$', views.MobileCountView.as_view()),
    path('users/',views.UserView.as_view()),        # users 路由
]

```

### 视图
```python
# users/views.py

from rest_framework.generics import CreateAPIView

from users.serializers import CreateUserSerializer

...

class UserView(CreateAPIView):
    """
    用户注册---把数据添加到数据库中
    """

    # 指定序列化器
    serializer_class = CreateUserSerializer

```