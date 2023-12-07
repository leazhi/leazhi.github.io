---
title: django-small 系列013-商品之表结构
author: leazhi
tags:
  - [django]
  - [python]
categories:
  - [django]
  - [python]
date: 2023-09-10 21:06:00
cover: https://hexo.linuser.com/gallery/code/1181671.jpg
discription: 
keywords: 
password: 
message: 
---

## 创建商品子应用及表结构

### 创建子应用

1.进入到项目子应用存放目录 apps  目录 ，然后执行：  
```bash
PS E:\GitLab\small\haoke\haoke\apps> python ..\..\manage.py startapp googs
```  

2.注册创建的子应用。编辑配置文件 dev.py, 在 INSTALLED_APPS 列表中添加配置：
```python
#haoke/settings/dev.py

...

INSTALLED_APPS = [
    ...
    'goods',
]
...
```

###  创建商品表：编写模型类代码：

编辑子应用 goods 目录下的 models.py 文件，添加商品表结构：
```python
# goods/models.py

from django.db import models
from haoke.utils.models import BaseModel

# Create your models here.
class GoodsCategory(BaseModel):
    """
    商品类别
    """

    name = models.CharField(max_length=10, verbose_name="名称")
    parent = models.ForeignKey('self', null=True, blank=True, on_delete=models.CASCADE, verbose_name="父类别")

    class Meta:
        db_table = "tb_goods_category"
        verbose_name = "商品类别"
        verbose_name_plural = verbose_name

    def __str__(self):
        return self.name

class GoodsChannel(BaseModel):
    """商品频道"""
    group_id = models.IntegerField(verbose_name="组号")
    category = models.ForeignKey(GoodsCategory, on_delete=models.CASCADE, verbose_name="顶级商品类别")
    url = models.CharField(max_length=50, verbose_name='频道页面链接')
    sequence = models.IntegerField(verbose_name="组内顺序")

    class Meta:
        db_table = "tb_goods_channel"
        verbose_name = "商品频道"
        verbose_name_plural = verbose_name

class Brand(BaseModel):
    """品牌"""
    name = models.CharField(max_length=20, verbose_name="名称")
    logo = models.ImageField(verbose_name="Logo图片")
    first_letter = models.CharField(max_length=1, verbose_name="品牌首字母")

    class Meta:
        db_table = 'tb_brand'
        verbose_name = "品牌"
        verbose_name_plural = verbose_name

    def __str__(self):
        return self.name

# 商品 SPU 表
class Goods(BaseModel):
    """商品SPU"""
    name = models.CharField(max_length=50, verbose_name="名称")
    brand = models.ForeignKey(Brand, on_delete=models.PROTECT, verbose_name="品牌")
    category1 = models.ForeignKey(GoodsCategory, on_delete=models.PROTECT, related_name='cat1_goods', verbose_name="一级类别")
    category2 = models.ForeignKey(GoodsCategory, on_delete=models.PROTECT,related_name='cat2_goods', verbose_name="二级类别")
    category3 = models.ForeignKey(GoodsCategory, on_delete=models.PROTECT, related_name='cat3_goods', verbose_name="三级类别")
    sales = models.IntegerField(default=0, verbose_name="销量")
    comments = models.IntegerField(default=0, verbose_name='评价数')

    class Meta:
        db_table = 'tb_goods'
        verbose_name = "商品"
        verbose_name_plural = verbose_name

    def __str__(self):
        return self.name

# 商品规格表
class GoodsSpecification(BaseModel):
    """商品规格"""
    goods = models.ForeignKey(Goods, on_delete=models.CASCADE, verbose_name="商品")
    name = models.CharField(max_length=20, verbose_name="规格名称")

    class Meta:
        db_table = "tb_goods_specification"
        verbose_name ="商品规格"
        verbose_name_plural = verbose_name

    def __str__(self):
        return self.name

# 规格选择表
class SpecificationOption(BaseModel):
    """规格选择"""
    spec = models.ForeignKey(GoodsSpecification, on_delete=models.CASCADE, verbose_name="规格")
    value = models.CharField(max_length=20, verbose_name="选项值")

    class Meta:
        db_table = "tb_specification_option"
        verbose_name = "规格选项"
        verbose_name_plural = verbose_name

    def __str__(self):
        return '%s - %s' %(self.spec, self.value)

# 商品 SKU 表
class SKU(BaseModel):
    """商品SKU"""
    name = models.CharField(max_length=50, verbose_name="名称")
    caption = models.CharField(max_length=100, verbose_name="副标题")
    goods = models.ForeignKey(Goods, on_delete=models.CASCADE, verbose_name="商品")
    category = models.ForeignKey(GoodsCategory, on_delete=models.PROTECT, verbose_name="从属类别")
    price = models.DecimalField(max_digits=10, decimal_places=2, verbose_name="单价")
    cost_price = models.DecimalField(max_digits=10,decimal_places=2, verbose_name="进价")
    market_price = models.DecimalField(max_digits=10, decimal_places=2, verbose_name="市场价")
    stock = models.IntegerField(default=0, verbose_name="库存")
    sales = models.IntegerField(default=0, verbose_name="销量")
    comments = models.IntegerField(default=0, verbose_name="评价数")
    is_launched= models.BooleanField(default=False, verbose_name="是否上架销售")
    default_image_url = models.CharField(max_length=200, default='', null=True, blank=True, verbose_name="默认图片")

    class Meta:
        db_table = "tb_sku"
        verbose_name = "商品SKU"
        verbose_name_plural = verbose_name

    def __str__(self):
        return '%s:%s' % (self.id, self.name)

# SKU 图片表
class SKUImage(BaseModel):
    """SKU图片"""
    sku = models.ForeignKey(SKU, on_delete=models.CASCADE, verbose_name="sku")
    image = models.ImageField(verbose_name="图片")

    class Meta:
        db_table = "tb_sku_image"
        verbose_name = "SKU图片"
        verbose_name_plural = verbose_name

    def __str__(self):
        return '%s %s' % (self.sku.name, self.id)

# SKU 具体规格表
class SKUSpecification(BaseModel):
    sku = models.ForeignKey(SKU, on_delete=models.CASCADE, verbose_name='sku')
    spec = models.ForeignKey(GoodsSpecification, on_delete=models.PROTECT, verbose_name='规格名称')
    option = models.ForeignKey(SpecificationOption, on_delete=models.PROTECT, verbose_name="规格值")

    class Meta:
        db_table = "tb_sku_specification"
        verbose_name = "SKU规格"
        verbose_name_plural = verbose_name

    def __str__(self):
        return '%s: %s - %s' %(self.sku, self.spec.name, self.option.value)
```

