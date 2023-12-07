---
title: django 系列-模型类（一）
author: leazhi
tags:
  - [django]
  - [python]
categories:
  - [django]
  - [python]
date: 2023-07-22 18:12:32
cover: https://hexo.linuser.com/gallery/code/1181671.jpg
discription: 
keywords: django, python, sql, mysql
password: 
message: 
---

## 模型类中的字段说明和选项

### 字段说明：
和MySQL的类型类似的

| 类型             | 说明                                                         |
| :--------------- | :----------------------------------------------------------- |
| AutoField        | 自动增长的IntegerField，通常不用指定，不指定时Django会自动创建属性名为id的自动增长属性 |
| BooleanField     | 布尔字段，值为True或False                                    |
| NullBooleanField | 支持Null、True、False三种值                                  |
| CharField        | 字符串，参数max_length表示最大字符个数，必须设置             |
| TextField        | 大文本字段，一般超过4000个字符时使用                         |
| IntegerField     | 整数                                                         |
| DecimalField     | 十进制浮点数， 参数max_digits表示总位数， 参数decimal_places表示小数位数 |
| FloatField       | 浮点数                                                       |
| DateField        | 日期， 参数auto_now表示每次保存对象时，自动设置该字段为当前时间，用于"最后一次修改"的时间戳，它总是使用当前日期，默认为False； 参数auto_now_add表示当对象第一次被创建时自动设置当前时间，用于创建的时间戳，它总是使用当前日期，默认为False; 参数auto_now_add和auto_now是相互排斥的，组合将会发生错误 |
| TimeField        | 时间，参数同DateField                                        |
| DateTimeField    | 日期时间，参数同DateField                                    |
| FileField        | 上传文件字段                                                 |
| ImageField       | 继承于FileField，对上传的内容进行校验，确保是有效的图片      |


### 选项：

| 选项         | 说明                                                         |
| :----------- | :----------------------------------------------------------- |
| null         | 如果为True，表示允许为空，默认值是False                      |
| blank        | 如果为True，则该字段允许为空白，默认值是False                |
| db_column    | 字段的名称，如果未指定，则使用属性的名称                     |
| db_index     | 若值为True, 则在表中会为此字段创建索引，默认值是False        |
| default      | 默认                                                         |
| primary_key  | 若为True，则该字段会成为模型的主键字段，默认值是False，一般作为AutoField的选项使用 |
| unique       | 如果为True, 这个字段在表中必须有唯一值，默认值是False        |
| verbose_name | 主要是admin后台显示                                          |

**null是数据库范畴的概念，blank是表单验证范畴的**

## 定义模型类

1.切换到项目开发的 python 虚拟环下境安装 pymysql 模块：
```python
leazhi@ubuntuhome:~$ workon web12
(web12) leazhi@ubuntuhome:~$ pip3 install pymysql -i https://pypi.douban.com/simple
Looking in indexes: https://pypi.douban.com/simple
Collecting pymysql
  Downloading https://pypi.doubanio.com/packages/e5/30/20467e39523d0cfc2b6227902d3687a16364307260c75e6a1cb4422b0c62/PyMySQL-1.1.0-py3-none-any.whl (44 kB)
     ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ 44.8/44.8 kB 3.0 MB/s eta 0:00:00
Installing collected packages: pymysql
Successfully installed pymysql-1.1.0
(web12) leazhi@ubuntuhome:~$ 
```

{% note danger simple %}
注意：如果你是远程开发，那么还需要将安装在远程主机上的 pymysql 包同步到本地来，否则后面在配置连接 mysql 的时候会报 mysqlclient 没有安装的错误
{% endnote %}

