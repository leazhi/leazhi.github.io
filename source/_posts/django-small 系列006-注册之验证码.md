---
title: django-small 系列006-注册之邮箱和验证码
author: leazhi
tags:
  - [django]
  - [python]
categories:
  - [django]
  - [python]
date: 2023-08-25 20:11:09
cover: https://hexo.linuser.com/gallery/code/1181671.jpg
discription: 
keywords: 
password: 
message: 
---

## 短信验证码

### 注册三方：yuntongxun.com 

注册信息：
```bash
ACCOUNT SID：2c94811c8a27cf2d018a2ca1c93001b9
AUTH TOKEN：d572f5bba5bd4c78a4d72e12b68c75bc
Rest URL：https://app.cloopen.com:8883
AppID：2c94811c8a27cf2d018a2ca1ca8901c0
```

注册好后，将 yuntongxun 目录复制到下面创建的 sms 包目录下

### 创建 celery任务

1.在项目根目录 haoke 下新建一个 python 包目录 celery_tasks ,同时，在该目录下新建一个名为 sms 的包目录：

2.安装 celery 异步任务模块：
```python
(haoke_small) leazhi@ubuntuhome:~$ pip install celery
Looking in indexes: https://pypi.tuna.tsinghua.edu.cn/simple
Collecting celery
  Downloading https://pypi.tuna.tsinghua.edu.cn/packages/18/b9/cb8d519ea0094b9b8fe7480225c14937517729f8ec927643dc7379904f64/celery-5.3.1-py3-none-any.whl (420 kB)
     ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ 420.4/420.4 kB 2.8 MB/s eta 0:00:00
......
  Downloading https://pypi.tuna.tsinghua.edu.cn/packages/20/f4/c0584a25144ce20bfcf1aecd041768b8c762c1eb0aa77502a3f0baa83f11/wcwidth-0.2.6-py2.py3-none-any.whl (29 kB)
Installing collected packages: wcwidth, vine, tzdata, six, prompt-toolkit, click, billiard, python-dateutil, click-repl, click-plugins, click-didyoumean, amqp, kombu, celery
Successfully installed amqp-5.1.1 billiard-4.1.0 celery-5.3.1 click-8.1.7 click-didyoumean-0.3.0 click-plugins-1.1.1 click-repl-0.3.0 kombu-5.3.1 prompt-toolkit-3.0.39 python-dateutil-2.8.2 six-1.16.0 tzdata-2023.3 vine-5.0.0 wcwidth-0.2.6
```

3.将封装好的 yuntongxun 目录复制到创建的 celery_tasks/sms/ 目录下：

4.在 包目录 celery_tasks/ 目录下新建一个 config.py 文件，内容为：
```python
broker_url = 'redis://127.0.0.1:6379/14'
```

5.在 包目录 celery_tasks/ 目录下新建一个 main.py 文件，内容为：
```python
from celery import Celery
import os

# 为 celery 使用 Django 配置
if not os.getenv('DJANGO_SETTINGS_MODULE'):
    os.environ['DJANGO_SETTINGS_MODULE'] = 'haoke.settings.dev'

# 创建爱女 celery 应用
app = Celery('haoke')

# 添加 selery 配置信息
app.config_from_object('celery_tasks.config')

# 注册任务
app.autodiscover_tasks(['celery_tasks.sms'])
```

6.在 包目录 sms/ 目录下新建一个 tasks.py 文件，编译任务：
```python

from celery_tasks.sms.yuntongxun.sms import CCP
from celery_tasks.main import app

# 执行短信验证码发送
def send_sms_code(mobile,sms_code):
    """
    :param mobile: 手机号
    :param sms_code: 短信验证码长度
    :return:
    """
    CCP().send_template_sms(mobile, [sms_code, 6], 1)
```


### 创建子应用 verifications

