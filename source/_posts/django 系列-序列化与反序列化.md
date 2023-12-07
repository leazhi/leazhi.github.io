---
title: django 系列-序列化与反序列化
author: leazhi
tags:
  - [django]
  - [python]
categories:
  - [django]
  - [python]
date: 2023-08-09 08:33:03
cover:  https://hexo.linuser.com/gallery/code/1181671.jpg
discription: 
keywords: 
password: 
message: 
---

## 使用 Django shell 进行序列化调试
```python
(web12) leazhi@ubuntuhome:~$ cd web12/
(web12) leazhi@ubuntuhome:~/web12$ python manage.py shell
Python 3.10.12 (main, Jun 11 2023, 05:26:28) [GCC 11.4.0] on linux
Type "help", "copyright", "credits" or "license" for more information.
(InteractiveConsole)
>>> from users.serializer import BookInfoSerializers,PeopleInfoSerializers
>>> from users.models import BookInfo
```

### 单个数据序列化
```python
# 实例化， 模型类对象，构造查询集
>>> book = BookInfo.objects.get(id=2)
# book  #

# 把模型类对象转化为 json 类型数据 ， 序列化器---字典类型数据
# BookInfoServializers() 括号中的 instance 接收序列化的数据；data 接收反序列化的数据
>>> ser = BookInfoSerializers(instance= book)

# 获取序列化后的数据---通过序列化对象.data  --- data 属性坑一获取序列化后的数据
>>> ser.data      # {'id': 2, 'name': '天龙八部', 'pub_date': '1986-07-24', 'readcount': 36, 'commentcount': 40, 'is_delete': False, 'image': None}
```


###  多个数据
```python
# 实例化， 模型类对象，构造查询集
>>> book = BookInfo.objects.all()
# book  #

# 把模型类对象转化为 json 类型数据 ， 序列化器---字典类型数据
# BookInfoServializers() 括号中的 instance 接收序列化的数据；data 接收反序列化的数据
# many=True 如果是查询集合 QuerySet ，可以通过添加 many=True 参数补充说明（当前是有多个数据进行序列化）
>>> ser = BookInfoSerializers(instance=book, many=True)

# 获取序列化后的数据---通过序列化对象.data  --- data 属性坑一获取序列化后的数据
>>> ser.data
```

### 关联查询对象的序列化

1.编辑子应用下的 serializer.py 文件，先导入模型类 PeopleInfo, 然后编写关联查询的序列化代码，如下：
```python
# users/serializer.py

...
from users.models import PeopleInfo

...

# 关联查询序列化类：
class PeopleInfoSerializer(serializers.ModelSerializer):
    # 数据校验
    # 定义一个性别的数组，供后面 Choices 方法传值
    GENDER_CHOICE = (
        (0, 'male'),
        (1, 'female'),
    )

    name = serializers.CharField(max_length=20, label='名称')
    gender = serializers.IntegerField(label='性别')
    description = serializers.CharField(max_length=200, label='描述信息')
    # StringRelatedField  将此字段序列化为关联对象的字符串表示方式
    # 返回图书中的名称
    book = serializers.StringRelatedField(label='图书')
    is_delete = serializers.BooleanField(label='逻辑删除', default=0)
# #
    class Meta:
        model = PeopleInfo
        fields = '__all__'
        # fields = ('id', 'name', 'gender', 'book', 'description')
```

2.进入 django shell 调试窗口，执行：

```python
(web12) leazhi@ubuntuhome:~/web12$ python manage.py shell
Python 3.10.12 (main, Jun 11 2023, 05:26:28) [GCC 11.4.0] on linux
Type "help", "copyright", "credits" or "license" for more information.
(InteractiveConsole)
>>> from users.serializer import PeopleInfoSerializer
>>> from users.models import PeopleInfo
>>> 
>>> person = PeopleInfo.objects.get(id=18)    # 单表查询
>>> person
<PeopleInfo: 郭靖>
>>> 
>>> ser = PeopleInfoSerializer(person)        # 序列化
>>> ser.data
{'id': 18, 'name': '郭靖', 'gender': 1, 'description': '降龙十八掌', 'book': '射雕英雄传', 'is_delete': False}
>>> 
>>> person = PeopleInfo.objects.get(id=1)     # 如果表中没有该 ID，会报如下错误
Traceback (most recent call last):
  File "<console>", line 1, in <module>
  File "/home/leazhi/.env/web12/lib/python3.10/site-packages/django/db/models/manager.py", line 85, in manager_method
    return getattr(self.get_queryset(), name)(*args, **kwargs)
  File "/home/leazhi/.env/web12/lib/python3.10/site-packages/django/db/models/query.py", line 429, in get
    raise self.model.DoesNotExist(
users.models.PeopleInfo.DoesNotExist: PeopleInfo matching query does not exist.
```


####

1.编辑子应用下的 serializer.py 文件，先导入模型类 PeopleInfo, 然后编写关联查询的序列化代码，如下：
```python
# users/serializer.py

...
from users.models import PeopleInfo

...

# 关联查询序列化类：
class PeopleInfoSerializer(serializers.ModelSerializer):
    # 数据校验
    # 定义一个性别的数组，供后面 Choices 方法传值
    GENDER_CHOICE = (
        (0, 'male'),
        (1, 'female'),
    )

    name = serializers.CharField(max_length=20, label='名称')
    gender = serializers.IntegerField(label='性别')
    description = serializers.CharField(max_length=200, label='描述信息')
    # StringRelatedField  将此字段序列化为关联对象的字符串表示方式
    # 返回图书中的名称

    # book 外键--- 数据是从模型类里面来
    # 在序列化器里面 book --- 本质上就是为了显示管理的数据
    # 如果坑一把关联的数据也显示为字典，则最好
    book = PeopleInfoSerializer()

    is_delete = serializers.BooleanField(label='逻辑删除', default=0)
# #
    class Meta:
        model = PeopleInfo
        fields = '__all__'
        # fields = ('id', 'name', 'gender', 'book', 'description')
```

2.进入 django shell 调试窗口，执行：
```python
(web12) leazhi@ubuntuhome:~/web12$ python manage.py shell
Python 3.10.12 (main, Jun 11 2023, 05:26:28) [GCC 11.4.0] on linux
Type "help", "copyright", "credits" or "license" for more information.
(InteractiveConsole)
>>> from users.serializer import PeopleInfoSerializer
>>> from users.models import PeopleInfo
>>> person = PeopleInfo.objects.get(id=18)
>>> person
<PeopleInfo: 郭靖>
>>> ser = PeopleInfoSerializer(person)
>>> ser.data
{'id': 18, 'name': '郭靖', 'gender': 1, 'description': '降龙十八掌', 'book': OrderedDict([('id', 1), ('name', '射雕英雄传'), ('pub_date', '1980-05-01')]), 'is_delete': False}
>>> 
```