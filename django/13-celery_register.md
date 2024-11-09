---
title: 13.Celery异步及邮箱注册
category: Django框架
tags:
  - framework
  - django
  - celery
  - register
  - email
  - asynchronization
date: 2023-1-13 00:00:00
---



## celery异步

### 介绍

这里使用[celery](https://docs.celeryq.dev/en/stable/)异步处理库，专注于实时处理和任务调度的分布式任务队列，同时提供操作和维护分布式系统所需的工具。

特点：简单（丰富的文档），高效（每个进程可以处理百万个任务每分钟），灵活（每个部分都可以自定义拓展）。

组成：client（任务发出者），worker（任务处理者），broker（任务队列，中间人）。

### 使用

1. 安装

```bash
pip3 install celery
```

2. 配置celery main

```python celery_tasks/celery_main.py
import os

from celery import Celery

# 使用settings配置
os.environ.setdefault("DJANGO_SETTINGS_MODULE", "drf_study.settings")

# 实例化Celery
app = Celery("app")
# 引入配置
app.config_from_object("celery_tasks.celery_config")
# 监听文件夹任务
app.autodiscover_tasks(["celery_tasks.email", ])
```

3. 缓存配置

```python
# 连接的数据库，其中的%d占位几号库
LOCATION = "redis://127.0.0.1:6379/%d"
# 登录时使用的key模板，一般为 页面:功能:唯一标识
LOGIN_KEY_TEMPLATE = "forget:verification:%s"
# 设置过期时间
EXPIRE_TIME = 60 * 3
```

4. 配置broker

```python celery_tasks/celery_config.py
from config.dbs.redis_dev import LOCATION
# 配置处理任务队列
broker_url = LOCATION % 2
# 配置处理结果队列
result_backend = LOCATION % 3
```

5. 定义worker

```python celery_tasks/email/tasks.py
import logging

from django.core.mail import send_mail

from celery_tasks.celery_main import app
from drf_study.settings import DEFAULT_FROM_EMAIL

logger = logging.getLogger(__name__)


@app.task(name="send_email_task")
def send_email_task(email, msg):
    try:
        content = {
            "recipient_list": [email],
            "from_email": DEFAULT_FROM_EMAIL,
            "subject": "邮件标题",
            "message": f"邮件内容：{msg}",
            # "html_message": f"<h2>HTML文本内容，会直接覆盖message。</h2>"
        }
        res = send_mail(**content)
        if res:
            logger.info(f"邮件发送[成功][email: {email}][msg: {msg}]")
        else:
            logger.warn(f"邮件发送[失败][email: {email}][msg: {msg}]")
    except Exception as e:
        logger.error(f"邮件发送[异常][email: {email}][error: {e}]")
```

5. 定义client

在视图中使用

```python views.py
send_email_task.delay(email, msg)
```

### 启动服务

```bash
celery -A celery_tasks.tasks_main worker --loglevel=INFO

# 完整命令
python3 -m celery -A celery_tasks.celery_main worker --loglevel=INFO
```



## 发送邮件

详情参见[发送邮件 | Django 文档](https://docs.djangoproject.com/zh-hans/4.2/topics/email/)。

settings配置

```python config/qq_email_config.py
# 邮件客户端
EMAIL_HOST = "smtp.qq.com"
# 客户端端口
EMAIL_PORT = 25
# 发件人邮箱
EMAIL_HOST_USER = "2677232159@qq.com"
# 发件人授权码
EMAIL_HOST_PASSWORD = "xxx"
# 使用安全链
EMAIL_USE_TLS = True
# 默认发件人邮箱
DEFAULT_FROM_EMAIL = EMAIL_HOST_USER
```

将以上内容导入settings.py即可
