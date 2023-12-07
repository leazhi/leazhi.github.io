---
title: django-small 系列012-用户密码修改
author: leazhi
tags:
  - [django]
  - [python]
categories:
  - [django]
  - [python]
date: 2023-09-10 16:49:03
cover: https://hexo.linuser.com/gallery/code/1181671.jpg
discription: django 
keywords: django, python
password: 
message: 
---


## 创建密码修改视图：

编辑子应用 user 下的 views.py 文件，在该文件最后添加如下代码：
```python
# users/views.py

...

# 修改用户密码
class PassWord(APIView):
    """
    修改用户密码视图，请求接受 POST， ipassword, password, password2
    检验原始密码 ipassword 是否正确
    正确的话校验新密码的正确性，返回OK
    """
    def put(self, request):
        # 获取参数
        ipassword = request.data["ipassword"]
        password = request.data["password"]
        password2= request.data["password2"]
        user_id = request.data["user_id"]

        # 获取请求用户
        user = User.objects.get(id=user_id)

        # 校验新密码是否符合要求
        if password != password2:
            raise Exception("两次密码输入不一致")

        if len(password) > 20 or len(password) < 8:
            raise Exception("密码长度需要 8 ~ 20 位")

        # 检查原始密码是否正确
        user.check_password(ipassword)

        # 修改密码为新密码：
        user.set_password(password)
        user.save()

        # 返回数据
        return Response(status=status.HTTP_202_ACCEPTED)
```

## 添加密码修改访问路由

编辑子应用 user 下的 urls.py 文件，在该文件的 urlpatterns 列表中添加如下代码：
```python
# users/urls.py

...

urlpatterns = [
    ...
    path('password/', views.PassWord.as_view()),
]

...
```