---
title: 3.Django相关配置
category: Django框架
tags:
  - framework
  - django
  - settings
  - pip
  - mysql
  - drf
  - logger
  - app
date: 2023-1-03 00:00:00
---



## 系统路径及时区

```python settings.py
sys.path.insert(0, str(BASE_DIR))
sys.path.insert(1, os.path.join(BASE_DIR, 'apps'))

LANGUAGE_CODE = 'zh-hans'

TIME_ZONE = 'Asia/Shanghai'

USE_I18N = True

USE_L10N = True

USE_TZ = False
```



## 模板配置

```python settings.py
TEMPLATES = [
  {
    'BACKEND': 'django.template.backends.django.DjangoTemplates',
		# 配置模板文件的根路径
    'DIRS': [BASE_DIR / 'templates']
    ,
    'APP_DIRS': True,
    'OPTIONS': {
      'context_processors': [
        # 上下文处理器
        'django.template.context_processors.debug',
        'django.template.context_processors.request',
        'django.contrib.auth.context_processors.auth',
        'django.contrib.messages.context_processors.messages',
      ],
    },
  },
]
```

> 可以通过注册上下文处理器（函数），让所有模板文件都可访问到公共数据。



## 静态文件

```python settings.py
STATIC_URL = 'static/'

STATICFILES_DIRS = [
	# 配置静态文件路径
  BASE_DIR / STATIC_URL,
]
```



## 数据库配置

1. 创建数据库及分配用户

```bash
# 创建数据库
CREATE DATABASE `dbName` CHARSET='utf8';

# 创建用户
CREATE USER userName IDENTIFIED BY 'password';

# 分配权限
GRANT ALL PRIVILEGES ON `dbName`.* TO userName@'%';
```

2. 安装pymysql

```bash
pip3 install pymysql
```

3. 配置MySQL

配置安装器

```python projectName/__init__.py
import pymysql

# 如报错mysqlclient错误则添加
pymysql.version_info = (1, 4, 3, "final", 0)
# 安装pymsql客户端
pymysql.install_as_MySQLdb()
```

配置mysql信息

```ini config/database/mysql_dev.ini
[client]
database = dbName
user = userName
password = password
host = 127.0.0.1
port = 3306
default-character-set = utf8
```

配置settings

```python settings.py
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.mysql',
        'OPTIONS': {
            'read_default_file': 'config/database/mysql_dev.ini',
        },
    }
}
```

或者直接配置在settings.py文件，但是不推荐。

```python settings.py
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.mysql',
        "NAME": "dbName",
        "USER": "userName",
        "PASSWORD": "password",
        "HOST": "127.0.0.1",
        "PORT": 3306
    }
}
```

