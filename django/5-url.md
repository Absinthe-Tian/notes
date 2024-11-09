---
title: 5.路由
category: Django框架
tags:
  - framework
  - django
  - url
  - path
  - route
date: 2023-1-05 00:00:00
---



用于映射视图函数到资源地址。



## 视图函数路由

### 无参视图函数路由

```python
from django.urls import path
from . import views

urlpatterns = [
    path('index/', views.index, name="index"),
]
```

> name：用于给该路由取名。方便页面跳转及重定向等等。

### param参数视图函数路由

```python
urlpatterns = [
    path('hello/<str:name>/', views.hello),
]
```

> param参数使用`<paramName>`占位的方式声明，该命名必须与视图函数形参命名保持一致。

* 路径转换器：用于限定param参数的类型。

1. str：匹配除`/`之外的非空字符串，默认值可省略。
2. int：匹配0或任意正整数。
3. slug：匹配字母、数字、下划线`_`、短横杆`-`组成的字符串。
4. uuid：匹配一个格式化[UUID](https://baike.baidu.com/item/UUID/5921266)。
5. path：匹配非空字段，包含路径分隔符`/`。

* 正则匹配：用于根据指定的格式进行匹配。

```python
from django.urls import re_path
from . import views

urlpatterns = [
    re_path("^hello/(?P<name>[A-z0-9]{1,12})/(?P<age>[0-1][0-9]{0,2})/$", views.hello),
]
```

> 正则路由使用`re_path`函数。
>
> 以`()`包裹匹配正则，其中`?P`代表规定参数，`<name>`指定匹配的参数名，后面紧跟正则表达式即可。
>
> 使用复杂，一般直接在视图中进行限定。



## 配置子路由

在项目模块下的urls.py文件添加如下结构

```python projectName/urls.py
from django.urls import path, include

urlpatterns = [
    path('user/', include("user.urls")),
]
```

> 配置子路由，需要使用`include`函数包含子路由的路由文件。

在APP下的urls.py文件添加如下结构

```python user/urls.py
from django.urls import path
from . import views

urlpatterns = [
    path("index/", views.index)
]
```

> user/urls.py子路由文件同一般路由文件使用一致。
>
> 访问完整的资源地址为：`/子路由/子路由下urls的路由/`，如`/user/index/`。

* kwargs参数：用于主路由给子路由下的所有路由添加参数。

在路由`path`函数添加第三个参数即为`kwargs`参数。

在APP下的views.py文件添加如下结构

```python user/views.py
from django.http import HttpResponse


def index(request, **kwargs):
	# flag = True
  print(kwargs.get("flag"))
  return HttpResponse("This is user's index")
```



## 类视图路由

```python
urlpatterns = [
    path("index/", views.IndexView.as_view(), name="index"),
]
```

