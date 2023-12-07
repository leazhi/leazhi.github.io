---
title: django-small 系列011-收货地址完善
author: leazhi
tags:
  - [django]
  - [python]
categories:
  - [django]
  - [python]
date: 2023-09-07 08:25:33
cover: https://hexo.linuser.com/gallery/code/1181671.jpg
discription: 
keywords: 
password: 
message: 
---


## 为省、市、区添加缓存

### 配置缓存
1.安装扩展模块 drf-extensions
```bash
leazhi@ubuntuhome:~$ workon haoke_small
(haoke_small) leazhi@ubuntuhome:~$ pip install drf-extensions
Looking in indexes: https://pypi.tuna.tsinghua.edu.cn/simple
Collecting drf-extensions
  Downloading https://pypi.tuna.tsinghua.edu.cn/packages/58/15/ee46fa8293a51b68c60699806c0c2f55ff527613d83739e27b8f85444990/drf_extensions-0.7.1-py2.py3-none-any.whl (21 kB)
Requirement already satisfied: djangorestframework>=3.9.3 in ./.env/haoke_small/lib/python3.10/site-packages (from drf-extensions) (3.14.0)
Requirement already satisfied: django>=3.0 in ./.env/haoke_small/lib/python3.10/site-packages (from djangorestframework>=3.9.3->drf-extensions) (3.2.20)
Requirement already satisfied: pytz in ./.env/haoke_small/lib/python3.10/site-packages (from djangorestframework>=3.9.3->drf-extensions) (2023.3)
Requirement already satisfied: asgiref<4,>=3.3.2 in ./.env/haoke_small/lib/python3.10/site-packages (from django>=3.0->djangorestframework>=3.9.3->drf-extensions) (3.7.2)
Requirement already satisfied: sqlparse>=0.2.2 in ./.env/haoke_small/lib/python3.10/site-packages (from django>=3.0->djangorestframework>=3.9.3->drf-extensions) (0.4.4)
Requirement already satisfied: typing-extensions>=4 in ./.env/haoke_small/lib/python3.10/site-packages (from asgiref<4,>=3.3.2->django>=3.0->djangorestframework>=3.9.3->drf-extensions) (4.7.1)
Installing collected packages: drf-extensions
Successfully installed drf-extensions-0.7.1
```

2.编辑配置文件 dev.py，在文件默认添加如下配置：
```python
# haoke/settings/dev.py

...

# DRF 缓存扩展
REST_FRAMEWORK_EXTENSIONS = {
    'DEFAULT_CACHE_RESPONSE_TIMEOUT': 60 * 60 * 24,
    # 'DEFAULT_CACHE_ERRORS': True,
    'DEFAULT_USE_CACHE': 'default',       # 缓存数据保存到该配置文件中 redis 的 default 配置库中
    # 'DEFAULT_CACHE_KEY_FUNC': 'drf_extensions.cache.keys.default_key_func',
    # 'DEFAULT_KEY_PREFIX': 'drf_extensions',
}
```

### 为省市区数据缓存添加视图：

1.编辑子应用 areas 目录下的视图文件 views.py，添加如下视图：
```python
# haoke/app/areas/views.py

...

from rest_framework_extensions.cache.mixins import CacheResponseMixin

...

class AreasViewSet(CacheResponseMixin, ModelViewSet):     # 只需要在这里添加 CacheResponseMixin 即可
    pagination_class = None

    # 提供数据集
    def get_queryset(self):
        if self.action == 'list':
            return Area.objects.filter(parent=None)
        else:
            return Area.objects.all

    # 提供序列化器
    def get_serializer_class(self):
        if self.action == 'list':
            return AreaModelSerializer
        else:
            return SubAreaModelSerializer

    # 获取省列表
    def list(self, request, *args, **kwargs):
        queryset = self.get_queryset()
        serializer = self.get_serializer_class()(queryset, many=True)
        return render(request, serializer.data, status=200)

```