> 参见[Databases | Django documentation | Django (djangoproject.com)](https://docs.djangoproject.com/en/4.1/ref/databases/#mysql-notes)。

4. 迁移与映射

```bash
# 迁移
python3 manage.py makemigrations [appName]

# 映射
python3 manage.py migrate [appName]
```

> 映射完后，数据库会自动根据模型生成对应的表。



## APP配置

APP：可以理解为是一个完整功能的模块，对于第三方APP，可以当做插件使用。

### drf导入

1. 安装

```bash
pip3 install djangorestframework
```

2. 注册

```python settings.py 
INSTALLED_APPS = [
  ...
  'rest_framework'
]
```

> 参见[Home - Django REST framework (django-rest-framework.org)](https://www.django-rest-framework.org/#installation)。

### 自定义app

1. 创建app

```bash
python3 manage.py startapp appName
```

> 可以使用pycharm编辑器的快捷键快速执行命令。
>
> windows： [Ctrl]{.kbd} + [Alt]{.kbd} + [R]{.kbd .red}
>
> mac：[option]{.kbd} + [R]{.kbd .red}


2. 注册app

```python settings.py 
INSTALLED_APPS = [
  ...
  'rest_framework',
  'appName'
]
```

> 可以将所有自定义的app移动到一个apps文件夹，将该文件夹设置为资源文件夹，并将其插入系统路径。
>
> ```python settings.py 
> # line-18
> sys.path.insert(0, str(BASE_DIR))
> sys.path.insert(1, os.path.join(BASE_DIR, 'apps'))



## 日志器

```python settings.py 
# 日志器
LOGGING = {
    'version': 1,
    'disable_existing_loggers': False,
    'formatters': {
        'verbose': {
            'format': '%(levelname)s %(asctime)s %(module)s %(lineno)d %(message)s',
        },
        'simple': {
            'format': '%(levelname)s %(module)s %(lineno)d %(message)s',
        },
    },
    'filters': {
        'require_debug_true': {
            '()': 'django.utils.log.RequireDebugTrue',
        },
    },
    'handlers': {
        'console': {
            'level': 'DEBUG',
            'filters': ['require_debug_true'],
            'class': 'logging.StreamHandler',
            'formatter': 'simple'
        },
        'file': {
            'level': 'INFO',
            'class': 'logging.handlers.RotatingFileHandler',
            # 日志文件路径
            'filename': BASE_DIR / 'logs/logName.log',
            'maxBytes': 300 * 1024 * 1024,
            'backupCount': 10,
            'formatter': 'verbose'
        }
    },
    'loggers': {
        # 定义一个名为django的日志器
        'django': {
            'handlers': ['console','file'],
            'propagate': True,
            # 接收的最低日志级别
            'level': 'INFO'
        },
    }
}
```

> 参见[日志 | Django 文档 | Django (djangoproject.com)](https://docs.djangoproject.com/zh-hans/4.1/topics/logging/)。



## 中间件

```python settings.py
MIDDLEWARE = [
    'django.middleware.security.SecurityMiddleware',
    'django.contrib.sessions.middleware.SessionMiddleware',
    'django.middleware.common.CommonMiddleware',
    'django.middleware.csrf.CsrfViewMiddleware',
    'django.contrib.auth.middleware.AuthenticationMiddleware',
    'django.contrib.messages.middleware.MessageMiddleware',
    'django.middleware.clickjacking.XFrameOptionsMiddleware',
]
```

> 自定义的中间件需要在此激活。



## 限流

详情参见[Throttling - Django REST framework](https://www.django-rest-framework.org/api-guide/throttling/)。

全局限流配置如下：

```python settings.py
REST_FRAMEWORK = {
  'DEFAULT_THROTTLE_CLASSES': [
    'rest_framework.throttling.AnonRateThrottle',
    'rest_framework.throttling.UserRateThrottle'
  ],
  'DEFAULT_THROTTLE_RATES': {
    # 匿名用户
    'anon': '100/day',
    # 登录用户
    'user': '1000/day'
  }
}
```



## 缓存

```python settings.py
# 配置缓存
CACHES = {
    "default": {
        "BACKEND": "django_redis.cache.RedisCache",
        "LOCATION": LOCATION % 0,
        "OPTIONS": {
            "CLIENT_CLASS": "django_redis.client.DefaultClient",
            "CONNECTION_POOL_KWARGS": {
                "max_connections": 100,
                "decode_responses": True
            }
        }
    },
    "缓存名": {
        "BACKEND": "django_redis.cache.RedisCache",
        "LOCATION": LOCATION % 缓存库,
        "OPTIONS": {
            "CLIENT_CLASS": "django_redis.client.DefaultClient",
            "CONNECTION_POOL_KWARGS": {
                "max_connections": 100,
                "decode_responses": False
            }
        }
    }
}
```

缓存响应

1. 安装响应缓存依赖

```bash
pip3 install drf-extensions -i https://pypi.douban.com/simple
```

2. 配置缓存

```python settings.py
# 设置CacheResponseMixin缓存响应数据的配置
REST_FRAMEWORK_EXTENSIONS = {
    'DEFAULT_USE_CACHE': '缓存名',
    'DEFAULT_CACHE_RESPONSE_TIMEOUT': 60 * 60
}
```

3. 响应缓存

```python views.py
from rest_framework_extensions.cache.mixins import CacheResponseMixin

class AreaViewSet(CacheResponseMixin, ModelViewSet):
```
