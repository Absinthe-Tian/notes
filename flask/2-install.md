---
title: 2.第一个程序
date: 2023-12-17 15:08:18
categories: Flask框架
tags: 
  - install
  - run
---

# 安装

使用pip3安装Flask。

```bash
pip3 install flask
```

# 第一个程序

```python
# 导入flask
from flask import Flask

# 创建Flask对象
app = Flask(__name__)


# 路由装饰器，用于映射路由地址。
@app.route('/')
def index():
    # 返回给前端的数据
    return "<h1>Hello Flask</h1>"


if __name__ == '__main__':
    # 启动应用
    app.run(host="0.0.0.0", port=8000, debug=True)
```

> 访问：http://127.0.0.1:5000/

# 启动方式

## 方式一

运行python文件，运行时让程序执行`app.run()`接口来启动应用。

run方法参数如下：

* *host=None*（默认localhost）
* *port=None*（默认5000）
* *debug=None*（默认False，会启动热加载及报错展示。）
* *load_dotenv=True*
* *options*

## 方式二

使用`flask run`命令，运行时指定app即可。

查看帮助：

```bash
flask run --help
```

启动示例：

```bash
flask --app hello run -h 0.0.0.0 -p 8000  --debugger
```

> `hello`是app所在的文件名。
