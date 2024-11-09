---
title: 8.通信状态保持
category: Django框架
tags:
  - framework
  - django
  - session
  - cookie
  - token
  - jwt
date: 2023-1-08 00:00:00
---



由于http协议是无状态的，每次请求都是一次新的请求，不会保存之前的通信状态，这对用户是很不友好的，为此需要通过一些技术来保存用户的登录状态。



## Cookie

```python
def get(self, request):
  response = HttpResponse()
  # 添加/修改cookie，max_age存活秒数，expires过期时间
  response.set_cookie("key", value, max_age=0, expires=datetime.datetime(2023,1,8))
  # 获取cookie
  request.COOKIES.get("key")
  # 删除cookie
  response.delete_cookie("key")
```

> 所有的通信数据都保存在客户端（浏览器），不安全，用户可清理。



## Session

```python
def get(self, request):
  # 获取session
  request.session.get("key", default_value)
  # 添加/修改session
  request.session["key"] = value
  # 设置过期时间，默认两周，0：关闭浏览器过期
  request.session.set_expiry(0)
  # 清空当前session
  request.session.flush()
```

> 所有的通信数据都保存在服务端，占用服务器资源。



## Token

是服务端生成的一串加密字符串、以作客户端进行请求的一个“令牌”。当用户第一次使用账号密码成功进行登录后，服务器便生成一个Token及Token失效时间并将此返回给客户端，若成功登陆，以后客户端只需在有效时间内带上这个Token前来请求数据即可，无需再次带上用户名和密码。



JWT（JSON Web Token）：是一个开发标准的Token，一般应用于Web服务，包含Header、Payload、Signature三个部分，并由`.`连接，如`xxx.yyy.zzz`。



我们需要使用的是[Simple JWT ](https://django-rest-framework-simplejwt.readthedocs.io/en/latest/)。

其他[Django REST framework JWT](https://jpadilla.github.io/django-rest-framework-jwt/)。

### 基本使用

1. 安装依赖

```bash
pip3 install djangorestframework-simplejwt
```

2. 配置

```python settings.py
INSTALLED_APPS = [
  'django.contrib.admin',
  'django.contrib.auth',
  'django.contrib.contenttypes',
  'django.contrib.sessions',
  'django.contrib.messages',
  'django.contrib.staticfiles',
  'rest_framework',
  'rest_framework_simplejwt'
]

REST_FRAMEWORK = {
  ...
  # 配置验证方式
  'DEFAULT_AUTHENTICATION_CLASSES': (
    # jwt认证
    'rest_framework_simplejwt.authentication.JWTAuthentication',
    # session认证
    'rest_framework.authentication.SessionAuthentication',
    # 认证基类
    'rest_framework.authentication.BasicAuthentication',
  )
}

# 配置JWT
SIMPLE_JWT = {
  # access有效时间
  "ACCESS_TOKEN_LIFETIME": timedelta(minutes=5),
  # refresh有效时间
  "REFRESH_TOKEN_LIFETIME": timedelta(days=1),
  # 是否替换Token
  "ROTATE_REFRESH_TOKENS": False,
  # 余量
  "LEEWAY": 0,
  ...
}
```

> 更多配置参见[设置 — 简单的 JWT](https://django-rest-framework-simplejwt.readthedocs.io/en/latest/settings.html)。

3. 配置路由

```python urls.py
from rest_framework_simplejwt.views import (
  TokenObtainPairView,
  TokenRefreshView,
)

urlpatterns = [
  ...
  path('api/token/', TokenObtainPairView.as_view(), name='token_obtain_pair'),
  path('api/token/refresh/', TokenRefreshView.as_view(), name='token_refresh'),
  ...
]
```

4. 使用流程

调用`api/token/`接口获取access Token与refresh Token，访问带有权限的接口时，需要在请求头中的Authorization携带Bearer Token=access Token。

> 默认access Token有5分钟时效，5分钟过期后可通过之前获取的refresh Token发送到`api/token/refresh/`接口，以获取刷新后的access Token。refresh Token默认时效24小时。

### 自定义校验字段

不仅仅使用username和password进行校验，还可以自定义为email和password进行校验。

```python utils/auth_backend.py
from django.contrib.auth.backends import ModelBackend
from django.db.models import Q

class CustomBackend(ModelBackend):
    def authenticate(self, request, username=None, password=None, **kwargs):
      	
        try:
            user = User.objects.get(Q(username=username) | Q(email=username))
            if user.check_password(password):
                return user
        except Exception as e:
            raise e
```

settings配置

```python settings.py
AUTHENTICATION_BACKENDS = (
    'utils.auth_backend.CustomBackend',
)
```

### 自定义返回数据

返回自定义字段的数据

1. 添加序列化器

```python utils/my_token_obtain.py
from rest_framework import serializers
from rest_framework_simplejwt.serializers import TokenObtainPairSerializer
from django.contrib.auth import authenticate


class CustomTokenObtainPairSerializer(TokenObtainPairSerializer):
    # 定义自定义的用户信息字段
    user_id = serializers.IntegerField(source='user.id', read_only=True)

    # 验证用户信息
    def validate(self, attrs):
        # 调用父类的 validate() 方法进行默认验证
        data = super().validate(attrs)

        # 获取当前请求的用户信息
        user = authenticate(username=attrs['username'], password=attrs['password'])

        # 将 user 的 id 添加到返回数据中
        data['user_id'] = user.id

        return data
```

2. 指定序列化器

```python settings.py
SIMPLE_JWT = {
  ...
	# 指定序列化器
  "TOKEN_OBTAIN_SERIALIZER": "utils.my_token.CustomTokenObtainPairSerializer",
}
```

### 手动创建令牌

```python
from rest_framework_simplejwt.tokens import RefreshToken

def get_tokens_for_user(user):
    refresh = RefreshToken.for_user(user)

    return {
        'refresh': str(refresh),
        'access': str(refresh.access_token),
    }
```

