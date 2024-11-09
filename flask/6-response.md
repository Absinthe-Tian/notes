---
title: 6.响应
date: 2023-12-18 15:45:58
categories: Flask框架
tags: 
  - url_for
  - html
  - json
  - tuple
  - response
---

# url_for

效果：通过视图函数名获取路由地址。

作用：

* 以后修改某个URL，但是没有修改URL对应的函数名，配合重定向就不必全部替换原来的URL。
* 帮助我们转义一些特殊字符和unicode字符串。

```python
url_for("endpoint", param1=1, param2=2, _external=True)
```

> endpoint：视图函数endpoint（默认取函数名或`as_view`指定的name属性）。
>
> param：params参数（有则匹配，否则视为query参数）。
>
> _external：拼接完整地址。

# 重定向

301：永久重定向（旧网址的废弃）

302：暂时重定向（未登录用户跳转）

```python
@app.get('/info/', endpoint='info')
def info_view():
  	# return redirect('/login/', code=302)
    return redirect(url_for("login_view"), code=302)
```

# 响应类型

## HTML

Flask允许我们直接返回字符串，它会将我们的字符串包裹在HTML页面中返回。

```python
@app.route('/')
def index_view():
    # 自动转换为HTML数据
    return "<h1>Hello Flask</h1>"
```

## JSON

Flask允许我们直接返回字典，它会将我们的字典转换为JSON数据返回。

```python
@app.route('/json/')
def json_view():
    # 自动转换为JSON数据
    return {"key": "value"}
```

## Tuple

Flask允许我们返回特定格式的元祖，它会对元祖进行拆包并进行相应的处理进行返回。

```python
@app.route('/tuple/')
def tuple_view():
    # 自动拆包处理
    return '这是响应内容', 200
```

> 可返回格式：
>
> (response, status)
>
> (response, headers)
>
> (response, status, headers)
>
> 其中：
>
> response：返回的响应数据。
>
> status：重载响应状态码。
>
> header：额外报头，可以是一个列表或字典。

## Response

Flask给我们内置了Response对象进行返回。

```python
from flask import Response, make_response

@app.route('/home1/')
def home1_view():
    return Response("这是响应内容", status=200)
  
@app.route('/home2/')
def home2_view():
  	response = make_response("这是响应内容")
    response.status = 200
    
    return response
```

