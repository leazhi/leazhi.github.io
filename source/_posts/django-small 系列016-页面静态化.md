---
title: django-small 系列016-页面静态化
author: leazhi
tags:
  - [python]
  - [django]
categories:
  - [python]
  - [django]
date: 2023-09-17 10:21:52
cover: https://hexo.linuser.com/gallery/code/1181671.jpg
discription: 
keywords: 
password: 
message: 
---

Django 中的页面静态化是将动态生成的页面转换为静态的 HTML 文件，以提高网站的性能和加载速度。在 Django 中，可以使用缓存和模板标签来实现页面静态化。 
 
首先，需要在 Django 的设置文件中启用缓存。可以选择使用内存缓存、文件缓存或者其他缓存后端。启用缓存后，Django 会将动态生成的页面缓存起来，以便后续的访问可以直接使用缓存的静态页面。 
 
接下来，可以使用 Django 的模板标签来实现页面的静态化。模板标签可以将动态的内容嵌入到静态的 HTML 模板中。例如，可以使用  `{% cache %}`  标签来缓存整个页面或者某个特定的模块。在模板中使用该标签时，可以设置缓存的时间，以及缓存的键名等参数。 
 
除了缓存和模板标签，还可以使用 Django 的静态文件处理器来处理静态文件，例如 CSS 和 JavaScript 文件。可以使用  `{% static %}`  标签来引用静态文件，并使用 Django 的静态文件管理器来收集和管理静态文件。 
 
总的来说，Django 中的页面静态化是通过缓存和模板标签来实现的。通过将动态生成的页面转换为静态的 HTML 文件，可以提高网站的性能和加载速度，同时也可以减轻服务器的负载。


## 创建页面静态化模板文件

### 新建模板目录并注册

1.在项目子目录 haoke 下面新建一个模板文件存放目录 templates.

2.将新建好的模板目录注册到配置文件 dev.py 中：
```python
# settings/dev.py

...

TEMPLATES = [
    {
        'BACKEND': 'django.template.backends.django.DjangoTemplates',
        # 注册 templates 目录
        'DIRS': [ BASE_DIR/ 'templates' ],
        'APP_DIRS': True,
        'OPTIONS': {
            'context_processors': [
                'django.template.context_processors.debug',
                'django.template.context_processors.request',
                'django.contrib.auth.context_processors.auth',
                'django.contrib.messages.context_processors.messages',
            ],
        },
    },
]

...

```

### 在模板目录中创建页面静态化文件

在上面创建的 templates 目录中，新建一些页面静态化文件，这里我就直接复制项目 docs 目录下的 `detail.html, index.html 及 list.html`:
```bash
cp /data/gitlab/python3-django-small_haoke/haoke/docs/{detail.html,index.html,list.html} /data/gitlab/python3-django-small_haoke/haoke/haoke/templates/
```

### 注册后端找前段目录的路径

编辑配置文件 settings/dev.py, 在文件最后添加：
```python
# settings/dev.py

...
import os

...

GENERATED_STATIC_HTML_FILES_DIR = os.path.join(os.path.dirname(os.path.dirname(BASE_DIR)), 'front_end_pc')                  # 前端目录和 django 项目在同一目录下
```

## 页面静态化

1.在子应用 contents 目录下新建一个 crons.py 文件，写入以下内容：
```python
# 查询数据，来用做模板变量的数据
import os.path
from django.conf import settings
from goods.models import GoodsChannel
from django.template import loader              # 找到指定的模板--把查询的数据放到模板中
from collections import OrderedDict             # 把变量指定为字典类型
from contents.models import ContentCategory

def generate_static_index_html():
    """
    生成静态的主页 html 文件
    """
    # 商品频道实现（1.商品的分组；2商品的分类（商品名称,id,url),需要实现的数据类似于
    '''
    catetories = {
        # 组1
        {
            # 频道：id, name 和 url
            'channels': [{'id': ,'name': ,'url': }, {}, {}, ...]
            # 类别
            'sub_cats': [{'id': , 'name': ,'sub_cats': [{}, {}]}, {}, {}, ... ]
        }，
        # 组2
        {
        
        }  
    }
    '''

    categories = OrderedDict()              # 商品类别转换成字典类型

    # 查询:查询集
    channels = GoodsChannel.objects.order_by('group_id', 'sequence')                # 查询排序

    for channel in channels:
        # 组号
        group_id = channel.group_id
        # 利用组号查询类别
        if group_id not in categories:
            categories[group_id] = {'channel': [], 'sub_cats': []}
        # 当前频道的类别
        cat1 = channel.catgory
        categories[group_id]['channel'].append({
            'id': cat1.id,
            'name': cat1.name,
            'url': channel.url
        })

        # cat2 = cat1.goodscategory_set.all()        # 二级类别
        for cat2 in cat1.goodscategory_set.all():
            cat2.sub_cats = []
            for cat3 in cat2.goodscategory_set.all():
                cat2.sub_cats.append(cat3)
            categories[group_id]['sub_cats'].append(cat2)

    ## 广告内容
    contents = {}

    # 使用广告类别查询广告内容
    content_categories = ContentCategory.objects.all()
    for cat in content_categories:
        contents[cat.key] = cat.content_set.filter(status=True).order_by('sequence')

    ## 渲染模板
    context = {
        'contents': contents,
        'catetories': categories
    }

    # 获取指定的模板
    template = loader.get_template('index.html')

    # 把数据写入到指定的模板中
    html_text = template.render(context)

    # 把模板中的数据写道前端的静态服务器里面的 index.html 文件里
    file_path = os.path.join(settings.GENERATED_STATIC_HTML_FILES_DIR, 'index.html')

    with open(file_path, 'w', encoding='utf-8') as f:
        f.write(html_text)
```

2.打开 django shell ，测试：
```bash
┌──(leazhi㉿kali-desktop)-[/data/gitlab/python3-django-small_haoke/haoke]
└─$ python manage.py shell
Python 3.11.4 (main, Jun  7 2023, 10:13:09) [GCC 12.2.0]
Type 'copyright', 'credits' or 'license' for more information
IPython 8.14.0 -- An enhanced Interactive Python. Type '?' for help.

In [1]: from contents.crons import generate_static_index_html

In [3]: generate_static_index_html()

In [4]: 
```

3.编辑前端目录 front_end_pc 下的 index.html 文件，批量替换该文件中的 fastdfs 访问IP，将写死的 192.168.1.103 替换成自己的前端服务器IP：


4.运行前后端项目，访问前端：
![](../static/img/20230917/20230917004.jpeg)


