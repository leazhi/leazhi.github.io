---
title: django-small 系列009-个人用户中心及Email
author: leazhi
tags:
  - [django]
  - [python]
categories:
  - [django]
  - [python]
date: 2023-09-02 10:16:19
cover: https://hexo.linuser.com/gallery/code/1181671.jpg
discription: 
keywords: 
password: 
message: 
---

## 个人用户中心

个人用户中心要实现展示当前登录的用户名、手机号及邮箱，同时可以修改密码、邮箱，以及退出登录。

### 在模型类中写入邮箱状态字段

1.编辑子应用 users 目录下的 models.py 文件，在 User 模型类中添加邮箱状态字段。

```python
# users/models.py

from django.db import models
from django.contrib.auth.models import AbstractUser     # 导入用户抽象模型类

# Create your models here.
class User(AbstractUser):       # 继承 AbstractUser
    '''
    用户模型类
    '''
    # 添加手机号字段
    mobile = models.CharField(max_length=11, unique=True, verbose_name='手机号')
    
    # 邮箱验证状态字段
    email_active = models.BooleanField(verbose_name='邮箱验证状态', default=False)  

    # 定义表明及在后台展示的名称      
    class Meta:
        db_table = 'tb_users'
        verbose_name = '用户'
        verbose_name_plural = verbose_name
```

2.在终端下执行如下命令进行数据迁移：
```bash
leazhi@ubuntuhome:~$ workon haoke_small
(haoke_small) leazhi@ubuntuhome:~$ cd small/haoke/

# 数据迁移
(haoke_small) leazhi@ubuntuhome:~/small/haoke$ python manage.py makemigrations
Migrations for 'users':
  haoke/apps/users/migrations/0002_user_email_active.py
    - Add field email_active to user

# 写入表字段：
(haoke_small) leazhi@ubuntuhome:~/small/haoke$ python manage.py migrate
Operations to perform:
  Apply all migrations: admin, auth, contenttypes, sessions, users
Running migrations:
  Applying users.0002_user_email_active... OK

```

### 数据查询及展示

#### 创建序列化器

1.编辑子应用 users 目录下的 serializers.py 文件，在该文件中导入 rest_framework.serializers 模块中的 Serializer 类，并创建一个 UserDetailSerializer 类，继承 rest_framework.serializers.ModelSerializer 类。
```python
# users/serializers.py

...

from rest_framework import serializers

...

class UserDetailSerializer(serializers.ModelSerializer):

    class Meta:
        model = User
        fields = ['id', 'username', 'mobile', 'email', 'email_active']

```

#### 创建用户详情视图类：
1.编辑子应用 users 目录下的 views.py 文件，在该文件中导入 rest_framework.generics 模块中的子模块  RetrieveAPIView，并创建一个 UserDetailAPIView 类，继承 rest_framework.generics.RetrieveAPIView 类。
```python 
# users/views.py

...

from rest_framework.generics import CreateAPIView, RetrieveAPIView
from rest_framework.permissions import IsAuthenticated    # 验证用户是否登录的模块
from users.serializers import UserDetailSerializer

...

class UserDetailView(RetrieveAPIView):

    # 指定序列化器
    serializer_class = UserDetailSerializer

    # 加一层验证 --- 只有验证通过后的用户才可以来到用户中心.  IsAuthenticated 该模块会自动认证用户是否是登录状态的
    permission_classes = [IsAuthenticated]

    # 获取用户的数据 --- 根据当前登录的用户进行查询.查询出来给序列化器
    def get_object(self):    # 获取详情页的数据 --- 从请求参数里面拿（从模型类里面获取）
        return self.request.user    # 让其返回指定指定查询数据

```

#### 添加访问路由

1.编辑子应用 users 目录下的 urls.py 文件，在该文件中添加访问个人中心路由 `user/`：
```python
# users/urls.py

... 
urlpatterns = [
    ...
    path('user/', views.UserDetailView.as_view()),
]
```
## 邮箱与验证

### Email(邮箱)地址修改

#### 创建邮箱修改视序列化器：

1.编辑子应用 users 目录下的 serializers.py 文件，在该文件中导入 rest_framework 中 serializers 模块，并创建一个 EmailSerializer 类，继承 serializers.ModelSerializer 类。
```python
# users/serializers.py

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
    instance.save()         # 保存修改后的数据
    return instance
```

#### 创建邮箱修改视图类：

1.编辑子应用 users 目录下的 views.py 文件，在该文件中导入 EmailSerializer 类，并创建一个 EmailView 类，继承 CreateAPIView 类。
```python
# users/views.py

...

from rest_framework.generics import CreateAPIView, RetrieveAPIView, UpdateAPIView
from users.serializers import EmailSerializer

...

class EmailView(UpdateAPIView):
    """
    修改邮箱 update()

    1.查询数据是否存在；
    2.保存修改的数据；
    3.返回修改的数据。
    """
    # 指定序列化器
    serializer_class = EmailSerializer

    # 加一层验证 --- 只有验证通过后的用户才可以来到用户中心.  IsAuthenticated 该模块会自动认证用户是否是登录状态的
    permission_classes = [IsAuthenticated]

    def get_object(self):    # 指定要修改的数据
      return self.request.user    # 让其返回指定指定查询数据
```

#### 添加访问路由

1.编辑子应用 users 目录下的 urls.py 文件，在该文件中添加访问个人中心路由 `email/`：
```python
# users/urls.py

... 
urlpatterns = [
    ...
    path('email/', views.EmailView.as_view()),
]
```

### 邮箱验证（激活邮箱）

#### django 邮箱的配置

1.编辑配置文件 dev.py 文件，在该文件中添加邮箱配置：
```python
# haoke/settings/dev.py

...

EMAIL_BACKEND = 'django.core.mail.backends.smtp.EmailBackend'
EMAIL_HOST = 'smtp.163.com'
EMAIL_PORT = 25
EMAIL_HOST_USER = 'veazhi@163.com'
EMAIL_HOST_PASSWORD = '19971015'
EMAIL_USE_TLS = False
EMAIL_USE_SSL = False
DEFAULT_FROM_EMAIL = '商城：好客<veazhi@163.com>'
```
#### 使用异步任务发送邮件

1.在异步任务包目录下新建 email 包目录，在该目录下新建 tasks.py 文件，在该文件中导入 celery 模块，并创建一个 email_send 函数：
```python
# tasks.py

from celery_tasks.main import app
from django.conf import settings
from django.core.mail import send_mail


@app.task(name='send_verify_email')

def send_verify_email(to_email, verify_url):
    """
    发送验证邮件
    :param to_email: 收件人邮箱
    :param verify_url: 验证链接
    :return: None
    """

    subject = '商城验证邮件'
    # message = ''
    # from_email = settings.EMAIL_FROM
    # recipient_list = [to_email]
    html_message = '<p>尊敬的用户您好！</p>' \
                   '<p>感谢您使用商城。</p>' \
                   '<p>您的邮箱为：%s 。请点击此链接激活您的邮箱：</p>' \
                   '<p><a href="%s">%s<a></p>' % (to_email, verify_url, verify_url)
    # msg = EmailMessage(subject, message, from_email, recipient_list)
    # msg.html_message = html_message
    # msg.send()
    send_mail(subject, html_message, settings.EMAIL_FROM, [to_email])
```

2.注册邮件发送的异步任务：
```python
# celery_tasks/main.py

...

app.autodiscover_tasks(['celery_tasks.sms', 'celery_tasks.email'])
```



























