---
title: 5.请求
date: 2023-12-18 12:47:29
categories: Flask框架
tags: 
  - form
  - file
  - request
---

# Form表单

Flask使用POST方式收集表单数据。

```python
@app.route('/login', methods=["GET", "POST"])
def login_view():
    if request.method == "GET":
        return "login.html"
    if request.method == "POST":
        # 方式一
        uname = request.form.get("uname")
        pwd = request.form.get("pwd")
        # 方式二
        uname = request.values.get("uname")
        pwd = request.values.get("pwd")

        return f"用户名：{uname}，密码：{pwd}"
```

# 文件上传

使用Form表单进行文件的上传，需要添加请求头`Content-Type:multipart/form-data;`才能上传文件。

```python
@app.post('/upload')
def upload_view():
    f = request.files.get('pic')
    f.save(f"./imgs/{f.filename}")

    return "OK"
```

# request对象

request对象中常用的属性及方法如下：

| 属性        | 说明                                      |
| ----------- | ----------------------------------------- |
| values      | 请求的所有数据（包括query参数和form数据） |
| form        | 请求的form数据                            |
| args        | 请求的query数据                           |
| cookies     | 请求的cookie数据                          |
| headers     | 请求的报头数据                            |
| method      | 请求的HTTP方法                            |
| remote_addr | 请求客户端的IP地址                        |
| user_agent  | 请求客户端的User-Agent信息                |
| files       | 请求的上传文件                            |
| path        | 请求的URL路径                             |
| url         | 请求的完整URL地址                         |
| is_secure() | 检查请求是否通过HTTPS请求                 |
| is_xhr()    | 检查请求是否是AJAX请求                    |
| get_json()  | 请求的JSON数据                            |
| get_data()  | 请求的原始数据                            |