2.在子应用下的 models.py（该文件在创建子应用的时候自动生成的，无需在手动创建） 文件中写入:
```python
from django.db import models

# Create your models here.

# 图书的模型类
class BookInfo(models.Model):           # 继承
    # 在模型类里面， ID 是不需要去指定的。它默认会设置为主键 且自增长
    '''
    # models.CharField()：models 是模型类的方法，CharFiled() 是表的字段类型（字符串类型）
    # max_length：字段名长度
    # name：表中的字段名
    # 结合起来相当于 mysql 中的：`name` VARCHAR(20)
    '''
    # 书名：
    name = models.CharField(max_length=20, verbose_name='书名')

    # 出版日期：相当于在 mysql 中执行的: `pub_date` DATE NOT NULL
    pub_date = models.DateField(verbose_name='日期', null=True)

    # 阅读量：由于阅读量是一个整型，所以这里默认设置为 0 (一开始上架的时候没有阅读量)
    readcount = models.IntegerField(verbose_name='阅读量', default=0)

    # 评论量：整型，默认设置为0。相当于在 mysql 中执行的：`commentcount` INTEGER DEFAULT 0
    commentcount = models.IntegerField(verbose_name='评论量', default=0)

    # 是否上架: 布尔类型，默认设置为不上架
    is_delete = models.BooleanField(verbose_name='是否上架', default=False)

    # 定义表名：如果不指定表名，则直接使用小写app应用名_小写模型类名(users_BookInfo)
    class Meta:
        db_table = 'bookinfo'

        # admin：django 自带的后台管理模块
        verbose_name = '图书'     # 在 admin 里面显示 图书S 名称
        verbose_name_plural = verbose    # 这样就在后台管理里面显示 图书 名称

# 书本人物信息表---模型类
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
```
{% note info simple %}
执行上面这个模型类，相当于在 mysql 中执行了：
```sql
create table book(`id` INT UNSIGNED AUTO_INCREMENT PRIMARY KEY,`书名` VARCHAR(20) NOT NULL ,`日期` DATE NOT NULL, `阅读量` INTEGER DEFAULT 0, `评论量` INTEGER DEFAULT 0, `是否上架` DOUBLE PRECISION default FALSE)ENGINE=InnoDB DEFAULT CHARSET=utf8;
```
{% endnote %}

3.编辑项目下的 settings.py 文件，修改 DATABASES 的配置，如下：
```python
DATABASES = {
    'default': {
        # 'ENGINE': 'django.db.backends.sqlite3',
        # 'NAME': BASE_DIR / 'db.sqlite3',
        'ENGINE': 'django.db.backends.mysql',           # 修改使用 mysql
        'NAME': 'book',                                 # 数据库名
        'HOST': '127.0.0.1',                            # IP 地址
        'PORT': '3306',                                 # 端口号
        'USER': 'root',                                 # 用户名
        'PASSWORD': 'chekir0214',                       # 密码
    }
}

```

4.编辑项目目录下的初始化文件 __init__.py, 导入 pymysql:
```python
import pymysql

pymysql.install_as_MySQLdb()
```

5.登录mysql ,创建数据库 book:
```mysql
(root@localhost (none) 06:39:)>create database if not exists book;
Query OK, 1 row affected (0.002 sec)
```

6.打开终端工具，切换到项目主目录下，执行：

6.1.生成一个记录（迁移文件）
```bash
(web12) leazhi@ubuntuhome:~/web12$ python manage.py makemigrations
Migrations for 'users':
  users/migrations/0001_initial.py
    - Create model BookInfo
    - Create model PeopleInfo
```
{% note danger simple %}
注意：执行此步骤，不会到数据中生成任何数据，只会在当前子应用目录下生成迁移文件
{% endnote %}


6.2.同步到数据库中
```python 
(web12) leazhi@ubuntuhome:~/web12$ python manage.py makemigrations
Migrations for 'users':
  users/migrations/0001_initial.py
    - Create model BookInfo
    - Create model PeopleInfo
(web12) leazhi@ubuntuhome:~/web12$ python manage.py migrate
Operations to perform:
  Apply all migrations: admin, auth, contenttypes, sessions, users
Running migrations:
  Applying contenttypes.0001_initial... OK
  Applying auth.0001_initial... OK
  Applying admin.0001_initial... OK
  Applying admin.0002_logentry_remove_auto_add... OK
  Applying admin.0003_logentry_add_action_flag_choices... OK
  Applying contenttypes.0002_remove_content_type_name... OK
  Applying auth.0002_alter_permission_name_max_length... OK
  Applying auth.0003_alter_user_email_max_length... OK
  Applying auth.0004_alter_user_username_opts... OK
  Applying auth.0005_alter_user_last_login_null... OK
  Applying auth.0006_require_contenttypes_0002... OK
  Applying auth.0007_alter_validators_add_error_messages... OK
  Applying auth.0008_alter_user_username_max_length... OK
  Applying auth.0009_alter_user_last_name_max_length... OK
  Applying auth.0010_alter_group_name_max_length... OK
  Applying auth.0011_update_proxy_permissions... OK
  Applying auth.0012_alter_user_first_name_max_length... OK
  Applying sessions.0001_initial... OK
  Applying users.0001_initial... OK
```

