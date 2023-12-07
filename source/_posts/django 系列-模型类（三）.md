---
title: django 系列-模型类（三）
author: leazhi
tags:
  - [django]
  - [python]
categories:
  - [django]
  - [python]
date: 2023-07-27 07:34:37
cover: https://hexo.linuser.com/gallery/code/1181671.jpg
discription: 关系型数据库中的高级查询
keywords: django, python, sql, mysql
password: 
message: 
---

在模型类二中简单的使用了 get() 查询 和 filter() 条件查询，接下来就要做一些高级查询。如下：

## all() 方法，查询表中所有数据
{% note default simple %}
通过 all() 方法，查询表中所有数据，返回 QuerySet 列表。可以通过下标取值。但不能通过属性取值（因为返回的数据烈性是列表，有多个相同的属性）。如果非要取，那么只能通过循环的方式取出！
{% endnote %}

调用类属性 objects 的 all() 方法查询表中所有数据，如下： 
```python
>>> BookInfo.objects.all()
<QuerySet [<BookInfo: 射雕英雄传>, <BookInfo: 天龙八部>, <BookInfo: 笑傲江湖>, <BookInfo: 雪山飞狐>, <BookInfo: python 从入门到精通>, <BookInfo: pyt 西游记2>, <BookInfo: 西游记后传>]>

# 通过下标取值
>>> BookInfo.objects.all()[3]       
<BookInfo: 雪山飞狐>

# 不能通过属性取值：
>>> BookInfo.objects.all().name
Traceback (most recent call last):
  File "<console>", line 1, in <module>
AttributeError: 'QuerySet' object has no attribute 'name'
```

## count() 方法，统计表中数据总数

调用类属性 objects 的 count() 方法查询表中所有数据，如下： 
```python
>>> BookInfo.objects.count()
8
```

## exclude()方法，反向取值（排除符合条件外的）

{% note default simple %}
**默认，exclude() 方法不加条件，同 all() 方法一样，查询所有结果**
```python
# exclude() 方法：
>>> BookInfo.objects.exclude()
<QuerySet [<BookInfo: 射雕英雄传>, <BookInfo: 天龙八部>, <BookInfo: 笑傲江湖>, <BookInfo: 雪山飞狐>, <BookInfo: python 从入门到精通>, <BookInfo: pyt 西游记2>, <BookInfo: 西游记后传>]>

# all() 方法：
>>> BookInfo.objects.all()
<QuerySet [<BookInfo: 射雕英雄传>, <BookInfo: 天龙八部>, <BookInfo: 笑傲江湖>, <BookInfo: 雪山飞狐>, <BookInfo: python 从入门到精通>, <BookInfo: pyt 西游记2>, <BookInfo: 西游记后传>]>
```
{% endnote %}

调用类属性 objects 的 exclude() 方法排除查询符合条件外的数据
```python
>>> BookInfo.objects.exclude(name='python 从入门到精通')
<QuerySet [<BookInfo: 射雕英雄传>, <BookInfo: 天龙八部>, <BookInfo: 笑傲江湖>, <BookInfo: 雪山飞狐>, <BookInfo: 西游记2>, <BookInfo: 西游记后传>]>
```

## 比较运算符：gt(大于)、gte(大于等于)、lt(小于)、lte(小于等于)，等于就不用加双下划线，直接赋值比较

{% note default simple %}
**语法格式：**属性名称__比较运算符 = 值
{% endnote %}

调用类属性 objects 的 filter() 方法对表中的数据进行比较
```python
# 查询表中 ID 大于 5 的数据
>>> BookInfo.objects.filter(id__gt = 5)
<QuerySet [<BookInfo: python 从入门到精通>, <BookInfo: 西游记2>, <BookInfo: 西游记后传>]>

# 查询表中 name 等于 python 从入门到精通 的数据
>>> BookInfo.objects.filter(name='python 从入门到精通')
<QuerySet [<BookInfo: python 从入门到精通>, <BookInfo: python 从入门到精通>]>
```

## contains()方法，是否包含（模糊查询）

调用类属性 objects 的 filter() 方法对表中的数据进行模糊查询：
```python
# 查询表中 name 包含 游 的数据
>>> BookInfo.objects.filter(name__contains='游')
<QuerySet [<BookInfo: 西游记2>, <BookInfo: 西游记后传>]>
```

## 日期查询：year、month、day

