---
title: 3.环境参数
date: 2023-12-17 16:21:23
categories: Flask框架
tags: 
  - env
  - config
---

# 使用方式

flask允许用户配置环境参数来使用。

[Flask Documentation (3.0.x) flask.Config](https://flask.palletsprojects.com/en/latest/api/#flask.Config)。

```python
# 方式一
app.config['DEBUG'] = True
# 方式二
app.config.update({'DEBUG':True})
# 方式三
app.config.from_mapping({'DEBUG':True})
# 方式四
class Config:
  DEBUG = True

app.config.from_object(Config)
# 方式五
{"DEBUG":"True"}

app.config.from_file('config.json', load=json.load)
# 方式六
DEBUG = True

app.config.from_pyfile('setting.py')
# 方式七
app.config.from_envvar('envName')
```

# 常用配置

1. `DEBUG`：设置为 `True` 时，启用调试模式。在调试模式下，Flask 会显示详细的错误信息，并在代码发生变化时自动重新加载应用程序。默认值为 `False`。
2. `TESTING`：设置为 `True` 时，启用测试模式。在测试模式下，Flask 会禁用一些错误处理和日志记录功能，以便在测试环境中运行。默认值为 `False`。
3. `SECRET_KEY`：用于加密和解密会话数据的密钥。这个密钥应该是随机生成的，并且不应该在多个应用程序之间共享。默认值为 `None`，但在生产环境中运行时，你需要设置一个安全的密钥。
4. `JSON_AS_ASCII`：设置为 `True` 时，JSON 数据将以 ASCII 格式编码。默认值为 `True`。
5. `JSON_SORT_KEYS`：设置为 `True` 时，JSON 数据的键将按字母顺序排序。默认值为 `False`。
6. `JSONIFY_PRETTYPRINT_REGULAR`：设置为 `True` 时，JSON 数据将以易读格式输出。默认值为 `False`。
7. `JSONIFY_MIMETYPE`：JSON 数据的 MIME 类型。默认值为 `application/json`。

# env

在flask项目中，可以使用`python-dotenv`在`.env`文件中指定一些环境变量。

安装：

```bash
pip3 install python-dotenv
```

使用：

```ini
FLASK_APP=run
FLASK_DEBUG=true
FLASK_ENV=development
FLASK_RUN_HOST=0.0.0.0
FLASK_RUN_PORT=8000
```

