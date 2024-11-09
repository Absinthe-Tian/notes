---
title: 4.路由参数
date: 2023-12-17 16:51:22
categories: Flask框架
tags: 
  - params
  - query
  - SEO
---

# params参数

## 使用

```python
@app.route('/details/<int:uid>/')
def details(uid):
    return f"详情uid：{uid}"
```

`converter:variable`内置可指定类型：

* string：默认使用的字符串类型。
* int：只能传递整型。
* float：只能传递浮点型。
* path：可接收斜杠的特殊string类型。
* uuid：只能传递符合uuid的字符串。
* any：可以指定多个同级路径，示例如下。

```python
# 访问 /student/1/ 或 /teacher/1/
@app.get('/<any(student,teacher):temp>/<int:uid>/')
def any_view(temp, uid):
    if temp == "student":
        return f"学生uid：{uid}"
    elif temp == "teacher":
        return f"老师uid：{uid}"
```

## 自定义类型

参数类型由*werkzeug.routing.BaseConverter*作为类型转换器基类，继承并重写即可自定义参数类型。

```python
from werkzeug.routing import BaseConverter

# 重写类型转换器基类
class PhoneConverter(BaseConverter):
    regex = '1[3-9]\d{9}'
    
    # 拿到params后会执行该方法处理，并将返回值传给视图函数形参。
    def to_python(self, value: str):
        value = "Phone：" + value
        return value

# 注册转换器
app.url_map.converters.setdefault("phone", PhoneConverter)

@app.route('/phone/<phone:param>/')
def phone_view(param):
    return params
```

# query参数

```python
from flask import Flask, request

@app.route('/login/')
def login_view():
  	# 方式一
    username = request.args.get('username')
    password = request.args.get('pwd')
    # 方式二
    username = request.values.get('username')
    password = request.values.get('pwd')
    
    return f"用户名：{username}，密码：{password}。"
```

> 通过`request`对象获取请求数据。

# 总结

如果该应用需要SEO优化，希望更容易被搜索引擎排前，则推荐使用params参数。

如果不在乎SEO优化，则推荐query参数。