1.创建子应用：
```python
(haoke_small) leazhi@ubuntuhome:~/small$ cd haoke/haoke/apps/
(haoke_small) leazhi@ubuntuhome:~/small/haoke/haoke/apps$ python ../../manage.py startapp verifications
```
2.子应用创建完成后，我们还需要去 django 的配置文件 dev.py 中注册该子应用：
```python
# haoke/settings/dev.py
...

INSTALLED_APPS = [
    ...
    'verifications'
]
...

```

3.编辑项目配置文件 dev.py, 加入短信验证码的 redis 配置：
```python
# haoke/settings/dev.py

...

CACHES = {
    'default': {
        'BACKEND': 'django_redis.cache.RedisCache',
        'LOCATION': 'redis://127.0.0.1:22652/9',
        'OPTIONS': {
            'CLIENT_CLASS':'django_redis.client.DefaultClient',
            "PASSWORD": "Zmi2hU4hGkdroBl7wWm/DszVaWWqrgNuWwD2tmHO",
        }
    },
    'code': {
        'BACKEND': 'django_redis.cache.RedisCache',
        'LOCATION': 'redis://127.0.0.1:22652/10',
        'OPTIONS': {
            'CLIENT_CLASS':'django_redis.client.DefaultClient',
            "PASSWORD": "Zmi2hU4hGkdroBl7wWm/DszVaWWqrgNuWwD2tmHO",
        }
    },
    'verify_code': {            # 短信验证码的 redis 配置
        "BACKEND": "django_redis.cache.RedisCache",
        "LOCATION": "redis://127.0.0.1:22652/2",
        'OPTIONS': {
            'CLIENT_CLASS': 'django_redis.client.DefaultClient',
            "PASSWORD": "Zmi2hU4hGkdroBl7wWm/DszVaWWqrgNuWwD2tmHO",
        }
    }
}

...
```


4.编辑子应用 verifications/views.py 文件，内容为：
```python
import random

from django.shortcuts import render
from rest_framework import status
from rest_framework.response import Response

# Create your views here.
from rest_framework.views import APIView
from django_redis import get_redis_connection

import logging

from celery_tasks.sms.tasks import send_sms_code
from verifications import constans

logger = logging.getLogger('django')


class SMSCodeView(APIView):
    def get(self, request, mobile):
        # 连接redis
        redis_conn = get_redis_connection('verify_code')

        # 检查是否在一定的时间内有发送记录
        flag = redis_conn.get('sms_flag_%s' % mobile)
        if flag:
            return Response({'message': '发送短信过于频繁'}, status=status.HTTP_400_BAD_REQUEST)

        # 生成短信验证码
        sms_code = '%06d' % random.randint(0, 999999)

        # 输出日志到控制台
        logger.info(sms_code)

        # 保存短信验证码：
        pl = redis_conn.pipeline()
        pl.setex('sms_%s' % mobile, constans.SMS_CODE_REDIS_EXPIRES, sms_code)

        # 检查是否在一定的时间内有发送记录
        pl.setex('sms_flag_%s' % mobile, constans.SMS_CODE_REDIS_INTERVAL, 1)

        # 执行管道命令（发送短信）
        pl.execute()

        # 发送短信
        send_sms_code(mobile, sms_code)

        return Response({'message': 'ok'}, status=status.HTTP_201_CREATED)
```

5.编辑子应用 verifications/constants.py 文件，内容为：
```python   
SMS_CODE_REDIS_EXPIRES = 300        # 短信验证码有效期
SMS_CODE_REDIS_INTERVAL= 60         # 60秒内不能重复发送短信
```

6.编辑子应用 verifications/urls.py 文件，内容为：
```python
from django.urls import path, re_path
from . import views 

urlpatterns = [
    re_path(r'^codes/(?P<mobile>1[345789]\d{9})/$',views.SMSCodeView.as_view()),
]
```

编辑项目主路由文件 haoken/urls.py 文件，内容为：
```python
# haoke/urls.py

from django.contrib import admin
from django.urls import path, include

urlpatterns = [
    path('admin/', admin.site.urls),
    path('', include('users.urls')),
    path('', include('verifications.urls')),        # 添加子路由

]
```