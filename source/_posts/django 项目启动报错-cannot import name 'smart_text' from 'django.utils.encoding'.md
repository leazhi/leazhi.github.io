---
title: django 项目启动报错-cannot import name 'smart_text' from 'django.utils.encoding'
author: leazhi
tags:
  - [django]
categories:
  - [django]
date: 2023-08-31 13:56:17
cover: https://hexo.linuser.com/gallery/code/1181671.jpg
discription: 
keywords: 
password: 
message: 
---

## 开发环境：
- python 3.11.4
- django 3.1.0


## 故障描述

开发 django-small 项目，用到 djangorestframework-jwt 包，启动项目报错：

```python
INFO autoreload 668 Watching for file changes with StatReloader
Performing system checks...

Exception in thread django-main-thread:
Traceback (most recent call last):
  File "/home/leazhi/.env/haoke_small/lib/python3.10/site-packages/rest_framework/settings.py", line 177, in import_from_string
    return import_string(val)
  File "/home/leazhi/.env/haoke_small/lib/python3.10/site-packages/django/utils/module_loading.py", line 30, in import_string
    return cached_import(module_path, class_name)
  File "/home/leazhi/.env/haoke_small/lib/python3.10/site-packages/django/utils/module_loading.py", line 15, in cached_import
    module = import_module(module_path)
  File "/usr/lib/python3.10/importlib/__init__.py", line 126, in import_module
    return _bootstrap._gcd_import(name[level:], package, level)
  File "<frozen importlib._bootstrap>", line 1050, in _gcd_import
  File "<frozen importlib._bootstrap>", line 1027, in _find_and_load
  File "<frozen importlib._bootstrap>", line 1006, in _find_and_load_unlocked
  File "<frozen importlib._bootstrap>", line 688, in _load_unlocked
  File "<frozen importlib._bootstrap_external>", line 883, in exec_module
  File "<frozen importlib._bootstrap>", line 241, in _call_with_frames_removed
  File "/home/leazhi/.env/haoke_small/lib/python3.10/site-packages/rest_framework_jwt/authentication.py", line 4, in <module>
    from django.utils.encoding import smart_text
ImportError: cannot import name 'smart_text' from 'django.utils.encoding' (/home/leazhi/.env/haoke_small/lib/python3.10/site-packages/django/utils/encoding.py)

During handling of the above exception, another exception occurred:

Traceback (most recent call last):
  File "/usr/lib/python3.10/threading.py", line 1016, in _bootstrap_inner
    self.run()
  File "/usr/lib/python3.10/threading.py", line 953, in run
    self._target(*self._args, **self._kwargs)
  File "/home/leazhi/.env/haoke_small/lib/python3.10/site-packages/django/utils/autoreload.py", line 64, in wrapper
    fn(*args, **kwargs)
  File "/home/leazhi/.env/haoke_small/lib/python3.10/site-packages/django/core/management/commands/runserver.py", line 133, in inner_run
    self.check(display_num_errors=True)
  File "/home/leazhi/.env/haoke_small/lib/python3.10/site-packages/django/core/management/base.py", line 485, in check
    all_issues = checks.run_checks(
  File "/home/leazhi/.env/haoke_small/lib/python3.10/site-packages/django/core/checks/registry.py", line 88, in run_checks
    new_errors = check(app_configs=app_configs, databases=databases)
  File "/home/leazhi/.env/haoke_small/lib/python3.10/site-packages/django/core/checks/urls.py", line 14, in check_url_config
    return check_resolver(resolver)
  File "/home/leazhi/.env/haoke_small/lib/python3.10/site-packages/django/core/checks/urls.py", line 24, in check_resolver
    return check_method()
  File "/home/leazhi/.env/haoke_small/lib/python3.10/site-packages/django/urls/resolvers.py", line 494, in check
    for pattern in self.url_patterns:
  File "/home/leazhi/.env/haoke_small/lib/python3.10/site-packages/django/utils/functional.py", line 57, in __get__
    res = instance.__dict__[self.name] = self.func(instance)
  File "/home/leazhi/.env/haoke_small/lib/python3.10/site-packages/django/urls/resolvers.py", line 715, in url_patterns
    patterns = getattr(self.urlconf_module, "urlpatterns", self.urlconf_module)
  File "/home/leazhi/.env/haoke_small/lib/python3.10/site-packages/django/utils/functional.py", line 57, in __get__
    res = instance.__dict__[self.name] = self.func(instance)
  File "/home/leazhi/.env/haoke_small/lib/python3.10/site-packages/django/urls/resolvers.py", line 708, in urlconf_module
    return import_module(self.urlconf_name)
  File "/usr/lib/python3.10/importlib/__init__.py", line 126, in import_module
    return _bootstrap._gcd_import(name[level:], package, level)
  File "<frozen importlib._bootstrap>", line 1050, in _gcd_import
  File "<frozen importlib._bootstrap>", line 1027, in _find_and_load
  File "<frozen importlib._bootstrap>", line 1006, in _find_and_load_unlocked
  File "<frozen importlib._bootstrap>", line 688, in _load_unlocked
  File "<frozen importlib._bootstrap_external>", line 883, in exec_module
  File "<frozen importlib._bootstrap>", line 241, in _call_with_frames_removed
  File "/home/leazhi/small/haoke/haoke/urls.py", line 21, in <module>
    path('', include('users.urls')),
  File "/home/leazhi/.env/haoke_small/lib/python3.10/site-packages/django/urls/conf.py", line 38, in include
    urlconf_module = import_module(urlconf_module)
  File "/usr/lib/python3.10/importlib/__init__.py", line 126, in import_module
    return _bootstrap._gcd_import(name[level:], package, level)
  File "<frozen importlib._bootstrap>", line 1050, in _gcd_import
  File "<frozen importlib._bootstrap>", line 1027, in _find_and_load
  File "<frozen importlib._bootstrap>", line 1006, in _find_and_load_unlocked
  File "<frozen importlib._bootstrap>", line 688, in _load_unlocked
  File "<frozen importlib._bootstrap_external>", line 883, in exec_module
  File "<frozen importlib._bootstrap>", line 241, in _call_with_frames_removed
  File "/home/leazhi/small/haoke/haoke/apps/users/urls.py", line 13, in <module>
    from . import views
  File "/home/leazhi/small/haoke/haoke/apps/users/views.py", line 3, in <module>
    from rest_framework.views import APIView
  File "/home/leazhi/.env/haoke_small/lib/python3.10/site-packages/rest_framework/views.py", line 17, in <module>
    from rest_framework.schemas import DefaultSchema
  File "/home/leazhi/.env/haoke_small/lib/python3.10/site-packages/rest_framework/schemas/__init__.py", line 33, in <module>
    authentication_classes=api_settings.DEFAULT_AUTHENTICATION_CLASSES,
  File "/home/leazhi/.env/haoke_small/lib/python3.10/site-packages/rest_framework/settings.py", line 225, in __getattr__
    val = perform_import(val, attr)
  File "/home/leazhi/.env/haoke_small/lib/python3.10/site-packages/rest_framework/settings.py", line 168, in perform_import
    return [import_from_string(item, setting_name) for item in val]
  File "/home/leazhi/.env/haoke_small/lib/python3.10/site-packages/rest_framework/settings.py", line 168, in <listcomp>
    return [import_from_string(item, setting_name) for item in val]
  File "/home/leazhi/.env/haoke_small/lib/python3.10/site-packages/rest_framework/settings.py", line 180, in import_from_string
    raise ImportError(msg)
ImportError: Could not import 'rest_framework_jwt.authentication.JSONWebTokenAuthentication' for API setting 'DEFAULT_AUTHENTICATION_CLASSES'. ImportError: cannot import name 'smart_text' from 'django.utils.encoding' (/home/leazhi/.env/haoke_small/lib/python3.10/site-packages/django/utils/encoding.py).
```

## 解决方案

根据网上提供的信息，说是 Django 版本过低，不支持 smart_text 函数。请确保您的Django版本是最新的，并且与您的项目兼容。

于是升级 django 到 3.2.20 版本
```bash
(haoke_small) leazhi@ubuntuhome:~$ pip3 install --upgrade django==3.2.20
```

再次启动项目，成功。