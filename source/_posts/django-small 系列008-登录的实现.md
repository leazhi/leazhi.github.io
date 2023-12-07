---
title: django-small 系列008-登录的实现
author: leazhi
tags:
  - [django]
  - [python] 
categories:
  - [django]
  - [python] 
date: 2023-08-31 10:40:16
cover: https://hexo.linuser.com/gallery/code/1181671.jpg
discription: 
keywords: 
password: 
message: 
---

## Token 的实现

### 生成 token

1.安装 djangorestframework-jwt 包：
```python
leazhi@ubuntuhome:~$ workon haoke_small
(haoke_small) leazhi@ubuntuhome:~$ pip3 install djangorestframework-jwt
Looking in indexes: https://pypi.tuna.tsinghua.edu.cn/simple
Collecting djangorestframework-jwt
  Downloading https://pypi.tuna.tsinghua.edu.cn/packages/2b/cf/b3932ad3261d6332284152a00c3e3a275a653692d318acc6b2e9cf6a1ce3/djangorestframework_jwt-1.11.0-py2.py3-none-any.whl (13 kB)
Collecting PyJWT<2.0.0,>=1.5.2 (from djangorestframework-jwt)
  Downloading https://pypi.tuna.tsinghua.edu.cn/packages/87/8b/6a9f14b5f781697e51259d81657e6048fd31a113229cf346880bb7545565/PyJWT-1.7.1-py2.py3-none-any.whl (18 kB)
Installing collected packages: PyJWT, djangorestframework-jwt
Successfully installed PyJWT-1.7.1 djangorestframework-jwt-1.11.0
```     

2.在 dev.py 文件中添加如下配置：
```python
# haoke/settings/dev.py

...

import datetime
...

REST_FRAMEWORK = {
    'DEFAULT_AUTHENTICATION_CLASSES': (
        'rest_framework_jwt.authentication.JSONWebTokenAuthentication',
        'rest_framework.authentication.SessionAuthentication',
        'rest_framework.authentication.BasicAuthentication',
    ),
    # 'DEFAULT_PERMISSION_CLASSES': (
    #     'rest_framework.permissions.IsAuthenticated',
    # ),
    # 'DEFAULT_PAGINATION_CLASS': 'rest_framework.pagination.PageNumberPagination',
    # 'PAGE_SIZE': 10
}

JWT_AUTH = {
    'JWT_EXPIRATION_DELTA': datetime.timedelta(days=1),
    # 'JWT_ALLOW_REFRESH': True,
    # 'JWT_REFRESH_EXPIRATION_DELTA': datetime.timedelta(days=7),
    # 'JWT_AUTH_HEADER_PREFIX': 'JWT',
}
```

3.编辑子应用 users 下的序列化器文件 serializers 文件，加入 token 生成代码：
```pythobn
# haoke/users/serializers.py

from rest_framework_jwt.settings import api_settings  # 用户发送过来的数据进行签名（加密）
import re
from users.models import User
from django_redis import get_redis_connection
from rest_framework import serializers


class CreateUserSerializer(serializers.ModelSerializer):
    password = serializers.CharField(write_only=True, label='password')
    password2 = serializers.CharField(write_only=True, label='password2')
    sms_code = serializers.CharField(write_only=True, label='sms_code')
    allow = serializers.CharField(write_only=True, label='allow')

    # 生成 token,前段需要 token 进行认证
    # 增加 toker 字段
    token = serializers.CharField(label="token", read_only=True)

    class Meta:
        model = User
        fields = ('id', 'username', 'password', 'password2', 'sms_code', 'allow', 'mobile', 'token')

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

        # # 获取数据
        # username = validated_data['username']
        # password = validated_data['password']
        # password2 = validated_data['password2']
        mobile = validated_data['mobile']
        # sms_code = validated_data['sms_code']
        # allow = validated_data['allow']

        # 1.校验数据

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

        # 2.保存数据

        # 删除不需要保存的数据
        del validated_data['password2']
        del validated_data['sms_code']
        del validated_data['allow']

        # 获取反序列化后的数据
        # super() 继承序列化器类
        # 创建对象
        user = super().create(validated_data)

        # 调用 django 的认证系统加密密码
        # 密码保存之前需要进行加密处理
        # 加密后的密码保存到数据库
        user.set_password(validated_data['password'])
        user.save()

        # 3.生成token
        # 使用 jwt 中的头部模块 和 编码模块
        jwt_payload_handler = api_settings.JWT_PAYLOAD_HANDLER
        jwt_encode_handler = api_settings.JWT_ENCODE_HANDLER

        # 构造 token,传入用户的数据作为登录保持
        payload = jwt_payload_handler(user)
        token = jwt_encode_handler(payload)
        user.token = token                          # 将 token 值添加到 user 字典中

        # 返回数据
        return user
```

