---
title: 10.中间件、分页器及自动化接口文档
category: Django框架
tags:
  - framework
  - django
  - middleware
  - thirdparty
  - paging
date: 2023-1-10 00:00:00
---



## 中间件

中间件可以在请求到达路由之前，响应发送之前进行一些统一操作。

详情参见[中间件](https://docs.djangoproject.com/zh-hans/4.2/topics/http/middleware/)。

```python mymiddleware.py
from django.utils.deprecation import MiddleWareMixin
from django.http import HttpResponse

class MyException(MiddleWareMixin):
  
  # 视图之前执行
  def process_request(self, request):
    pass
  
  # 视图之前，process_request之后执行
  def process_view(self,request, view_func, view_args, view_kwargs):
    pass
  
  # 视图之后执行
  def process_response(self, request):
    pass
```

> 简单自定义，在对应的函数中进行操作即可。
>
> 或者重写`__init__`和`__call__`方法。
>
> 定义后的需要[激活中间件](https://www.knight-blog.cn/django/5a8fc734/#%E4%B8%AD%E9%97%B4%E4%BB%B6)。



## 分页器

详情参见[分页器 | Django 文档](https://docs.djangoproject.com/zh-hans/4.2/ref/paginator/)。

```python views.py
pg = Paginator(data, 5)

pg1 = pg.page(1)

pg1.has_next()
```

> 将data数据，以每页5条数据进行分页。

Django中还有很多其他功能，请自行参考官方文档进行学习与使用。



## 自动化接口文档

详情参考[Core API](https://www.coreapi.org/)。

### 安装

1. 安装依赖

```bash
pip3 install coreapi
```

2. 配置

```python settings.py
REST_FRAMEWORK = {
  ...
  # 自动化API文档
  'DEFAULT_SCHEMA_CLASS': "rest_framework.schemas.coreapi.AutoSchema"
}
```

3. 添加路由

```python urls.py
from django.contrib import admin
from django.urls import path
from rest_framework.documentation import include_docs_urls

urlpatterns = [
  path('admin/', admin.site.urls),
  path('docs/', include_docs_urls())
]
```

4. 访问地址

```http
http://127.0.0.1:8000/docs/
```

### 添加注释

```python views.py
class UserView(View):
  """
  视图类的注释
  """
 	pass

class UserView(View):
  """
  get:
  这是get方法的注释
  
  post:
  这是post方法的注释
  """
  pass
```

1. 可在序列化器中添加`help_text`属性进行说明

```python
extra_kwargs = {
  # 字段名
  'first_name': {
    # 字段Description
    'help_text': '姓氏，字符串，1-4位',
    # 字段限制
    'min_length': 1,
    'max_length': 4,
    # 错误提示
    'error_messages': {
      'min_length': '最少1个字符',
      'max_length': '最多4个字符'
    }
  }
}
```

2. 可以在视图中指定scheme

```python views.py
class MyView(APIView):
    schema = AutoSchema(manual_fields=[
        coreapi.Field('username', required=True, location='path', description='Username of the user'),
        coreapi.Field('query', required=False, location='query', description='Search query'),
        coreapi.Field('body', required=True, location='body', description='User data', schema=MySerializer()),
    ])
```

> APIView是指定schema的基类，ViewSet等等都可使用。

3. 可以在视图函数中使用scheme自定义字段

```python
from rest_framework.decorators import schema, api_view
from rest_framework.schemas import AutoSchema
import coreapi
import coreschema

email_schema = AutoSchema(manual_fields=[
    coreapi.Field(name="email", 
                  required=True, 
                  location="form", 
                  schema=coreschema.String(description="邮件地址")),
])


@api_view(["POST"])
@schema(email_schema)
def send_email(request):
  pass
```

> location：字段是哪个位置的参数，如query、header、path、form。



## 响应缓存

详情参见[DRF extensions](https://chibisov.github.io/drf-extensions/docs/)。

### 安装

```bash
pip3 install drf-extensions
```

### 配置

```python settings.py
CACHES = {
  "cache_name": {
        "BACKEND": "django_redis.cache.RedisCache",
        "LOCATION": "redis://127.0.0.1:6379/1",
        "OPTIONS": {
            "CLIENT_CLASS": "django_redis.client.DefaultClient",
            "CONNECTION_POOL_KWARGS": {
                "max_connections": 100,
                "decode_responses": True
            }
        }
    }
}

REST_FRAMEWORK_EXTENSIONS = {
  "DEFAULT_USE_CACHE": "cache_name",
  "DEFAULT_CACHE_RESPONSE_TIMEOUT": 60 * 15
}
```

### 缓存

```python views.py
from rest_framework_extensions.cache.mixins import CacheResponseMixin

class xxxViewSet(CacheResponseMixin, ModelViewSet):
  pass
```