## 收获地址的增、删、改、查


### 准备工作

1.在项目子目录 haoke 目录下新建一个包目录 utils，然后在该目录下新建一个 models.py 文件，添加如下模型类：
```pyton
# utils/models.py

from django.db import models

class BaseModel (models.Model):
    create_time = models.DateTimeField(auto_now_add=True, verbose_name='创建时间')
    update_time = models.DateTimeField(auto_now=True, verbose_name='更新时间')

    class Meta:
        abstract = True    # 说明是抽象模型类，用于继承使用，数据库迁移时不会创建 BaseModel 的表
```

2.编辑子应用 users 目录下的 models.py，添加如下模型类：
```python
# users/models.py

...

from haoke.utils.models import BaseModel

...

class User(AbstractUser):

  ...

  email_active = models.BooleanField(default=False, verbose_name='邮箱是否激活')
  # 添加一个默认地址
  default_address = models.ForeignKey('Address', related_name='users', null=True, blank=True, on_delete=models.SET_NULL, verbose_name='默认地址')

  class Meta:
    ...


  class Address(BaseModel):
    """
    地址模型类
    """
    user = models.ForeignKey(User, on_delete=models.CASCADE, related_name='addresses', verbose_name='用户')
    title = models.CharField(max_length=20, verbose_name='地址名称')
    receiver = models.CharField(max_length=20, verbose_name='收货人')
    province = models.ForeignKey('areas.Area', on_delete=models.PROTECT,related_name='province_addresses', verbose_name='省')
    city = models.ForeignKey('areas.Area',on_delete=models.PROTECT, related_name='city_addresses', verbose_name='市')
    district = models.ForeignKey('areas.Area', on_delete=models.PROTECT, related_name='district_addresses', verbose_name='区')
    place = models.CharField(max_length=50, verbose_name='地址')
    mobile = models.CharField(max_length=11, verbose_name='手机号')
    tel = models.CharField(max_length=20, null=True, blank=True, default='', verbose_name='固定电话')
    email = models.CharField(max_length=30, null=True, blank=True, default='', verbose_name='邮箱')
    is_deleted = models.BooleanField(default=False, verbose_name='逻辑删除')

  class Meta:
    db_table = 'tb_address'
    verbose_name = '用户地址'
    verbose_name_plural = verbose_name

  # def __str__(self):
  #   return self.title


  # def get_default_address(self):
  #   """
  #   获取默认地址
  #   :return:
  #   """
  #   return self.is_default


  # def set_default_address(self):
  #   """
  #   设置默认地址
```

3.数据迁移，生成表：
```bash
(haoke_small) leazhi@ubuntuhome:~$ cd small/haoke/
(haoke_small) leazhi@ubuntuhome:~/small/haoke$ python manage.py makemigrations

(haoke_small) leazhi@ubuntuhome:~/small/haoke$ python manage.py migrate
```

### 增 

#### 添加序列化器

编辑子应用 users 目录下的 serializers.py 文件，代码如下：

```python
# users/serializers.py

...

from .models import User,Address
class UserAddressSerializer(serializers.ModelSerializer):
    """
    用户地址序列化器
    """

    # 对下列这些字段进行单独校验
    province = serializers.StringRelateField(read_only=True)
    city = serializers.StringRelateField(read_only=True)
    district = serializers.StringRelateField(read_only=True)
    province_id = serializers.IntegerField(read_only=True)
    city_id = serializers.IntegerField(read_only=True)
    district_id = serializers.IntegerField(read_only=True)
  
  class Meta:
    model = Address
    # 排除指定的哪些字段输出
    # 注意：如果字段不在序列化器中，则不会输出
    # 注意：如果字段在序列化器中，但未指定，则不会输出
    # 注意：如果字段在序列化器中，且指定了，则输出
    # 注意：如果字段在序列化器中，且指定了，但值为空，则不会输出
    # 注意：如果字段在序列化器中，且指定了，且值不为空，则输出
    exclude = ('user', 'is_deleted', 'create_time', 'update_time')

  def create(self, validated_data):
    """
    新增保存
    :param validated_data:
    :return:
    """
    if not re.match(r'1[3-9]\d{9}', validated_data['mobile']):
        raise serializers.ValidationError('手机号格式不正确')
    
    # 保存当前登录用户的数据
    validated_data['user'] = self.context['request'].user

    # 返回验证通过后的手机号
    return super().create(validated_data)


# 地址的标题
class AddressTitleSerializer(serializers.ModelSerializer):
    """
    地址标题序列化器
    """

    class Meta:
        model = Address
        fields = ('title',)
```

