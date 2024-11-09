---
title: 4.视图
category: Django框架
tags:
  - framework
  - django
  - view
  - viewset
date: 2023-1-04 00:00:00
---



用于编写接口服务，处理业务逻辑。

> 以下视图均有对应的路由配置，详情请观看下一节。



## 视图函数

### 无参函数

```python
from django.http import HttpResponse


def index(request):
    return HttpResponse("Welcome to Django")
```

### param参数

```python
def hello(request, name):
    return HttpResponse(f"Hello {name}, welcome to Django")
```

### kwargs参数

用于接收主路由传递给子路由下所有路由的参数。

```python user/views.py
from django.http import HttpResponse


def index(request, **kwargs):
	# flag = True
  print(kwargs.get("flag"))
  return HttpResponse("This is user's index")
```

### 重定向

```python
from django.shortcuts import redirect, reverse
from django.http import HttpResponse


def index_view(request, **kwargs):
    print(kwargs.get("flag"))
    return HttpResponse("This is user's index")


def hello_view(request, **kwargs):
    print(kwargs.get("flag"))
    
    # return redirect("/user/index/")
    return redirect(reverse("index_view"))
```

> 在需要重定向的位置，返回`redirect`函数值，即可实现重定向到指定路由。
>
> redirect：重定向函数，传递一个重定向目标路由，可以是绝对路由，也可以是相对路由。
>
> reverse：反译函数，用于查询路由的name属性，返回路由的绝对地址。



## 视图类

```python
from django.views import View


class IndexView(View):
  def get(self, request):
    pass

  def post(self, request):
    pass
```

> 包含如下方法
>
> ```python
> http_method_names = [
>     "get",
>     "post",
>     "put",
>     "patch",
>     "delete",
>     "head",
>     "options",
>     "trace",
> ]
> ```



## 请求与响应

详情参见[请求和响应对象](https://docs.djangoproject.com/zh-hans/4.2/ref/request-response/)。



## 文件上传

详情参见[文件上传 | Django 文档 | Django (djangoproject.com)](https://docs.djangoproject.com/zh-hans/4.2/topics/http/file-uploads/)。

**注意**：表单需要设置`enctype="multipart/form-data"`。