7.登录 mysql ,切换到创建的 book 库中，查看生成的 bookinfo 表字段：
```sql
(root@localhost (none) 10:16:)>use book;
Reading table information for completion of table and column names
You can turn off this feature to get a quicker startup with -A

Database changed
(root@localhost book 10:18:)>show tables;
+----------------------------+
| Tables_in_book             |
+----------------------------+
| auth_group                 |
| auth_group_permissions     |
| auth_permission            |
| auth_user                  |
| auth_user_groups           |
| auth_user_user_permissions |
| bookinfo                   |
| django_admin_log           |
| django_content_type        |
| django_migrations          |
| django_session             |
| peopleinfo                 |
+----------------------------+
12 rows in set (0.000 sec)

(root@localhost book 10:18:)>desc bookinfo;
+--------------+-------------+------+-----+---------+----------------+
| Field        | Type        | Null | Key | Default | Extra          |
+--------------+-------------+------+-----+---------+----------------+
| id           | int(11)     | NO   | PRI | NULL    | auto_increment |
| name         | varchar(20) | NO   |     | NULL    |                |
| pub_date     | date        | YES  |     | NULL    |                |
| readcount    | int(11)     | NO   |     | NULL    |                |
| commentcount | int(11)     | NO   |     | NULL    |                |
| is_delete    | tinyint(1)  | NO   |     | NULL    |                |
+--------------+-------------+------+-----+---------+----------------+
6 rows in set (0.001 sec)

(root@localhost book 10:18:)>desc peopleinfo;
+-------------+--------------+------+-----+---------+----------------+
| Field       | Type         | Null | Key | Default | Extra          |
+-------------+--------------+------+-----+---------+----------------+
| id          | int(11)      | NO   | PRI | NULL    | auto_increment |
| name        | varchar(20)  | NO   |     | NULL    |                |
| gender      | smallint(6)  | NO   |     | NULL    |                |
| description | varchar(200) | YES  |     | NULL    |                |
| is_delete   | tinyint(1)   | NO   |     | NULL    |                |
| book_id     | int(11)      | NO   | MUL | NULL    |                |
+-------------+--------------+------+-----+---------+----------------+
6 rows in set (0.001 sec)
```

## 插入测试数据

{% note warning simple %}
因为 peopleinfo 关联的主表是 bookinfo，所以先要在 bookinfo 表中插入数据，方可在 peopleinfo 表中插入
{% endnote %}

1.接上一篇模型类（一），分别往 2 个表中插入如下数据：

1.1.bookinfo 表：
```sql
insert into bookinfo(name, pub_date, readcount,commentcount, is_delete) values
('射雕英雄传', '1980-5-1', 12, 34, 0),
('天龙八部', '1986-7-24', 36, 40, 0),
('笑傲江湖', '1995-12-24', 20, 80, 0),
('雪山飞狐', '1987-11-11', 58, 24, 0);
```

1.2.peopleinfo 表：
```sql
insert into peopleinfo(name, gender, book_id, description, is_delete)  values
    ('郭靖', 1, 1, '降龙十八掌', 0),
    ('黄蓉', 0, 1, '打狗棍法', 0),
    ('黄药师', 1, 1, '弹指神通', 0),
    ('欧阳锋', 1, 1, '蛤蟆功', 0),
    ('梅超风', 0, 1, '九阴白骨爪', 0),
    ('乔峰', 1, 2, '降龙十八掌', 0),
    ('段誉', 1, 2, '六脉神剑', 0),
    ('虚竹', 1, 2, '天山六阳掌', 0),
    ('王语嫣', 0, 2, '神仙姐姐', 0),
    ('令狐冲', 1, 3, '独孤九剑', 0),
    ('任盈盈', 0, 3, '弹琴', 0),
    ('岳不群', 1, 3, '华山剑法', 0),
    ('东方不败', 0, 3, '葵花宝典', 0),
    ('胡斐', 1, 4, '胡家刀法', 0),
    ('苗若兰', 0, 4, '黄衣', 0),
    ('程灵素', 0, 4, '医术', 0),
    ('袁紫衣', 0, 4, '六合拳', 0);
```