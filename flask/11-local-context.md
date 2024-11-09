---
title: 11.Local、Context与g对象
date: 2023-12-21 14:03:35
categories: Flask框架
tags: 
  - 线程安全
  - local
  - context
  - g
---

# Local

当需要实现并发效果时，一般采取多线程，但是线程中的数据互相不隔离，在数据操作时并不安全。

![flask中的多线程](image-20231221190834866.png)

在Flask中，如request对象、session对象等等，其实是绑定到了一个`werkzeug.local.Local`对象上，封装于`threading.local`。

每个Local对象的属性都只能由自己的线程操作，与其他线程隔离互不干扰，也叫`ThreadLocal`对象/变量。

```python
from werkzeug.local import Local
from threading import Thread, local

# threading中的local对象
# local = local()

# werkzeug中的Local对象
local = Local()
local.request = "主线程的request对象"

class MyThread(Thread):
  def run(self):
    local.request = "子线程的request对象"
    print(local.request)
    
mt = MyThread()
mt.start()
mt.join()
print(local.request)
```

# Context

上下文即表示某个实例可全局使用。

Flask底层已经将视图中的应用上下文和请求上下文都推入到了对应的栈中了，不需要我们关心视图中上下文的内容。

## 应用上下文

应用上下文用于表示当前活动的 Flask 应用实例。

这在某些情况下非常有用，例如在多个应用共享相同的资源（如数据库连接或配置变量）的情况下，或者在开发扩展或插件时，需要知道当前的应用实例。

Flask中的应用上下文由`Flask.app_context()`上下文管理器创建，它允许当前应用实例设置为全局访问，可操作应用实例的属性和方法，使用`current_app`代理访问当前应用实例。

### 视图使用

在视图中直接调用`current_app`即可使用应用上下文。

```python
from flask import Flask, current_app

app = Flask(__name__)

@app.get('/')
def index_view():
  print(current_app.name)
  return "index"
```

### 外部使用

应用上下文是一个栈（`_app_ctx_stack`），在外部直接使用`current_app`会报错，需要我们提供一个`app_context()`。

```python
from flask import Flask, current_app

app = Flask(__name__)

# 方式一
# 创建一个应用上下文
app_ctx = app.app_context()
# push到栈中
app_ctx.push()
# 使用
print(current_app.name)

# 方式二
with app.app_context():
  print(current_app.name)
```

## 请求上下文

用于表示当前活动的 HTTP 请求。请求上下文允许您在处理请求时访问和操作与请求相关的资源，例如请求路径、查询参数、表单数据、会话数据等。

请求上下文通常是通过 `Flask.request_context()` 上下文管理器创建的。当激活上下文时，它会将当前请求对象设置为全局可访问的。这允许在上下文中访问和操作请求对象的属性和方法，以及使用 `request` 代理访问当前请求对象。

### 视图使用

在视图中，直接使用`request`对象即可，Flask会自动的将当前的请求上下文对象推进`_request_ctx_stack`的栈顶。

```python
from flask import Flask, request

app = Flask(__name__)

@app.get('/')
def index_view():
  print(request.methods)
  return "index"
```

### 外部使用

由于需要一些配置，这里就使用测试环境。

```python
from flask import Flask, request

app = Flask(__name__)

with app.test_request_context():
    print(request.method)
```

# g对象

在 Flask 中，`g` 对象是一个全局对象，用于在请求上下文中存储和共享数据。

它是一个线程安全的全局变量，可以在视图函数、模板和 Flask 扩展中使用。`g` 对象的生命周期与请求上下文相同，当请求结束时，`g` 对象中的数据会被销毁。

```python
from flask import g

g.name = "g对象"
```

