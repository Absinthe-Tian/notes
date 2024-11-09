---
title: 10.权限认证
date: 2023-12-20 19:01:43
categories: Flask框架
tags: 
  - cookie
  - session
  - token
  - jwt
---

Web应用程序是使用HTTP协议传输数据的。HTTP协议是无状态的协议。一旦数据交换完毕，客户端与服务器端的连接就会关闭。再次交换数据需要建立新的连接，这就意味着服务器无法从连接上跟踪会话。

# cookie

cookie由服务端颁发，存储在用户客户端中，cookie在访问需要确认身份的地址时，用作通行证进行访问。

cookie本质就是一小段文本信息，浏览器一般对cookie数量和大小有限制，最好个数小于20～30个，大小小于4k。

## 添加

cookie默认关闭浏览器过期，过期的cookie不再起作用。

* max_age：设置多少秒后过期（优先）。
* expires：设置具体年月日时分秒过期（格林尼治时间，即北京时间+8小时）。

```python
from datetime import timedelta

@app.get("/set_cookie/")
def set_cookie_view():
  	# 创建响应对象
    resp = make_response("设置cookie")
    # 计算过期时间
    expire_time = datetime.now() + timedelta(days=2)
    # 设置cookie
    resp.set_cookie("key", "value", expires=expire_time)
    # 返回响应
    return resp
```

## 读取

```python
# 通过request对象可获取已设置好的cookie
request.cookies.get("key")
```

## 删除

```python
def delete_cookie_view():
  	# 创建响应对象
    resp = make_response("删除cookie")
    # 删除cookie
    resp.delete_cookie("key")
    # 返回响应
    return resp
```

# session

session由服务端产生并以某种形式（mysql、redis等）保存在服务端，客户端再次访问时，只需要在服务端从session中核对用户信息即可。

session是为了解决cookie存储数据不安全的问题，但是会占用服务器资源。

session跟踪机制与cookie有关，需要与cookie一起使用，但是此时的cookie不会携带用户信息，而是存储SessionID，用于核对身份（也可以使用其他方式，只要可以携带SessionID即可）。

![session机制](image-20231220204209131.png)

## 添加

在使用session之前，需要配置session的盐（加密方式），如果secret发生改变，那么之前设置的所有session都不再生效。

```python
from flask import session
from datetime import timedelta


# 配置session的盐
app.secret_key = "your-secret-key"
# 配置session持久化时间，默认31天
app.permanent_session_lifetime = timedelta(days=7)


@app.get("/set_session/")
def set_session_view():
 		# 设置持久化
    session.permanent = True
  	# 设置session
    session["key"] = "value"
    return "设置session成功"
```

## 读取

```python
session.get("key")
```

## 删除

```python
# 删除指定session信息
session.pop("key")

# 清空session信息
session.clear()
```

# token

Flask中默认不包含token认证。

token令牌是认证身份的另外一种方式，其中JWT（JSON Web Token）是一种独立的认证和授权标准，由服务端颁发给客户端，客户端访问需要认证身份的地址时，只需要在请求头中添加`Authorization: Bearer <token>`即可。

token由三部分组成，并用`.`连接：

1. **Header（头部）**：Header 部分包含令牌的类型（例如 JWT）和使用的签名算法（如 HMAC SHA256 或 RSA SHA256）。它通常是一个 JSON 对象，使用 Base64Url 编码表示。
2. **Payload（负载）**：Payload 部分包含令牌中实际传递的数据。这些数据可以是用户身份信息、权限、到期时间等。Payload 同样是一个 JSON 对象，使用 Base64Url 编码表示。
3. **Signature（签名）**：Signature 部分是令牌的安全性关键。它通过对 Header 和 Payload 部分进行签名而来，以确保令牌在传输过程中未被篡改。签名算法通常使用私钥进行加密，以确保只有持有相应公钥的接收方可以验证签名。

jwt无需在服务端存储相关数据，减轻了服务器压力。

## 安装