## 用户名或手机号实现登录

1.编辑子应用 users 下的路由文件 urls.py ,导入 rest_framework 中的 JWT 视图 obtain_jwt_token 方法:
```python
# users/urls.py

from django.urls import path, re_path
from . import views
from rest_framework_jwt.views import obtain_jwt_token

urlpatterns = [
    # path('register/', views.RegisterView.as_view(), name='register'),
    re_path(r'^usernames/(?P<username>\w{5,20})/count/$', views.UsernameCountView.as_view()),
    re_path(r'^mobiles/(?P<mobile>)1[3-9]\d{9}/count/$', views.MobileCountView.as_view()),
    path('users/',views.UserView.as_view()),        # users 路由
    path(r'^authorizations/$', obtain_jwt_token)
]
```

2.在子应用 users 下新建一个定义登录方法的 utils.py 文件，并编写如下代码：
```python
# users/utils.py

import re
from django.contrib.auth.backends import ModelBackend
from users.models import User

def jwt_response_payload_handler(token, user=None, request=None):
    """
    处理JWT响应负载的函数
    """
    return {
        'token': token,
        'user_id': user.id,
        'username': user.username
    }

def get_user_by_account(account):
    """
    根据账号获取用户对象
    """
    try:
        if re.match('^1[3-9]\d{9}$', account):
            user = User.objects.get(mobile=account)
        else:
            user = User.objects.get(username=account)
    except User.DoesNotExist:
        return None
    else:
        return user

class UsernameMobileAuthModelBackend(ModelBackend):
    """
    自定义的用户名/手机号认证后端
    """
    def authenticate(self, request, username=None, password=None, **kwargs):
        """
        根据用户名/手机号和密码进行认证
        """
        user = get_user_by_account(username)
        if user is not None and user.check_password(password):
            return user
```

3.编辑配置文件 dev.py ,在 JWT_AUTH 字典中添加如下代码：
```python
# haoke/settings/dev.py

...

JWT_AUTH = {
    'JWT_EXPIRATION_DELTA': datetime.timedelta(days=1),
    'JWT_RESPONSE_PAYLOAD_HANDLER': 'users.utils.jwt_response_payload_handler',
}


# 在配置文件中告知 django 使用我们自定义的认证后端
AUTHENTICATION_BACKENDS = {
    'users.utils.UsernameMobileAuthModelBackend'
}
```

注意： 如果启动项目报 `ImportError: Could not import 'rest_framework_jwt.authentication.JSONWebTokenAuthentication' for API setting 'DEFAULT_AUTHENTICATION_CLASSES'. ImportError: cannot import name 'smart_text' from 'django.utils.encoding' ` 错误。请根据文章:[django 项目启动报错-cannot import name 'smart_text' from 'django.utils.encoding'](./django%20项目启动报错-cannot%20import%20name%20'smart_text'%20from%20'django.utils.encoding'.md) 中的方法解决。

到此为止。 django 登录系统已经搭建完毕。

