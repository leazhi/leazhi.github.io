---
title: django-small 系列010-邮箱验证及收获地址
author: leazhi
tags:
  - [django]
  - [python]
categories:
  - [django]
  - [python]
date: 2023-09-05 09:49:27
cover: https://hexo.linuser.com/gallery/code/1181671.jpg
discription: 
keywords: 
password: 
message: 
---

## 邮箱验证

### 准备工作
1.安装 itsdangerous 模块(它提供加密解密的方法)：
```bash
leazhi@ubuntuhome:~$ workon haoke_small
(haoke_small) leazhi@ubuntuhome:~$itsdangerous==1.1.0
Looking in indexes: https://pypi.tuna.tsinghua.edu.cn/simple
Collecting itsdangerous
  Downloading https://pypi.tuna.tsinghua.edu.cn/packages/68/5f/447e04e828f47465eeab35b5d408b7ebaaaee207f48b7136c5a7267a30ae/itsdangerous-1.1.0-py3-none-any.whl (15 kB)
Installing collected packages: itsdangerous
Successfully installed itsdangerous-1.1.0
```

**注意**：
这里 itsdangerous 的版本要低于 2.0.0 ，否则在后面使用 itsdangerous 模块会报：
```bash
  File "/home/leazhi/small/haoke/haoke/apps/users/serializers.py", line 17, in <module>
    from celery_tasks.email.tasks import send_verify_email
ImportError: cannot import name 'TimedJSONWebSignatureSerializer' from 'itsdangerous' (/home/leazhi/Desktop/python3/Django/Project/small/haoke/haoke/apps/users/models.py)
```

**解决方法**：

请参考：https://blog.csdn.net/weixin_43863487/article/details/123784400


### 邮件发送
1.编辑子应用 users 目录下的 models.py 文件，在该文件中导入 itsdangerous 模块中的 TimedJSONWebSignatureSerializer ，然后在 Uesr model 类下面添加一个生成邮箱链接的方法，如下：
```python
# users/models.py

...

from django.conf import settings
from itsdangerous import TimedJSONWebSignatureSerializer as TJWSerializer

...

    # 生成邮箱验证链接
    def generate_verify_email_url(self):
        # # 生成一个随机字符串
        # key = get_random_string(length=32)
        # # 拼接验证链接
        # verify_url = settings.EMAIL_VERIFY_URL + '?key=' + key
        # # 保存随机字符串到数据库
        # self.verify_key = key
        # # 保存验证链接到数据库
        # self.save()
        # return verify_url

        # 1.创建一个加密的序列化器 --- jsonwebserializer
        ser = TJWSerializer(secret_key=settings.SECRET_KEY, expires_in=3600)

        # 2.生成加密的字典
        data = {
            'user_id': self.id,
            'email': self.email,
        }

        # 3.加密
        token = ser.dumps(data).decode()

        # 4.拼接验证链接
        return 'https://www.meiduo.site:8080/success_verify_email.html?token=' + token

```

2.编辑子应用 users 目录下的 serializers , 先导入异步任务中的 send_varify_email 方法，然后在 EmailSerializer 类的 update 方法中添加邮件地址发送的调用，如下：
```python
# users/serializers.py

...

from celery_tasks.email.tasks import send_verify_email

...

class EmailSerializer(serializers.ModelSerializer):
  """
  邮箱修改序列化器
  """

  class Meta:
    model = User

    fields = ('id', 'email',)

    # 验证 email 地址必须是唯一的
    extra_kwargs = {
      'email': {
        'required': True,
        # 'allow_blank': False,
        # 'error_messages': {
        #   'required': '邮箱不能为空',
        #   'blank': '邮箱不能为空',
        #   'invalid': '邮箱格式错误',
        # }
      }
    }

  # 修改 email 地址方法
  def update(self, instance, validated_data):
    """
    重写更新方法
    """
    # 更新邮箱
    instance.email = validated_data['email']
    instance.save()

    # 调用执行的发送邮件地址 --- 异步任务
    verify_url = instance.generate_verify_email_url()
    send_verify_email(instance.email, verify_url = verify_url)
    return instance

```

### 激活邮箱

#### 解密 token:
1.编辑子应用 users 目录下的 models.py 文件，导入 itsdangerous 的解密方法 BadData, 然后添加如下代码：
```python
# users/models.py

...

from itsdangerous import TimedJSONWebSignatureSerializer as TJWSerializer, BadData

...

    @staticmethod
    def check_verify_email_token(token):
        # 1.创建一个加密的序列化器 --- jsonwebserializer
        ser = TJWSerializer(secret_key=settings.SECRET_KEY, expires_in=3600)

        # 解密
        try:
            data = ser.loads(token)
        except BadData:
            return None
        else:
            id = data.get('user_id')
            email = data.gete('email')

            try:
                user = User.objects.get(id=id, email=email)
            except User.DoesNotExist:
                return None
            else:
                return user
```

