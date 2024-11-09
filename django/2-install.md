---
title: 2.Django项目启动
category: Django框架
tags:
  - framework
  - install
  - django
  - pip
  - pycharm
date: 2023-1-02 00:00:00
---



## 创建项目

1. 安装框架依赖

```bash
pip3 install django
```

2. 创建项目

```bash
django-admin startproject projectName
```

> 包含manage.py和projectName目录。

3. 配置启动项

```bash
python3 manage.py runserver host:port
```

或使用pycharm编辑器配置启动

> 在此之前，如果需要使用远程虚拟环境，请先配置好远程的虚拟环境及解释器。

![settings-1](settings-1.png){height="256px" width="600px"}

![settings-2](settings-2.png){height="256px" width="600px"}

**小提示**：以上操作也可以直接通过pycharm创建django项目一键生成。

启动项目后可以在浏览器输入地址，默认是：http://127.0.0.1:8000

![start](start.png){height="256px" width="600px"}



## 文件介绍

`__init__.py`：包初始化文件。

`settings.py`：项目的配置文件。

`urls.py`：接口路由文件。

`views.py`：接口视图文件。

`asgi.py`：ASGI服务文件，基于ASGI协议网关接口的服务文件。

`wsgi.py`：WSGI服务文件，基于WSGI协议网关接口的服务文件。



## 第一个接口

1. 编写视图

```python projectName/views.py
from django.http import HttpResponse


def index(request):
    return HttpResponse("Welcome to Django")
```

2. 编写路由

```python projectName/urls.py
from django.contrib import admin
from django.urls import path
from . import views

urlpatterns = [
    path('admin/', admin.site.urls),
    path('index/', views.index),
]
```

3. 访问接口

```http
http://127.0.0.1:8000/index/
```

即可查看数据

![index](index.png){height="256px" width="600px"}