调用类属性 objects 的 filter() 方法对表中的数据进行模糊查询：
```python
# 查询表中 出版日期(pub_date)为 2000-01-01 之后出版的书籍数据
>>> BookInfo.objects.filter(pub_date__gte='2000-01-01')
<QuerySet [<BookInfo: python 从入门到精通>, <BookInfo: python 从入门到精通>, <BookInfo: 西游记2>, <BookInfo: 西游记后传>]>

# 查询表中 出版日期(pub_date)为 2012 年之后出版的书籍数据
>>> BookInfo.objects.filter(pub_date__year__gt='2012')
<QuerySet [<BookInfo: python 从入门到精通>, <BookInfo: python 从入门到精通>, <BookInfo: 西游记后传>]>
```

## F 对象和 Q 对象：实现属性之间的比较

{% note danger simple %}
**注意：**
1.该方法必须先导入方可使用；
```python
from django.db.models import F, Q
```
2.两种对象都市调用类属性 objects 的 filter() 方法对表中的数据进行属性比较的；
3.返回的数据类型为 QuerySet 列表；
{% endnote %}

### F 对象：
{% note default simple %}
**语法格式：**属性名称__比较运算符 = F('属性名')
{% endnote %}

调用类属性 objects 的 filter() 方法对表中的数据进行属性比较：
```python
# 查询阅读量大于等于评论量的图书：
>>> BookInfo.objects.filter(readcount__gte = F('commentcount'))
<QuerySet [<BookInfo: 雪山飞狐>, <BookInfo: python 从入门到精通>, <BookInfo: python 从入门到精通>, <BookInfo: 西游记2>, <BookInfo: 西游记后传>]>

# 查询阅读量大于评论量2倍的图书：
>>> BookInfo.objects.filter(readcount__gt = F('commentcount')*2)
<QuerySet [<BookInfo: 雪山飞狐>]>
```

### Q 对象：SQL 中的逻辑运算，与（&）、或（|）、非（~）

{% note default simple %}
**语法格式：**Q(属性名__运算符=值)&Q(属性名__运算符=值)
{% endnote %}

调用类属性 objects 的 filter() 方法对表中的数据进行属性比较：
```python
# 查询阅读量大于20，并且 ID 小于3 的图书
# 使用 filter 方法实现：
>>> BookInfo.objects.filter(readcount__gt=20).filter(id__lt=3)
<QuerySet [<BookInfo: 天龙八部>]>

# 使用 Q 对象实现：
>>> BookInfo.objects.filter(Q(readcount__gt=20))
<QuerySet [<BookInfo: 天龙八部>, <BookInfo: 雪山飞狐>]>

# 与：
>>> BookInfo.objects.filter(Q(readcount__gt=20)&Q(id__lt=3))
<QuerySet [<BookInfo: 天龙八部>]>

# 或：
>>> BookInfo.objects.filter(Q(readcount__gt=20)|Q(id__lt=3))
<QuerySet [<BookInfo: 射雕英雄传>, <BookInfo: 天龙八部>, <BookInfo: 雪山飞狐>]>

# 非：除了name 为 python 从入门到精通 的数据之外的数据：
>>> BookInfo.objects.filter(~Q(name='python 从入门到精通'))
<QuerySet [<BookInfo: 射雕英雄传>, <BookInfo: 天龙八部>, <BookInfo: 笑傲江湖>, <BookInfo: 雪山飞狐>, <BookInfo: 西游记2>, <BookInfo: 西游记后传>]>
# 相当于：
>>> BookInfo.objects.exclude(name='python 从入门到精通')
<QuerySet [<BookInfo: 射雕英雄传>, <BookInfo: 天龙八部>, <BookInfo: 笑傲江湖>, <BookInfo: 雪山飞狐>, <BookInfo: 西游记2>, <BookInfo: 西游记后传>]>
```

## 聚合查询：sum()

{% note danger simple %}
**注意：**
1.该方法必须先导入方可使用；
```python
from django.db.models import Sum,Avg,Max,Min
```
2.在模型类中需要使用过滤器 aggregate() 方法实现聚合查询;
3.返回的数据类型为字典；
{% endnote %}


{% note default simple %}
**语法格式：**aggregate(聚合函数('字段名'))
{% endnote %}

调用类属性 objects 的 aggregate() 过滤器方法对表中的数据进行聚合运算：

```python
# 查询表中图书的总阅读量：
>>> BookInfo.objects.aggregate(Sum('readcount'))
{'readcount__sum': 126}
```

## 排序查询:关键字 order_by