#### 编写邮件验证视图

1.编辑子应用 users 目录下的 views 文件，添加邮箱验证视图，如下：
```python
# users/views.py

...

from rest_framework import status

...

class VerifyEmailView(View):
  """
  邮箱验证视图
  """

  # 获取 token 数据
    def get(self, request):
        token = request.query_params.get('token')
        if not token:
            return Response({'message': '缺少 token'}, status=status.HTTP_400_BAD_REQUEST)

        # 有就验证
        user = User.check_verify_email_token(token)
        
        # 修改邮箱的验证状态
        if user is None:
            return Response({'message': '链接信息无效'}, status=status.HTTP_400_BAD_REQUEST)
        else:
            user.email_active= True
            user.save()
            return Response({'message': 'OK'})
```

#### 添加邮箱验证的路由
1.编辑子应用 users 目录下的 urls 文件，添加邮箱验证的路由，如下：
```python

# users/urls.py...

urlpatterns = [
  ...
  path('emails/verification/', views.VerifyEmailView.as_view()),
]
```

## 收货地址

### 创建子应用 areas
1.创建子应用 areas，并添加到 settings.py 的 INSTALLED_APPS 中:

1.1.创建子应用 areas:
```bash
(haoke_small) leazhi@ubuntuhome:~/small/haoke/haoke/apps$ python ../../manage.py startapp areas
```

1.2.将创建的子应用添加到配置文件 dev.py 中;
```python
# haoke/setting/dev.py

...

INSTALLED_APPS = [
    ...
    'areas',
]

...
```

### 创建 areas 的模型类：

1.编辑子应用 areas 目录下的 models.py 文件，创建模型类，如下：
```python
# areas/models.py

from django.db import models


class Area(models.Model):
    name = models.CharField(max_length=20, verbose_name='名称')
    # 自关联键
    parent = models.ForeignKey('self', null=True, blank=True, verbose_name='上级行政区域', on_delete=models.SET_NULL, related_name='subs')

    class Meta:
        db_table = 'tb_areas'
        verbose_name = '行政区域'
        verbose_name_plural = verbose_name

    def __str__(self):
        return self.name
```

2.执行命令进行数据迁移：
```bash
(haoke_small) leazhi@ubuntuhome:~/small/haoke/haoke/apps$ python ../../manage makemigrations
(haoke_small) leazhi@ubuntuhome:~/small/haoke/haoke/apps$ python ../../manage migrate
```

3.执行命令，往数据库中导入数据：
```bash
mysql -uroot -p small < areas.sql
```

### 创建序列化器

1.在子应用 areas 目录下新建 serializers.py 文件，编辑如下内容：
```python
# areas/serializers.py  
from rest_framework import serializers
from .models import Area

class AreaModelSerializer(serializers.ModelSerializer):
    class Meta:
        model = Area
        fields = ('id', 'name')

class SubAreaModelSerializer(serializers.ModelSerializer):
    parent = serializers.StringRelatedField()

    # 从省序列化器里面反序列化市的编号进行查询
    subs = AreaModelSerializer(many=True, read_only=True)
    class Meta:
        model = Area
        fields = ('id', 'name', 'subs')
```

### 创建视图

1.编辑子应用 areas 目录下创建 views.py 文件，并编辑如下内容：
```python   
# areas/models.py

from django.shortcuts import render
from rest_framework.viewsets import ModelViewSet
from areas.models import Area
from areas.serializers import AreaModelSerializer, SubAreaModelSerializer

class AreasViewSet(ModelViewSet):
    pagination_class = None

    # 提供数据集
    def get_queryset(self):
        if self.action == 'list':
            return Area.objects.filter(parent=None)
        else:
            return Area.objects.all

    # 提供序列化器
    def get_serializer_class(self):
        if self.action == 'list':
            return AreaModelSerializer
        else:
            return SubAreaModelSerializer

    # 获取省列表
    def list(self, request, *args, **kwargs):
        queryset = self.get_queryset()
        serializer = self.get_serializer_class()(queryset, many=True)
        return render(request, serializer.data, status=200)
```

### 创建子应用的访问路由

1.子应用 areas 目录下的新建 urls.py 文件，添加如下内容：
```python
# areas/urls.py 

from django.urls import path, re_path
from . import views
from rest_framework.routers import DefaultRouter

urlpatterns = [

]

route = DefaultRouter()
route.register(r'areas', views.AreasViewSet, basename='areas')
urlpatterns += route.urls
```

## 总路由添加子应用的路由
1.修改项目总路由，添加子应用的路由
```python
# haoke/urls.py

from django.contrib import admin
from django.urls import path, include

urlpatterns = [
    path('admin/', admin.site.urls),
    path('', include('users.urls')),
    path('', include('verifications.urls')),
    path('', include('areas.urls')),
]
```