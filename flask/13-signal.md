---
title: 13.信号
date: 2023-12-22 16:54:37
categories: Flask框架
tags: 
  - signal
  - blinker
---

[Flask中的Signals](https://flask.palletsprojects.com/en/latest/signals/)。

信号是一种轻量级方法，用于在应用程序和每个请求的生命周期中通知订阅者某些事件。当事件发生时，它会发出呼叫每个订阅者的信号。

# 内置信号

[Flask内置信号](https://flask.palletsprojects.com/en/latest/api/#core-signals-list)10个常用的信号：

1. before_render _template：模版渲染之前的信号。
2. template_rendered：模版渲染完成后的信号。
3. request_started：请求开始之前，在到达视图函数之前发送信号。
4. request_finished：请求结束时，在响应发送给客户端之前发送信号。
5. request_tearing_down：请求对象被销段时发送的信号，即使在请求过程中发生异常也会发送信号。
6. got_request_exception：在请求过程中抛出异常时发送信号，异常本身会通过exception传递到订阅（监听）的函数中。一般可以监听这个信号，来记录网站异常信息。
7. appcontext_tearing_down：应用上下文被销毁时发送的信号。
8. appcontext_pushed：应用上下文被推入到栈上时发送的信号。
9. appcontext_popped：应用上下文被推出栈时发送的信号。
10. message _flashed：调用了Flask的`flash`方法时发送的信号。

如`template_rendered`：

```python
from flask import template_rendered

# 方式一
def on_template_rendered(sender, template, context):
    print("模板渲染完成")

template_rendered.connect(on_template_rendered)

# 方式二
@template_rendered.connect
def on_template_rendered(sender, template, context):
    print("模板渲染完成")
```

# 自定义信号

## 创建信号

请确保已安装`blinker`库。

```python
from blinker import Namespace

# 创建一个命名空间
name_space = Namespace()
# 在命名空间中定义一个信号对象
signal_name = name_space.signal('signalName')
```

## 订阅信号

```python
# 方式一
def signal_handler(sender, **kwargs):
  print("信号处理逻辑")

signal_name.connect(signal_handler)

# 方式二
@signal_name.connect
def signal_handler(sender, **kwargs):
    print("信号处理逻辑")
```

## 发送信号

```python
signal_name.send(**kwargs)
```