### 数据迁移

执行数据迁移命令：
```bash
PS E:\GitLab\small\haoke\haoke\apps> python ..\..\manage.py makemigrations
Migrations for 'areas':
  areas\migrations\0001_initial.py
    - Create model Area
Migrations for 'goods':
  goods\migrations\0001_initial.py
    - Create model Brand
    - Create model Goods
    - Create model GoodsCategory
    - Create model GoodsSpecification
    - Create model SKU
    - Create model SpecificationOption
    - Create model SKUSpecification
    - Create model SKUImage
    - Create model GoodsChannel
    - Add field category1 to goods
    - Add field category2 to goods
    - Add field category3 to goods
Migrations for 'users':
  users\migrations\0003_auto_20230910_2146.py
    - Create model Address
    - Add field default_address to user
```

### 在数据库中生成数据表：

执行命令：
```bash
PS E:\GitLab\small\haoke\haoke\apps> python ..\..\manage.py migrate
Operations to perform:
  Apply all migrations: admin, areas, auth, contenttypes, goods, sessions, users
Running migrations:
  Applying areas.0001_initial... OK
  Applying goods.0001_initial... OK
  Applying users.0003_auto_20230910_2146... OK
```

## 创建广告子应用及数据表

### 创建广告子应用：

1..进入到项目子应用存放目录 apps  目录 ，然后执行：  
```bash
PS E:\GitLab\small\haoke\haoke\apps> python ..\..\manage.py startapp contents
```

2.注册创建的子应用。编辑配置文件 dev.py, 在 INSTALLED_APPS 列表中添加配置：
```python
#haoke/settings/dev.py

...

INSTALLED_APPS = [
    ...
    'contents',
]
...
```

### 创建商品表：编写模型类代码：
编辑子应用 contents 目录下的models.py文件，添加如下代码：
```python  
# contents/models.py

from django.db import models

# Create your models here.
from haoke.utils.models import BaseModel

# 广告内容类别表
class ContentCategory(BaseModel):
    """广告内容类别"""
    name = models.CharField(max_length=50, verbose_name="名称")
    key = models.CharField(max_length=50, verbose_name="类别键名")

    class Meta:
        db_table = 'tb_content_category'
        verbose_name = "广告内容类别"
        verbose_name_plural = verbose_name

    def __str__(self):
        return self.name

# 广告内容表
class Content(BaseModel):
    """广告内容"""
    category = models.ForeignKey(ContentCategory, on_delete=models.PROTECT, verbose_name='类别')
    title = models.CharField(max_length=100, verbose_name='标题')
    url = models.CharField(max_length=300, verbose_name='内容链接')
    image = models.ImageField(null=True, blank=True, verbose_name='图片')
    text = models.TextField(null=True, blank=True, verbose_name="内容")
    sequence = models.IntegerField(verbose_name="排序")
    status = models.BooleanField(default=True, verbose_name="是否展示")

    class Meta:
        db_table = 'tb_content'
        verbose_name = "广告内容"
        verbose_name_plural= verbose_name

    def __str__(self):
        return self.category.name + ':' + self.title
```

### 数据迁移

执行数据迁移命令：
```bash
PS E:\GitLab\small\haoke\haoke\apps> python ..\..\manage.py makemigrations
Migrations for 'contents':
  contents\migrations\0001_initial.py
    - Create model ContentCategory
    - Create model Content
```

### 生成数据表
执行命令：
```bash
PS E:\GitLab\small\haoke\haoke\apps> python ..\..\manage.py migrate          
Operations to perform:
  Apply all migrations: admin, areas, auth, contents, contenttypes, goods, sessions, users
Running migrations:
  Applying contents.0001_initial... OK
```
