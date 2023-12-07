---
title: django 系列-模型类（二）
author: leazhi
tags:
  - [django]
  - [python]
categories:
  - [django]
  - [python]
date: 2023-07-25 09:32:02
cover: https://hexo.linuser.com/gallery/code/1181671.jpg
discription: 关系型数据库的增删改查
keywords: django, python, sql, mysql
password: 
message: 
---

## django shell 

Django的manage工具提供了**shell**命令，帮助我们配置好当前工程的运行环境（如连接好数据库等），以便可以直接在终端中执行测试python语句。

1.进入你项目根目录，执行：
```bash
(web12) leazhi@ubuntuhome:~/web12$ python manage.py shell
Python 3.10.6 (main, May 29 2023, 11:10:38) [GCC 11.3.0] on linux
Type "help", "copyright", "credits" or "license" for more information.
(InteractiveConsole)    
>>> 
```

2.进入 django shell 环境后，我们就可以直接在终端中使用 python 语句了。
```bash
>>> from users.models import BookInfo
>>> 
```

## 模型类中数据的增：

### 方式一：以实例化对象的方式：

{% note warning simple %}
使用该方法保存数据，没有返回值。通常用于不需要返回值的场景
{% endnote %}

1.继续在 django shell 命令行下执行：

```python
# 实例化对象
>>> book = BookInfo(name='python', pub_date='2023-07-24')

# 调用实例化对象的 save() 方法保存数据.
>>> book.save()
>>> 
```

2.登录 mysql ，验证数据：
```sql
(root@localhost book 10:58:)>select * from bookinfo;
+----+-----------------+------------+-----------+--------------+-----------+
| id | name            | pub_date   | readcount | commentcount | is_delete |
+----+-----------------+------------+-----------+--------------+-----------+
|  1 | 射雕英雄传      | 1980-05-01 |        12 |           34 |         0 |
|  2 | 天龙八部        | 1986-07-24 |        36 |           40 |         0 |
|  3 | 笑傲江湖        | 1995-12-24 |        20 |           80 |         0 |
|  4 | 雪山飞狐        | 1987-11-11 |        58 |           24 |         0 |
|  5 | python          | 2023-07-24 |         0 |            0 |         0 |
+----+-----------------+------------+-----------+--------------+-----------+
5 rows in set (0.000 sec)
```

### 方式二：调用对象的 objects.create() 方法，它有返回值

{% note warning simple %}
使用该方法保存数据，有返回值（默认返回表 ID）。通常用于需要返回值的场景
{% endnote %}

objects 模型属性，是模型和数据库进行查询的接口。   
create() 创建、新建。模型对象中实现新增数据并保存。   

1.添加数据：
```python
>>> BookInfo.objects.create(name='西游记', pub_date='1989-04-21')
<BookInfo: BookInfo object (8)>         # 返回数据库中的 id
>>> BookInfo.objects.create(name='西游记2', pub_date='2000-07-01')
<BookInfo: BookInfo object (9)>
```

2.修改模型类，在 BookInfo() 类中添加 __str__() 魔法方法，让其返回添加数据的 name 值：
```python
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
```

3.退出 django shell,重新进入，再次添加测试数据：
```python
# 退出 django shell
>>> quit()

# 重新进入 django shell
(web12) leazhi@ubuntuhome:~/web12$ python manage.py shell
Python 3.10.6 (main, May 29 2023, 11:10:38) [GCC 11.3.0] on linux
Type "help", "copyright", "credits" or "license" for more information.
(InteractiveConsole)

# 导入模块
>>> from users.models import BookInfo

# 添加测试数据，不在返回数据 ID，而是返回添加的数据的 name 值
>>> BookInfo.objects.create(name='西游记后传', pub_date='2013-11-11')
<BookInfo: 西游记后传>
```

## 模型类中数据的查：

### 方式一：通过对象的 objects.get() 方法，获取单个数据

{% note danger simple %}
**注意：**

1.通过此方法去查询数据，如果没有这个查询的数据会报模型类没有数据的错---DoesNotExist;
2.该方法不能直接调用 update() 方法对数据进行修改，只能通过实例化对象的方式进行修改保存；
{% endnote %}

1.实例化对象查询：
```python
>>> book = BookInfo.objects.get(id=8)
>>> book
<BookInfo: 西游记>
```


### 方式二：通过 filter() 方法过滤查询，相当于 sql 语句中的 where 语句：

{% note success simple %}
这种查询方法可以被 update() 方法调用
{% endnote %}
```python
>>> BookInfo.objects.filter(name='python')
<QuerySet [<BookInfo: python>, <BookInfo: python>, <BookInfo: python>]>
```


## 模型类中数据的改：

### 先通过对象的 objects.get() 方法，获取单个数据，然后在通过实例化修改修改，最后保存数据。无返回值

```python
# 先查：
>>> book = BookInfo.objects.get(name='西游记')

# 再修改
>>> book.name = '西游记1'

# 保存！
>>> book.save()
```

2.登录 mysql ,查看数据是否被修改：
```sql
(root@localhost book 05:23:)>select * from bookinfo;
+----+-----------------+------------+-----------+--------------+-----------+
| id | name            | pub_date   | readcount | commentcount | is_delete |
+----+-----------------+------------+-----------+--------------+-----------+
|  1 | 射雕英雄传      | 1980-05-01 |        12 |           34 |         0 |
|  2 | 天龙八部        | 1986-07-24 |        36 |           40 |         0 |
|  3 | 笑傲江湖        | 1995-12-24 |        20 |           80 |         0 |
|  4 | 雪山飞狐        | 1987-11-11 |        58 |           24 |         0 |
|  5 | python          | 2023-07-24 |         0 |            0 |         0 |
|  7 | python          | 2023-07-24 |         0 |            0 |         0 |
|  8 | 西游记1         | 1989-04-21 |         0 |            0 |         0 |
|  9 | 西游记2         | 2000-07-01 |         0 |            0 |         0 |
| 10 | python          | 2023-07-24 |         0 |            0 |         0 |
| 11 | 西游记后传      | 2013-11-11 |         0 |            0 |         0 |
+----+-----------------+------------+-----------+--------------+-----------+
10 rows in set (0.000 sec)
```


