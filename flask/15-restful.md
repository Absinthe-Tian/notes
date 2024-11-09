---
title: 15.RESTful与GraphQL
date: 2023-12-23 18:25:05
categories: Flask框架
tags:
  - restful
  - GraphQL
  - API
  - 规范
  - flask-restful
---

# 介绍

REST（Representational State Transfer）是一组架构约束条件和原则，满足这些条件和原则的应用程序或设计称为RESTful。

它是一种基于HTTP/HTTPS协议的软件架构/设计风格，并不是标准，主要用于构建Web服务。

在RESTful Web服务中，每个资源都有一个唯一的URL，并通过HTTP/HTTPS方法来操作这个资源。

## 特点

1. 无状态：每个请求都应该包含足够的信息，以便服务器能够理解请求，并生成合适的响应，而不需要依赖于任何先前的请求或会话状态。
2. 缓存：RESTful Web服务可以轻松实现缓存，以提高性能和可伸缩性。
3. 统一的接口：RESTful Web服务使用标准的HTTP方法和状态码，使得API具有一致的、易于理解的接口。
4. 分层系统：RESTful Web服务通过将应用逻辑分为多层，使得系统更加模块化，易于维护和扩展。
5. 可扩展性：RESTful Web服务可以轻松地通过添加额外的资源和HTTP方法来扩展功能。

## URL规则

* 使用http或https协议。
* 数据传输采用JSON格式。
* 不能用动词，只能用名词，复数加s。

## 请求方式

* GET：获取资源。
* POST：创建或修改资源。
* PUT：更新资源（客户端提供所有改变的数据）。
* PATCH：更新资源（客户端仅提供需要改变的数据）。
* DELETE：删除资源。

## 状态码

| 状态码 |          描述          |         原因         |
| :----: | :--------------------: | :------------------: |
|  200   |           OK           |       请求成功       |
|  201   |        Created         |      资源已创建      |
|  204   |       No Content       |  响应不包含任何内容  |
|  400   |      Bad Request       |     请求格式错误     |
|  401   |      Unauthorized      |     需要身份验证     |
|  403   |       Forbidden        |     无权访问资源     |
|  404   |       Not Found        |      资源不存在      |
|  405   |   Method Not Allowed   |    请求方法不允许    |
|  406   |     Not Acceptable     | 请求头不满足资源需求 |
|  409   |        Conflict        |       请求冲突       |
|  415   | Unsupported Media Type |   不支持的媒体类型   |
|  500   | Internal Server Error  |    服务器内部错误    |
|  503   |  Service Unavailable   |   服务器暂时不可用   |

# Flask-Restful

[Flask-RESTful](https://flask-restful.readthedocs.io/en/latest/)是基于flask的第三方插件，它可以帮助我们更快的实现RESTful API。

安装：

```bash
pip3 install flask-restful
```

## 简单使用

1. 与`MethodView`相同，需要通过类来继承，并重写`get`、`post`、`put`、`delete`方法。
2. 需要使用`Api.add_resource(resource, urls)`注册路由，可以注册多个路由，以第一个为主。
3. 该类默认的endpoint为**类名小写**。

```python
from flask import Flask, url_for
from flask_restful import Resource, Api

app = Flask(__name__)

# 需要管理app或blueprint
api = Api(app)

# 定义Restful视图
class LoginView(Resource):
    def get(self):
        return {"method": "GET"}

    def post(self):
        return {"method": "POST"}

# 注册resource路由
api.add_resource(LoginView, "/login/")

if __name__ == "__main__":
    app.run(debug=True)

```

## 参数校验

我们可以使用`RequestParser`来对请求参数进行校验，但是由于在 Python 中，类型之间可以隐式地转换，因此这样的校验并不完全准确。

**注意**：验证没有通过时会直接返回响应，后续的内容不再执行。

```python
from flask import Flask
from flask_restful import Resource, Api
from flask_restful.reqparse import RequestParser

app = Flask(__name__)
api = Api(app)

class LoginView(Resource):

    def post(self):
      	# 定义校验字段约束
        parser = RequestParser()
        parser.add_argument("username", type=str, required=True, trim=True)
        parser.add_argument("password", type=str, required=True, trim=True)
        # 字段校验
        args = parser.parse_args()
        
        if args.get("username") == "admin" and args.get("password") == "123456":
            return {"msg": "登录成功",}
        else:
            return {"msg": "用户名或密码错误！",}

api.add_resource(LoginView, "/login/")

if __name__ == "__main__":
    app.run(debug=True)
```

`add_argument`参数如下：

1. `name`：参数的名称。
2. `type`：参数的类型。可以使用Python自带的数据类型，也可以使用`flask_restful.inputs`下的数据类型。
3. `default`：参数的默认值。
4. `required`：参数是否必需。
5. `choices`：参数的可选值列表。
6. `trim`：是否去除参数值的前导和尾随空格。
7. `action`：参数的操作。
8. `store_missing`：是否存储缺失的参数。这是一个较少使用的参数，因为通常我们只关心存在的参数。
9. `case_sensitive`：参数名称是否区分大小写。
10. `choices_callback`：一个可调用对象，用于验证参数值是否在 `choices` 列表中。
11. `type_callback`：一个可调用对象，用于将参数值转换为指定类型。
12. `location`：参数的位置。
13. `store_missing_location`：存储缺失参数的位置。
14. `dest`：参数的目标名称。
15. `help`：参数的帮助信息。
16. `ignore`：是否忽略参数。

## 规范返回值

在开发中，常常会规范返回值的字段。

**注意**：如果返回了规范中没有定义的字段，则会忽略，不再进行返回。

```python
from flask_restful import Resource, Api, fields, marshal_with

class LoginView(Resource):
  	
    # 定义返回值的字段
    resource_field = {
        "code": fields.Integer(),
        "msg": fields.String(),
        "data": fields.Nested({
            "username": fields.String,
            "password": fields.String
        })
    }
		
    # marshal_with 约束视图的返回值
    @marshal_with(resource_field)
    def post(self):
        return {
            "code": 0,
          	"msg": "获取成功",
          	"data": {
                "username": request.json.get("username"),
                "password": request.json.get("password")
            }
        }
```

> 也可以直接返回一个对象，会自动解析为JSON数据，也会规范返回值。

fields中的类型对象包含两个参数：

* attribute：指定属性名，即value从返回值的指定key获取，而不是根据类型对象的key获取（一般用于模型类字段名与规范返回值的字段名不一致）。
* default：指定默认值（如果该字段有原值，则不生效）。

## 模板渲染

不推荐！因为Restful要求我们返回JSON数据，而不应该是HTML。

添加如下代码即可正常渲染模板：

```python
# 解决渲染模板乱码问题
app.config["RESTFUL_JSON"] = dict(ensure_ascii=False)

@api.representation("text/html")
def html_output(data, code, headers):
    if isinstance(data, str):
        return Response(data)
    else:
        return Response(jsonify(data))
```

# GraphQL

[GraphQL](https://graphql.cn/)是一种查询语言API，它与服务端运行时一起使用，通过数据定义的类型系统执行查询。

它不依赖于任何特定的数据库或存储引擎，而是利用现有的代码和数据。GraphQL允许前端开发人员自由查询所需的数据，而后端开发人员可以将客户端应用程序需求与其后端系统体系结构分离。