#### 添加视图集
1.编辑子应用 users 目录下的 views.py 文件，导入 ModelViewSet 类，并继承 ModelViewSet 类，代码如下：
```python
# users/views.py

...

from rest_framework.viewsets import ModelViewSet
from users.serializers import  UserAddressSerializer, AddressTitleSerializer

...

class AddressViewSet(ModelViewSet):
    """
    用户地址
    """

    serializer_class = UserAddressSerializer

    permission_classes = [IsAuthenticated]

    # 指定查询集
    def get_queryset(self):
        return self.request.user.addresses.filter(is_deleted=False)

    # 指定查询集
    def list(self, request, *args, **kwargs):
        """
        用户地址列表数据
        :param request:
        :param args:
        :param kwargs:
        :return:
        """

        queryset = self.get_queryset().filter(user=request.user)
        # 使用序列化器
        serializer = self.get_serializer(queryset, many=True)

        # 用户的地址：
        use = serializer.request.user      # 当前登录的用户

        return Response({
            'addresses': serializer.data,
            'limit': 20,      # 最多添加20条用户收货地址数据
            'default_address_id': user.default_address_id,  # 默认收货地址ID
        })

    def create(self, request, *args, **kwargs):

        # 限制用户收货地址数据上限
        count = request.user.addresses.count()  # 获取用户收货地址数量

        if count >= 20:
            return Response({'message': '收货地址最多添加20条'}, status=status.HTTP_400_BAD_REQUEST)

            return super().create(request, *args, **kwargs)  # 调用父类方法
    
    # 逻辑删除
    def destroy(self, request, *args, **kwargs):
        """
        处理删除的
        :param request:
        :param args:
        :param kwargs:
        :return:
        """
        # 获取要删除的数据--再进行逻辑删除
        address = self.get_object()

        # 把逻辑删除的状态 False 设置成 True,变成逻辑删除！
        address.is_deleted = True

        # 保存数据
        address.save()

        return Response(status=status.HTTP_204_NO_CONTENT)

    # 标题修改方法--将其注册到视图类中
    @action(methods=['put'], detail=True)       # detail 如果需要请求中的数据可以使用它
    def title(self, request, pk=None):
        # 修改标题
        # 获取要修改的数据---对修改的数据进行校验---报错 ---返回
        address = self.get_object()

        ser =AddressTitleSerializer(instance= address , data= request.data)

        ser.is_valid()

        ser.save()

        return Response(ser.data)


    # 设置收货地址为默认地址
    @action(methods=['put'], detail=True)
    def status(self,request, pk):

        # 获取要设置默认的数据
        address = self.get_object()

        # 为用户添加默认地址：
        request.user.default_address = address

        # 保存
        request.user.save()

        return Response({'message': 'OK'}, status=status.HTTP_200_OK)
```

#### 添加访问路由

编辑子应用 users 目录下的 urls.py 文件，代码如下：
```python 
# users/urls.py

...

from rest_framework.routers import DefaultRouter

...

# 因为是是视图集，
# 路由需要使用DefaultRouter
router = DefaultRouter()
router.register('addresses', views.AddressViewSet, basename='addresses')
urlpatterns += router.urls
```