### 通过对象的 update() 方法修改，返回受影响的行数

{% note danger simple %}
提供 get() 方法查询数据后，不能再通过 update() 方法修改：
```[ython
>>> BookInfo.objects.get(name='python').update(name='python 从入门到精通')
Traceback (most recent call last):
  File "<console>", line 1, in <module>
  File "/home/leazhi/.env/web12/lib/python3.10/site-packages/django/db/models/manager.py", line 85, in manager_method
    return getattr(self.get_queryset(), name)(*args, **kwargs)
  File "/home/leazhi/.env/web12/lib/python3.10/site-packages/django/db/models/query.py", line 433, in get
    raise self.model.MultipleObjectsReturned(
users.models.BookInfo.MultipleObjectsReturned: get() returned more than one BookInfo -- it returned 3!
]
```
{% endnote %}

所以，我们只能通过第二种过滤查询对数据进行修改， 且会返回说影响的行数：
```python
>>> BookInfo.objects.filter(name='python').update(name='python 从入门到精通')
3
```
{% note warning simple %}
使用 filter()过滤查询后通过 update() 修改数据是直接修改保存数据的。不需要额外手动再去保存一次！
{% endnote %}

进入 mysql 验证：
```sql
(root@localhost book 07:11:)>select * from bookinfo;
+----+---------------------------+------------+-----------+--------------+-----------+
| id | name                      | pub_date   | readcount | commentcount | is_delete |
+----+---------------------------+------------+-----------+--------------+-----------+
|  1 | 射雕英雄传                | 1980-05-01 |        12 |           34 |         0 |
|  2 | 天龙八部                  | 1986-07-24 |        36 |           40 |         0 |
|  3 | 笑傲江湖                  | 1995-12-24 |        20 |           80 |         0 |
|  4 | 雪山飞狐                  | 1987-11-11 |        58 |           24 |         0 |
|  5 | python 从入门到精通       | 2023-07-24 |         0 |            0 |         0 |
|  7 | python 从入门到精通       | 2023-07-24 |         0 |            0 |         0 |
|  8 | 西游记1                   | 1989-04-21 |         0 |            0 |         0 |
|  9 | 西游记2                   | 2000-07-01 |         0 |            0 |         0 |
| 10 | python 从入门到精通       | 2023-07-24 |         0 |            0 |         0 |
| 11 | 西游记后传                | 2013-11-11 |         0 |            0 |         0 |
+----+---------------------------+------------+-----------+--------------+-----------+
10 rows in set (0.000 sec)
```

## 模型类中的删

### 通过实例化对象调用 delete() 方法删除，它会返回删除记录

1.删除数据：
```bash
>>> book = BookInfo.objects.get(id=10)
>>> book
<BookInfo: python 从入门到精通>
>>> book.
>>> book.delete()
(1, {'users.BookInfo': 1})
```

2.确认：
```sql
(root@localhost book 07:55:)>select * from bookinfo;
+----+---------------------------+------------+-----------+--------------+-----------+
| id | name                      | pub_date   | readcount | commentcount | is_delete |
+----+---------------------------+------------+-----------+--------------+-----------+
|  1 | 射雕英雄传                | 1980-05-01 |        12 |           34 |         0 |
|  2 | 天龙八部                  | 1986-07-24 |        36 |           40 |         0 |
|  3 | 笑傲江湖                  | 1995-12-24 |        20 |           80 |         0 |
|  4 | 雪山飞狐                  | 1987-11-11 |        58 |           24 |         0 |
|  5 | python 从入门到精通       | 2023-07-24 |         0 |            0 |         0 |
|  7 | python 从入门到精通       | 2023-07-24 |         0 |            0 |         0 |
|  8 | 西游记1                   | 1989-04-21 |         0 |            0 |         0 |
|  9 | 西游记2                   | 2000-07-01 |         0 |            0 |         0 |
| 11 | 西游记后传                | 2013-11-11 |         0 |            0 |         0 |
+----+---------------------------+------------+-----------+--------------+-----------+
```

### 通过 filter() 方法直接调用 delete() 方法删除

删除数据：
```sql
>>> BookInfo.objects.filter(name='西游记1').delete()
(1, {'users.BookInfo': 1})
```

验证数据：
```sql
(root@localhost book 07:59:)>select * from bookinfo;
+----+---------------------------+------------+-----------+--------------+-----------+
| id | name                      | pub_date   | readcount | commentcount | is_delete |
+----+---------------------------+------------+-----------+--------------+-----------+
|  1 | 射雕英雄传                | 1980-05-01 |        12 |           34 |         0 |
|  2 | 天龙八部                  | 1986-07-24 |        36 |           40 |         0 |
|  3 | 笑傲江湖                  | 1995-12-24 |        20 |           80 |         0 |
|  4 | 雪山飞狐                  | 1987-11-11 |        58 |           24 |         0 |
|  5 | python 从入门到精通       | 2023-07-24 |         0 |            0 |         0 |
|  7 | python 从入门到精通       | 2023-07-24 |         0 |            0 |         0 |
|  9 | 西游记2                   | 2000-07-01 |         0 |            0 |         0 |
| 11 | 西游记后传                | 2013-11-11 |         0 |            0 |         0 |
+----+---------------------------+------------+-----------+--------------+-----------+
8 rows in set (0.000 sec)
```