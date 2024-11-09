---
title: 11.Django Rest Framework
category: Django框架
tags:
  - framework
  - django
  - drf
date: 2023-1-11 00:00:00
---



djangoresframework是Django的一个插件APP，用于restfull风格API快速开发。

> 开发流程：定义模型 -> 定义序列化器 -> 定义视图 -> 定义路由。



## 序列化器

用于JSON数据与模型对象转换及数据校验。

> 序列化：将查询集对象 => JSON。
>
> 反序列化：JSON => 模型对象。

### 自定义序列化器

详情参见[Serializers - Django REST framework](https://www.django-rest-framework.org/api-guide/serializers/#serializers)。

```python serializers.py
class CommentSerializer(serializers.Serializer):
  email = serializers.EmailField()
  content = serializers.CharField(max_length=200)
  created = serializers.DateTimeField()

  def create(self, validated_data):
    return Comment(**validated_data)

  def update(self, instance, validated_data):
    instance.email = validated_data.get('email', instance.email)
    instance.content = validated_data.get('content', instance.content)
    instance.created = validated_data.get('created', instance.created)
    return instance
```

> 定义的字段即为返回的字段。

序列化器字段及属性参见[Serializer fields - Django REST framework ](https://www.django-rest-framework.org/api-guide/fields/)。

反序列化时，需要传入`data`参数，调用`save()`方法进行数据保存，保存的数据是创建还是修改，取决于是否传入`instance`参数，构造器如下：

```python serializers.py
def __init__(self, instance=None, data=empty, **kwargs):
  pass
```

> instance接收查询集或模型对象。
>
> 传入instance调用update方法，没有则调用create方法。
>
> 反序列化调用`save`方法之前，还需要调用`is_valid`方法校验数据。

### 模型序列化器

详情参见[ModelSerializer - Django REST 框架](https://www.django-rest-framework.org/api-guide/serializers/#modelserializer)。

```python serializers.py
class AccountSerializer(serializers.ModelSerializer):
    class Meta:
        model = Account
        fields = ['id', 'account_name', 'users', 'created']
```

> 需要有模型类才能使用。

追加额外校验逻辑

```python serializers.py
# 单字段校验，注意命名规则
def validate_fieldName(self, value):
  pass

# 对象字段校验，data包含所有字段
def validate(self, data):
  pass
```

### 关联序列化

详情参见[Serializer relations - Django REST framework ](https://www.django-rest-framework.org/api-guide/relations/)。

1. StringRelatedField字段

返回关联模型的`__str__`方法的返回值。

```python serializers.py
class AlbumSerializer(serializers.ModelSerializer):
  tracks = serializers.StringRelatedField(many=True)

  class Meta:
    model = Album
    fields = ['album_name', 'artist', 'tracks']
```

2. source属性

通过关联属性（正向关联：关联字段名；反向关联：关联模型小写_set），返回关联模型的某个字段值

```python serializers.py
class AlbumSerializer(serializers.ModelSerializer):
  tracks = serializers.CharField(source='关联属性.字段名')

  class Meta:
    model = Album
    fields = ['album_name', 'artist', 'tracks']
```

3. 嵌套序列化

```python serializers.py
class AlbumSerializer(serializers.ModelSerializer):
  tracks_set = TracksSerializer(many=True, read_only=True)

  class Meta:
    model = Album
    fields = ['album_name', 'artist', 'tracks']
```

> 会自动传入关联管理器对象进行序列化，并返回。

4. SerializerMethodField

将方法的返回值作为数据。

```python serializers.py
from django.contrib.auth.models import Group
from rest_framework import serializers
from .models import *


class MemberSerializer(serializers.ModelSerializer):
  class Meta:
    model = User
    fields = ["id", "username", "email", "groups", "is_superuser"]


class ClassSerializer(serializers.ModelSerializer):
  def get_member_detail(self, instance):
    members = MemberSerializer(instance.member.all(), many=True).data
    teacher_group = Group.objects.filter(name="老师").first()
    student_group = Group.objects.filter(name="学生").first()

    def get_info(user):
      return {"id": user["id"], "username": user["username"]}

    teachers = [get_info(i) for i in members if
                teacher_group.id in i["groups"] or i["is_superuser"]]

    students = [get_info(i) for i in members if
                student_group.id in i["groups"]]
    return {
      "teachers": teachers,
      "students": students,
    }

    member_detail = serializers.SerializerMethodField(method_name="get_member_detail")

    class Meta:
      model = Classes
      exclude = ["is_delete"]
```

> 默认方法名为`get_字段名`，也可指定方法名。

动态绑定序列化器可以重写`get_serializer`方法。



## 视图集

基于类视图的拓展，它不提供任何的get，post，put等请求方式，而是提供restfull风格对应API的方法。

详情参见[Viewsets - Django REST framework](https://www.django-rest-framework.org/api-guide/viewsets/)。

对应关系如下：

1. list：查询集合。
2. create：创建数据。
3. retrieve：查询单条。
4. update：修改单条。
5. destroy：删除单条。

### 自定义视图集

视图集定义如下：

```python views.py
from django.contrib.auth.models import User
from django.shortcuts import get_object_or_404
from myapps.serializers import UserSerializer
from rest_framework import viewsets
from rest_framework.response import Response


class UserViewSet(viewsets.ViewSet):
    """
    A simple ViewSet for listing or retrieving users.
    """
    def list(self, request):
        queryset = User.objects.all()
        # 实例化序列化器
        serializer = UserSerializer(queryset, many=True)
        return Response(serializer.data)

    def retrieve(self, request, pk=None):
        queryset = User.objects.all()
        user = get_object_or_404(queryset, pk=pk)
        serializer = UserSerializer(user)
        return Response(serializer.data)
```

> 序列化对象需要通过`.data`获取JSON数据。

路由定义如下：

```python urls.py
urlpatterns=[
  path("users/", UserViewSet.as_view({"get": "list", "post":"create"})),
  path("users/<pk>/", UserViewSet.as_view({"get": "retrieve", "put":"update", "delete":"destroy"})),
]
```

> `as_view({请求方式：视图集方法})`。

### 模型视图集

详情参见[ModelViewset - Django REST framework](https://www.django-rest-framework.org/api-guide/viewsets/#modelviewset)。

```python views.py
class AccountViewSet(viewsets.ModelViewSet):
    """
    A simple ViewSet for viewing and editing accounts.
    """
    queryset = Account.objects.all()
    serializer_class = AccountSerializer
    permission_classes = [IsAccountAdminOrReadOnly]
```

> 自动简单完成restfull对应的五个接口。

其他接口的定义

```python views.py
from rest_framework.decorators import action


class AccountViewSet(viewsets.ModelViewSet):
    ...
    
    @action(methods=["get"], detail=True)
    def viewName(self, request, pk):
      ...
      return Response()
```

> 定义其他接口时，需要使用action装饰器，其中的detail参数表示，是否是详情路由，即是否携带pk参数。
>
> 访问该接口时，直接使用方法名即可。

**注意**：使用drf API时，注意与Django自带的请求响应对象区分开。



## 路由器

详情参见[Routers - Django REST framework](https://www.django-rest-framework.org/api-guide/routers/)。

```python urls.py
from rest_framework import routers

urlpatterns = []

router = routers.SimpleRouter()
router.register(r'users', UserViewSet)
router.register(r'accounts', AccountViewSet)
urlpatterns += router.urls
```

> 自动映射restfull对应的五个路由。



## 权限校验

详情参见[Permissions - Django REST framework](https://www.django-rest-framework.org/api-guide/permissions/)。

### IsAuthenticated

需要登录才能访问。

```python views.py
from rest_framework import viewsets
from rest_framework.permissions import IsAuthenticated
from .serializers import *


# Create your views here.
class ClassesViewSet(viewsets.ModelViewSet):
  queryset = Classes.objects.filter(is_delete=False)
  serializer_class = ClassSerializer
  # 指定权限类
  permission_classes = [IsAuthenticated]
```

### 自定义权限

```python permission.py
from django.contrib.auth.models import Group
from rest_framework.permissions import BasePermission


class TeacherPermission(BasePermission):
  # 视图集权限
  def has_permission(self, request, view):
    # 获取当前登录的用户对象
    user = request.user
    # 获取老师分组
    teacher_group = Group.objects.filter(name="老师").first()
    # 获取用户分组
    user_groups = user.groups.all()
    return user.is_superuser or teacher_group in user_groups

  # 对象权限，单个对象操作时触发
  def has_object_permission(self, request, view, obj):
    pass
```

### 权限装饰器

```python utils/permission.py
from functools import update_wrapper

# 更改权限装饰器
def wrap_permission(*permissions, validate_permission=True):
    def decorator(func):
        def wrapper(self, request, *args, **kwargs):
            self.permission_classes = permissions
            if validate_permission:
                self.check_permissions(request)
            return func(self, request, *args, **kwargs)
				# 将wrapper伪装成func
        return update_wrapper(wrapper, func)

    return decorator
```

> 可以直接使用该装饰器指定视图集的某个方法的权限类。



## 分页器

详情参见[分页 - Django REST 框架](https://www.django-rest-framework.org/api-guide/pagination/)。

1. 定义

```python
class TopicPaginationPageNumber(PageNumberPagination):
    """
    PageNumber分页器：
    page: 页码
    size: 数据条数
    方便，固定跳转每页数据
    """
    # 默认每页多少条数据
    page_size = 3
    # 最大每页多少条数据
    max_page_size = 100
    # 指定哪个参数作为每页的条数
    page_size_query_param = 'size'
    # 指定哪个参数作为页数
    page_query_param = 'page'


class TopicPaginationLimitOffset(LimitOffsetPagination):
    """
    LimitOffset分页器：
    limit: 数据条数
    offset: 偏移量
    灵活，可以跳转指定数据
    """
    # 默认每页多少条数据
    default_limit = 3
    # 最大每页多少条数据
    max_limit = 100
```

2. 使用

```python views.py
class TopicViewSet(ModelViewSet):
    queryset = Topic.objects.filter(is_delete=False)
    serializer_class = TopicSerializer
    permission_classes = [IsAuthenticated]
    # 指定分页类
    pagination_class = TopicPaginationLimitOffset
```