{% note danger simple %}
**排序只能对一个字段名进行，如果对多个字段名排序，则只会对第一个字段名进行排序，其它字段名的排序会被忽略**
{% endnote %}

### 升序：
{% note default simple %}
**语法格式：**order_by('字段名')
{% endnote %}

调用类属性 objects.all() 方法对表中的数据进行全部查询，然后调用 order_by() 方法进行排序
```python
# 以升序的方式对表中阅读量进行排序
>>> BookInfo.objects.all().order_by('readcount')
<QuerySet [<BookInfo: python 从入门到精通>, <BookInfo: python 从入门到精通>, <BookInfo: 西游记2>, <BookInfo: 西游记后传>, <BookInfo: 射雕英雄传>, <B: 天龙八部>, <BookInfo: 雪山飞狐>]>
```

### 降序：关键字前面需要减号（非下划线）
{% note default icon simple %}
**语法格式：**order_by('-字段名')
{% endnote %}

调用类属性 objects.all() 方法对表中的数据进行全部查询，然后调用 order_by() 方法进行排序

```python
# 以降序的方式对表中评论量进行排序
<QuerySet [<BookInfo: 笑傲江湖>, <BookInfo: 天龙八部>, <BookInfo: 射雕英雄传>, <BookInfo: 雪山飞狐>, <BookInfo: python 从入门到精通>, <BookInfo: pyt 西游记2>, <BookInfo: 西游记后传>]>
```


## 关联查询

不同表之间的查询，它分为：一对多查询，多对一查询
### 一对多

#### 方法一：通过 get() 方法联合查询：
{% note default simple %}
**语法：** 一对应的模型类对象.多对应的模型类名小写_set
{% endnote %}

一（图书表 BookInfo） --- 多（人物表 PeopleInfo）

查询图是表 BookInfo 中 id 为 1 图书与之关联的人物表中 PeopleInfo 中对应的人物信息：

1.编辑 models.py 文件，在 PeopleInfo() 模型类中加入魔法方法 __str__(),如下：
```python
# usres/models.py

from django.db import models

# Create your models here.


'''
# create table book(`id` INT UNSIGNED AUTO_INCREMENT PRIMARY KEY,`书名` VARCHAR(20) NOT NULL ,`日期` date , `阅读量` INTEGER DEFAULT 0, `评论量` INTEGER DEFAULT 0, `是否上架` DOUBLE PRECISION default FALSE)ENGINE=InnoDB DEFAULT CHARSET=utf8;

# 登录MySQL，创建一个新库： 
(root@localhost (none) 06:39:)>create database if not exists book;
Query OK, 1 row affected (0.002 sec)

# 如果不指定表明，则直接使用子应用名_模型类名
'''
# 图书的模型类
class BookInfo(models.Model):           # 继承
    # 在模型类里面， ID 是不需要去指定的。它默认会设置为主键 且自增长
    '''
    # models.CharField()：models 是模型类的方法，CharFiled() 是表的字段类型（字符串类型）
    # max_length：字段名长度
    # verbose_name：表中的字段名
    结合起来相当于 mysql 中的： vachar(20)
    '''
    # 书名：
    name = models.CharField(max_length=20, verbose_name='书名')

    # 出版日期：
    pub_date = models.DateField(verbose_name='日期', null=True)

    # 阅读量：由于阅读量是一个整型，所以这里默认设置为 0 (一开始上架的时候没有阅读量)
    readcount = models.IntegerField(verbose_name='阅读量', default=0)

    # 评论量：整型，默认设置为0
    commentcount = models.IntegerField(verbose_name='评论量', default=0)

    # 是否上架: 布尔类型，默认设置为不上架
    is_delete = models.BooleanField(verbose_name='是否上架', default=False)

    # 定义表名：
    class Meta:
        db_table = 'bookinfo'
        # admin：django 自带的后台管理模块
        verbose_name = '图书'  # 在 admin 里面显示 图书S 名称
        verbose_name_plural = verbose_name  # 这样就在后台管理里面显示 图书 名称

    # 使用 __str__() 魔法方法，返回类对象的属性值
    def __str__(self):
        return self.name

# 实例化对象：
# book = BookInfo(name='python', pub_date='2023-07-24')
# 使用模型类中的 save() 方法保存数据
# book.save()

class PeopleInfo(models.Model):

    # 定义一个性别的数组，供后面 Choices 方法传值
    GENDER_CHOICE = (
        (0, 'male'),
        (1, 'female'),
    )
    # 属性名 = 属性类型（选项）
    # 名称：
    name = models.CharField(max_length=20, verbose_name='名称')
    # 性别：
    # choices 默认为空，也就是可以不传值。它接收的值类型为 元祖（元祖的特征，去重）
    # SmallIntegerField: 小型的整数类型
    gender = models.SmallIntegerField(choices=GENDER_CHOICE ,default=0, verbose_name='性别')
    # 描述信息：
    description = models.CharField(max_length=200, verbose_name='描述信息', null=True)
    # 外键
    # on_delete：级联删除（指明主表删除数据时，对于外键引用表数据如何处理）
    # CASCADE 表示删除主表数据时与之关联的副表数据（连接的外键）一并删除 ，常用；
    # PROTECT 阻止删除主表中被外键应用的数据（阻止用户删除关联的数据）
    book = models.ForeignKey(BookInfo,on_delete=models.CASCADE)
    # 逻辑删除，删除的状态，并非真正删除
    is_delete = models.BooleanField(verbose_name='逻辑删除', default=0)

    class Meta:
        db_table = 'peopleinfo'
        # admin：django 自带的后台管理模块
        verbose_name = '人物信息'  # 在 admin 里面显示 人物信息S 名称
        verbose_name_plural = verbose_name  # 这样就在后台管理里面显示 人物信息 名称
    def __str__(self):
        return self.name
```