Flask中可以使用[Flask-JWT-Extended](https://flask-jwt-extended.readthedocs.io/en/stable/)进行 token认证。

```bash
pip3 install Flask-JWT-Extended
```

## 配置

```python
from flask_jwt_extended import JWTManager, jwt_required, create_access_token
from datetime import timedelta

# 配置jwt盐
app.config["JWT_SECRET_KEY"] = "your-secret-key"
# 配置访问token过期时间
app.config["JWT_ACCESS_TOKEN_EXPIRES"] = timedelta(days=7)
# 配置刷新token过期时间
app.config["JWT_REFRESH_TOKEN_EXPIRES"] = timedelta(days=30)

# jwt初始化并与app关联
jwt = JWTManager(app)
```

> 访问token过期后，客户端可以拿着刷新token获取新的访问token。

## 添加

```python
# 产生一个与uid关联的访问token
access_token = create_access_token(identity=uid)   
# 产生一个与uid关联的刷新token
refresh_token = create_refresh_token(identity=uid)
```

> 将产生的token返回给客户端即可。

## 验证

访问token验证：

```python
@app.route("/access", methods=["GET"])
@jwt_required()
def access_view():
    return "需要访问权限"
```

> 在需要验证身份的视图函数中添加`@jwt_required`即可校验请求头中是否携带token。

## 获取

获取请求中的用户标识（例如用户ID）

```python
current_user = get_jwt_identity()
```

## 常用方法

### create_access_token

> 创建 JWT 访问令牌的函数。

1. `identity`（身份）：必需。表示用户身份的任意对象。这通常是用户的 ID、电子邮件地址或用户名。此值将包含在 JWT 令牌中，并可以在后续请求中使用，以便识别和验证用户身份。
2. `fresh`（新鲜）：可选。布尔值，默认为 `False`。设置为 `True` 时，表示令牌是新鲜的（即未过期的）。如果令牌是新鲜的，将在 JWT 令牌中添加一个 `fresh` 声明，其值为 `True`。
3. `expires_delta`（过期时间差）：可选。表示 JWT 令牌的过期时间。可以是一个 `datetime.timedelta` 对象或一个可以转换为 `datetime.timedelta` 的字符串（例如，`"1 day"` 或 `"2 hours"`）。如果未指定过期时间，将使用应用程序配置中的默认过期时间。
4. `additional_claims`（附加声明）：可选。表示要添加到 JWT 令牌中的附加声明。这可以是一个字典，其中键是声明名称，值是声明的值。
5. `additional_headers`（附加头部）：可选。表示要添加到 JWT 令牌中的附加头部。这可以是一个字典，其中键是头部名称，值是头部的值。

### create_refresh_token

> 创建 JWT 刷新令牌的函数。

1. `identity`（身份）：必需。表示用户身份的任意对象。这通常是用户的 ID、电子邮件地址或用户名。此值将包含在 JWT 令牌中，并可以在后续请求中使用，以便识别和验证用户身份。
2. `expires_delta`（过期时间差）：可选。表示 JWT 令牌的过期时间。可以是一个 `datetime.timedelta` 对象或一个可以转换为 `datetime.timedelta` 的字符串（例如，`"1 day"` 或 `"2 hours"`）。如果未指定过期时间，将使用应用程序配置中的默认过期时间。
3. `additional_claims`（附加声明）：可选。表示要添加到 JWT 令牌中的附加声明。这可以是一个字典，其中键是声明名称，值是声明的值。
4. `additional_headers`（附加头部）：可选。表示要添加到 JWT 令牌中的附加头部。这可以是一个字典，其中键是头部名称，值是头部的值。

### jwt_required

> 装饰器，用于保护需要 JWT 令牌的路由。

1. `optional`（可选）：布尔值，默认为 `False`。设置为 `True` 时，表示 JWT 令牌是可选的，即无需提供令牌即可访问路由。如果未提供令牌，`current_user` 将为 `None`。
2. `fresh`（新鲜）：布尔值，默认为 `False`。设置为 `True` 时，表示要求 JWT 令牌是新鲜的（即未过期的）。如果令牌已过期，将引发 `FreshTokenRequired` 异常。
3. `refresh`（刷新）：布尔值，默认为 `False`。设置为 `True` 时，表示要求 JWT 令牌是可刷新的。如果令牌不可刷新（例如，不是刷新令牌），将引发 `RefreshTokenRequired` 异常。
4. `locations`（位置）：可选的 `LocationType`，默认为 `None`。用于指定从哪些位置检索 JWT 令牌。可以是一个字符串，也可以是一个字符串列表。例如，`["headers", "query_string"]` 表示从 HTTP 请求头和查询字符串中检索令牌。如果未指定位置，将使用默认的位置（请求头和查询字符串）。
5. `verify_type`（验证类型）：布尔值，默认为 `True`。设置为 `True` 时，表示要验证令牌的类型（例如，确保令牌是访问令牌而不是刷新令牌）。
6. `skip_revocation_check`（跳过撤销检查）：布尔值，默认为 `False`。设置为 `True` 时，表示跳过撤销检查。如果令牌已被撤销，将引发 `TokenRevoked` 异常。

### get_jwt_identity

获取当前请求中的用户标识。如果请求中没有有效的 JWT 令牌，此函数将返回 `None`。

### get_current_user

>用于获取当前登录的用户。

需要先定义一个回调函数，用于返回查找的用户。

```python
@jwt.user_lookup_loader
def user_loader_callback(jwt_header, jwt_payload):
    user_id = jwt_payload["sub"]
    # 在这里，你需要根据 user_id 从数据库或其他数据源中查找用户
    user = User(user_id, ...)
    return user
```

### set_access_cookies

> 用于将 JWT 访问令牌存储在 HTTP 响应的 Cookie 中的函数。

1. `response`（响应）：必需。表示要设置 Cookie 的 HTTP 响应对象。
2. `encoded_access_token`（编码的访问令牌）：必需。表示已编码的 JWT 访问令牌。
3. `max_age`（最大年龄）：可选。表示访问令牌 Cookie 的最大生存时间（以秒为单位）。如果未指定 `max_age`，则使用应用程序配置中的默认值。
4. `domain`（域）：可选。表示访问令牌 Cookie 的域。如果未指定 `domain`，则使用应用程序配置中的默认值。

### set_refresh_cookies

> 用于将 JWT 刷新令牌存储在 HTTP 响应的 Cookie 中的函数。

1. `response`（响应）：必需。表示要设置 Cookie 的 HTTP 响应对象。
2. `encoded_refresh_token`（编码的刷新令牌）：必需。表示已编码的 JWT 刷新令牌。
3. `max_age`（最大年龄）：可选。表示刷新令牌 Cookie 的最大生存时间（以秒为单位）。如果未指定 `max_age`，则使用应用程序配置中的默认值。
4. `domain`（域）：可选。表示刷新令牌 Cookie 的域。如果未指定 `domain`，则使用应用程序配置中的默认值。

### unset_access_cookies

> 用于删除存储在 HTTP 响应的 Cookie 中 JWT 访问令牌的函数。

1. `response`：类型为 `Response` 的参数，表示要修改的 HTTP 响应。
2. `domain`：类型为 `Optional[str]` 的可选参数，表示 Cookie 的域。默认值为 `None`，表示在当前域上设置 Cookie。

### unset_refresh_cookies

> 用于删除存储在 HTTP 响应的 Cookie 中 JWT 刷新令牌的函数。

1. `response`：类型为 `Response` 的参数，表示要修改的 HTTP 响应。
2. `domain`：类型为 `Optional[str]` 的可选参数，表示 Cookie 的域。默认值为 `None`，表示在当前域上设置 Cookie。

### decode_token

> 用于解码 JWT 令牌的函数。

1. `encoded_token`（编码的令牌）：必需。表示要解码的 JWT 令牌。
2. `csrf_value`（CSRF 值）：可选。表示与令牌关联的 CSRF 值。如果提供了 CSRF 值，`decode_token` 函数将验证令牌中的 CSRF 值是否与提供的 CSRF 值匹配。如果未提供 CSRF 值，`decode_token` 函数将不会验证令牌中的 CSRF 值。
3. `allow_expired`（允许过期）：可选。表示是否允许解码已过期的令牌。如果 `allow_expired` 为 `True`，则 `decode_token` 函数将解码已过期的令牌。如果 `allow_expired` 为 `False`（默认值），则 `decode_token` 函数将拒绝解码已过期的令牌。

