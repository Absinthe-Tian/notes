---
title: 12.图片验证码及登录
category: Django框架
tags:
  - framework
  - django
  - pillow
  - verify
  - login
date: 2023-1-12 00:00:00
---



## Pillow

这里使用[Python Pillow](https://python-pillow.org/)图形处理库。

安装

```bash
pip3 install pillow
```

### 定义工具类

```python utils/verify_code.py
import random
import string

from PIL import Image, ImageFont, ImageDraw, ImageFilter


class ImageVerify:

    def __init__(self, x=140, y=40, length=6, size=28):
        """
        初始化图片
        :param x: 水平长度
        :param y: 垂直长度
        :param length: 字符长度
        :param size: 字符大小
        """
        self.x = x
        self.y = y
        self.length = length
        self.size = size

    def random_code(self):
        """
        生成随机字符串
        :return: code
        """
        source = string.digits + string.ascii_letters
        code = ''.join(random.choices(source, k=self.length))
        return code

    def random_color(self, start=0, end=255):
        """
        生成随机RGB值
        :param start: RGB的起始值
        :param end: RGB的终止值
        :return: (R, G, B)
        """
        rgb = [random.randint(start, end) for i in range(3)]
        return tuple(rgb)

    def random_lines(self, draw, min_count=4, max_count=8):
        """
        生成随机干扰线
        :param draw: 画笔对象
        :param min_count: 干扰线最少条数
        :param max_count: 干扰线最多条数
        :return:
        """
        for i in range(random.randint(min_count, max_count)):
            lines_coordinate = [(random.randint(0, self.x), random.randint(0, self.y)) for j in range(2)]
            draw.line(lines_coordinate, fill=self.random_color())

    def random_points(self, draw, min_count=100, max_count=300):
        """
        生成随机干扰点
        :param draw: 画笔对象
        :param min_count: 干扰点最少个数
        :param max_count: 干扰点最多个数
        :return:
        """
        for i in range(random.randint(min_count, max_count)):
            x = random.randint(0, self.x)
            y = random.randint(0, self.y)
            draw.point((x, y), fill=self.random_color())

    def verify_code(self):
        """
        生成验证码图片
        :return:
        """
        # 创建画布
        image = Image.new("RGB", (self.x, self.y), (255, 255, 255))

        # 创建字体
        font = ImageFont.truetype("static/字帮玩酷体.ttf", self.size)

        # 创建画笔
        draw = ImageDraw.Draw(image)

        # 绘制干扰线和干扰点
        self.random_lines(draw)
        self.random_points(draw)

        # 生成随机code
        code = self.random_code()

        # 绘制code
        for i in range(self.length):
            draw.text(((self.size - 10) * i + random.randint(15, 25), random.randint(0, self.y - self.size)), code[i],
                      self.random_color(64, 128), font)
        
        # 添加滤镜
        image = image.filter(ImageFilter.SMOOTH)
        return image, code
```

### 添加缓存

1. 安装

```bash
pip3 install django-redis
```

2. 配置

连接配置

```python config/dbs/redis_dev.py
# 连接的数据库，其中的%d占位几号库
LOCATION = "redis://127.0.0.1:6379/%d"
# 登录时使用的key模板，页面:功能:唯一标识
LOGIN_KEY_TEMPLATE = "login:verification:%s"
# 设置过期时间
EXPIRE_TIME = 60 * 3
```

对应的settings配置]([3.Django 相关配置 - django | Knight-Blog = Knight-Blog = 守望者 @Knight-Blog](https://www.knight-blog.cn/django/5a8fc734/)#缓存)。

3. 使用

```python
redis_conn = get_redis_connection(alias="cacheName")
redis_conn.set(key, value, expire_time)
redis_conn.get(key)
redis_conn.delete(key)
```

### 添加接口

1. 视图

```python users/views.py
import io
from django.http import HttpResponse
from django_redis import get_redis_connection
from config.dbs.redis_dev import LOGIN_KEY_TEMPLATE, EXPIRE_TIME
from utils.verify_code import ImageVerify


def get_verify_code(request, uuid):
    """
    获取图片验证码
    :return:
    """
    iv = ImageVerify()
    img, code = iv.verify_code()
    img_bytes = io.BytesIO()
    img.save(img_bytes, format="PNG")
    # 获取redis连接池
    redis_conn = get_redis_connection(alias="verify")
    # 设置数据
    redis_conn.set(LOGIN_KEY_TEMPLATE % uuid, code, EXPIRE_TIME)
    return HttpResponse(img_bytes.getvalue(), content_type="image/png")
```

2. 路由

```python users/urls.py
from django.urls import path
from . import views

urlpatterns = [
    path("verify/<uuid:uuid>/", views.get_verify_code)
]
```

3. 请求

```http
http://127.0.0.1:8000/users/verify/065e15ba-3a5a-4ff9-b112-3fedb77af8df/
```

4. 自定义验证

```python utils/auth_backend.py
from django.contrib.auth.backends import ModelBackend
from django.contrib.auth.models import User
from django.db.models import Q
from django_redis import get_redis_connection
from rest_framework import serializers

from config.dbs.redis_dev import LOGIN_KEY_TEMPLATE


class CustomBackend(ModelBackend):
    def authenticate(self, request, username=None, password=None, **kwargs):
        verify_code = request.data.get("verify")
        uuid = request.data.get("uuid")

        redis_conn = get_redis_connection(alias="verify")
        redis_code = redis_conn.get(LOGIN_KEY_TEMPLATE % uuid)
        redis_conn.delete(LOGIN_KEY_TEMPLATE % uuid)

        if redis_code is None:
            raise serializers.ValidationError({'msg': '验证码失效'})
        if verify_code.upper() != redis_code.upper():
            raise serializers.ValidationError({'msg': '验证码错误'})

        try:
            user = User.objects.get(Q(username=username) | Q(email=username))
            if user.check_password(password):
                return user
        except Exception as e:
            raise e
```

配置settings

```python settings.py
AUTHENTICATION_BACKENDS = (
    "utils.auth_backend.CustomBackend",
)
```

5. 请求

```json
{
    "username": "2677232159@qq.com",
    "password": "qwe123",
    "verify": "puuazz",
    "uuid": "065e15ba-3a5a-4ff9-b112-3fedb77af8df"
}
```