2.在 python 虚拟环境中打开 django shell ，执行：
```python
(web12) leazhi@ubuntuhome:~/web12$ python3 manage.py shell
Python 3.10.12 (main, Jun 11 2023, 05:26:28) [GCC 11.4.0] on linux
Type "help", "copyright", "credits" or "license" for more information.
(InteractiveConsole)
# 导入模型类
>>> from users.models import BookInfo, PeopleInfo
# 实例化对象
>>> book = BookInfo.objects.get(id=1)
# 一对多语法查询
>>> book.peopleinfo_set.all()
<QuerySet [<PeopleInfo: 郭靖>, <PeopleInfo: 黄蓉>, <PeopleInfo: 黄药师>, <PeopleInfo: 欧阳锋>, <PeopleInfo: 梅超风>]>

# 如果类中不加入魔法方法 __str__()，则返回对象：
>>> book.peopleinfo_set.all()     # 注意：一对多调用 all() 方法
<QuerySet [<PeopleInfo: PeopleInfo object (18)>, <PeopleInfo: PeopleInfo object (19)>, <PeopleInfo: PeopleInfo object (20)>, <PeopleInfo: PeopleInfo object (21)>, <PeopleInfo: PeopleInfo object (22)>]>
```

#### 方法二：通过 filter() 联合查询

{% note default simple %}
**语法：** filter(外健__一对应模型类属性名__比较运算/条件运算=值)
{% endnote %}


查询图书表 BookInfo 中 name 为 '射雕英雄传' 与之关联的人物信息表 PeopleInfo 中对应的所有人物信息：

继上面在 django shell ，执行：
```python
# book 是外健
>>> persons = PeopleInfo.objects.filter(book__name='射雕英雄传')
>>> persons
<QuerySet [<PeopleInfo: 郭靖>, <PeopleInfo: 黄蓉>, <PeopleInfo: 黄药师>, <PeopleInfo: 欧阳锋>, <PeopleInfo: 梅超风>]>
```

### 多对一

#### 方法一：通过 get() 联合查询
多对一通过外健

{% note default simple %}
**语法：** 多对应的模型类对象.外健字段名
{% endnote %}


多（人物表 PeopleInfo）--- 一（图书表 BookInfo） 


根据人物表 PeopleInfo 中的人物信息查询图书表中对应的书名：

继上面在 django shell ，执行：
```python
>>> person = PeopleInfo.objects.get(name='郭靖')
>>> person
<PeopleInfo: 郭靖>
>>> person.name
'郭靖'
>>> person.book
<BookInfo: 射雕英雄传>
```

#### 方法二：通过 filter() 联合查询

{% note default simple %}
**语法：** filter(关联模型类名小写__属性名__比较运算/条件运算=值)
{% endnote %}


查询图书数据，要求图书人物为 黄蓉：

继上面在 django shell ，执行：
```python
>>> book = BookInfo.objects.filter(peopleinfo__name='黄蓉')
>>> book
<QuerySet [<BookInfo: 射雕英雄传>]>
```

