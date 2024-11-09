---
title: 8.视图
date: 2023-12-19 13:57:00
categories: Flask框架
tags: 
  - view
  - route
  - url
---

# 视图函数

给视图函数添加路由有两种方式。

方式一：route

```python
@app.route('/', methods=['GET'], endpoint='index')
def index_view():
  return "Hello Flask"
```

> 该方式底层依然是使用`add_url_rule`方法实现的。
>
> 平替route方法还有：get、post、put、patch、delete等。

方式二：add_url_rule

```python
def index_view():
  return "Hello Flask"

app.add_url_rule(rule='/', view_func=index_view, methods=['GET'], endpoint='index')
```

> 该方式可以将视图与路由分离，便于管理。

# 视图类

Flask允许我们使用类视图来编写我们的业务逻辑代码。

 可以继承，从而把一些共性的代码提取到父类中，子类继承即。

但是一般情况下用的比较少，根据实际情况使用。

视图基类：

```python
from flask.views import View

# 定义视图类
class MyView(View):
  	# 重写请求方法
    def dispatch_request(self):
        return "Hello Flask"

# 注册路由
app.add_url_rule('/', view_func=MyView.as_view('index'), endpoint='index')

# 请求测试
with app.test_request_context():
    print(url_for('index'))
```

调度视图类：

重写get、post、put、patch、delete等方法。

```python
from flask.views import MethodView

# 定义视图类
class LoginView(MethodView):
  	# GET请求时调用
    def get(self):
        return render_template('login.html')
		
    # POST请求时调用
    def post(self):
        uname = request.form.get("uname")
        pwd = request.form.get("pwd")
        if uname == "qwe" and pwd == "123":
            return "登录成功"
        else:
            return "登录失败"


# 注册路由
app.add_url_rule('/login/', view_func=LoginView.as_view("login"))
```

# 附加功能

我们可以利用装饰器给视图函数及视图类添加一些公共的额外功能。

功能装饰器定义：

```python
from functools import wraps


def func_required(func):
  @wraps(func)
  def wrapper(*args, **kwargs):
    #额外功能代码
    pass
  return wrapper
```

功能装饰器使用：

```python
# 视图函数的使用
@app.route('/')
@func_required
def index():
  # 编写自己的业务代码
  return "Hello Flask"

# 视图类的使用
class IndexView(MethodView):
  decorators = [func_required, ]
  def get(self):
    # 编写自己的业务代码
		return "Hello Flask"

```

