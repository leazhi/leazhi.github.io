---
title: django-small 系列019-商品搜索
author: leazhi
tags:
  - [python]
  - [django]
categories:
  - [python]
  - [django]
date: 2023-09-21 19:48:47
cover: https://hexo.linuser.com/gallery/code/1181671.jpg
discription: 
keywords: 
password: 
message: 
---

## 安装配置 elasticsearch 搜索引擎

### 使用 docker 部署 elasticsearch 搜索引擎服务端

具体部署请参考：[docker 之服务部署-elasticsearch](https://hexo.linuser.com/2023/09/21/208384e18717/)

### 安装配置 elasticsearch 搜索引擎客户端

#### 安装 elasticsearch 客户端

打开命令行终端，使用 pip 安装调用 elasticsearch 搜索引擎的模块 drf-haystack 和 elasticsearch==2.4.0
```bash
┌──(leazhi㉿kali-desktop)-[~/Downloads]
└─$ pip install drf-haystack elasticsearch==2.4.0
Defaulting to user installation because normal site-packages is not writeable
DEPRECATION: Loading egg at /usr/local/lib/python3.11/dist-packages/fdfs_client_py-1.2.6-py3.11-linux-x86_64.egg is deprecated. pip 23.3 will enforce this behaviour change. A possible replacement is to use pip for package installation..
Collecting drf-haystack
  Obtaining dependency information for drf-haystack from https://files.pythonhosted.org/packages/0d/52/6bb104f3bc81c050d4d254119656a0b7dd756108ec527f74cf0bf3ad9e94/drf_haystack-1.8.12-py2.py3-none-any.whl.metadata
  WARNING: Retrying (Retry(total=4, connect=None, read=None, redirect=None, status=None)) after connection broken by 'NewConnectionError('<pip._vendor.urllib3.connection.HTTPSConnection object at 0x7f8a5a2cdcd0>: Failed to establish a new connection: [Errno 101] 网络不可达')': /packages/0d/52/6bb104f3bc81c050d4d254119656a0b7dd756108ec527f74cf0bf3ad9e94/drf_haystack-1.8.12-py2.py3-none-any.whl.metadata
  Downloading drf_haystack-1.8.12-py2.py3-none-any.whl.metadata (1.0 kB)
Collecting elasticsearch==2.4.0
  Downloading elasticsearch-2.4.0-py2.py3-none-any.whl (54 kB)
     ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ 54.3/54.3 kB 187.2 kB/s eta 0:00:00
Requirement already satisfied: urllib3<2.0,>=1.8 in /usr/lib/python3/dist-packages (from elasticsearch==2.4.0) (1.26.16)
Requirement already satisfied: Django<=4.2,>=2.2 in /home/leazhi/.local/lib/python3.11/site-packages (from drf-haystack) (3.1.10)
Requirement already satisfied: djangorestframework<=3.14,>=3.7 in /home/leazhi/.local/lib/python3.11/site-packages (from drf-haystack) (3.14.0)
Collecting django-haystack<=3.2.1,>=2.8 (from drf-haystack)
  Downloading django-haystack-3.2.1.tar.gz (466 kB)
     ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ 466.6/466.6 kB 389.8 kB/s eta 0:00:00
  Installing build dependencies ... done
  Getting requirements to build wheel ... done
  Preparing metadata (pyproject.toml) ... done
Requirement already satisfied: python-dateutil in /usr/lib/python3/dist-packages (from drf-haystack) (2.8.2)
Requirement already satisfied: asgiref<4,>=3.2.10 in /usr/lib/python3/dist-packages (from Django<=4.2,>=2.2->drf-haystack) (3.7.2)
Requirement already satisfied: pytz in /usr/lib/python3/dist-packages (from Django<=4.2,>=2.2->drf-haystack) (2023.3)
Requirement already satisfied: sqlparse>=0.2.2 in /usr/lib/python3/dist-packages (from Django<=4.2,>=2.2->drf-haystack) (0.4.2)
Downloading drf_haystack-1.8.12-py2.py3-none-any.whl (20 kB)
Building wheels for collected packages: django-haystack
  Building wheel for django-haystack (pyproject.toml) ... done
  Created wheel for django-haystack: filename=django_haystack-3.2.1-py3-none-any.whl size=141759 sha256=be3514561a5498b7b18373540a14728631e885b55f0ec7e8c36cdf8351a7ded6
  Stored in directory: /home/leazhi/.cache/pip/wheels/f4/7d/b8/be796a3c135037c9000005880b36179201ce70cd60572df978
Successfully built django-haystack
Installing collected packages: elasticsearch, django-haystack, drf-haystack
Successfully installed django-haystack-3.2.1 drf-haystack-1.8.12 elasticsearch-2.4.0
```

#### 注册 elasticsearch 客户端

编辑项目下的配置文件 dev.py ，将客户端注册到 INSTALLED_APPS =[ .. ] 列表中：
```python
# settings/dev.py

...

INSTALLED_APPS = [
    ...
    'haystack',
]

...

#  配置 es：
HAYSTACK_CONNECTIONS = {
    'default': {
        'ENGINE': 'haystack.backends.elasticsearch_backend.ElasticsearchSearchEngine',
        'URL': '192.168.3.200:9200/',
        'INDEX_NAME': 'haoke',
    },
}

# 当添加、修改、删除数据时，自动生成索引
HAYSTACK_SIGNAL_PROCESSOR='haystack.signals.RealtimeSignalProcessor'

...
```

## 代码编写

### 构造索引数据

在子应用 goods 目录下新建 search_indexes.py 文件，代码如下：
```
# goods/search_indexes.py

from .models import SKU
from haystack import indexes

class SKUIndex(indexes.SearchIndex, indexes.Indexable):
    """
    SKU 索引数据模型类
    """
    
    # document 
    text = indexes.CharField(document=True, use_template=True)
    
    def get_model(self):
        """
        获取指定的模型类
        返回模型类
        """
        
        return SKU
    
    
    def index_queryset(self, using=None):
        """
        建立的数据查询积极俄
        """
        return self.get_model().objects.filter(is_launched=True)
```

### 构造索引文件目录

1.在 templates 目录下递归创建`search/indexes/goods` 目录：

2.在递归创建的 `search/indexes/goods` 目录下新建名为 `sku_text.txt` 的文件,并在该文件中写入：
```text
{{ object.name }}
{{ object.id }}
{{ object.caption }}
```

3.打开 django shell 模式，在项目根目录下执行ming：
```bash
┌──(leazhi㉿kali-desktop)-[/data/gitlab/python3-django-small_haoke]
└─$ cd haoke               
                                                                                                                                                                                                                                                                                                                        
┌──(leazhi㉿kali-desktop)-[/data/gitlab/python3-django-small_haoke/haoke]
└─$ python manage.py rebuild_index
WARNING: This will irreparably remove EVERYTHING from your search index in connection 'default'.
Your choices after this are to restore from backups or rebuild via the `rebuild_index` command.
Are you sure you wish to continue? [y/N] y
Removing all documents from your index because you said so.
All documents removed.
Indexing 16 商品SKU
GET /haoke/_mapping [status:404 request:0.002s]
```

### 编写序列化器

编辑子应用 goods 目录下的 serializer.py 文件，创建 SKUIndexSerializer 序列化器：
```python
# goods/serializer.py

...
from drf_haystack.serializers import HaystackSerializer
from .search_indexes import SKUIndex

...

class SKUIndexSerializer(HaystackSerializer):
    object = SKUSerializers(read_only=True)
    class Meta:
        index_classes = [SKUIndex]
        fields = ('text', 'object')
```

### 编写视图类

编辑子应用 goods 目录下的 views.py 文件，创建 SKUSearchView 类：
```python
# goods/views. py

...
from drf_haystack.viewsets import HaystackViewSet
from .serializers import SKUIndexSerializer

...

class SKUSearchView(HaystackViewSet):
    index_models = [SKU]
    serializer_class = SKUIndexSerializer
```


### 添加访问路由

编辑子应用 goods 目录下的 urls.py 文件，使用 rest_framework 默认路由添加：
```bash
# goods/urls.py

from . import views
from django.urls import path, re_path
from rest_framework.routers import DefaultRouter


urlpatterns = [
    re_path('^categories/(?P<category_id>\d+)/skus/$', views.SKUListView.as_view()),
]

router = DefaultRouter()
router.register('skus/saerch', views.SKUSearchView,basename='skus_search')
urlpatterns += router.urls
```


























