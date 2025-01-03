---
title: 12.钩子函数
date: 2023-12-22 15:17:42
categories: Flask框架
tags:
  - hook
---

# 作用

钩子函数可以在处理业务逻辑的前后追加额外的功能逻辑。

在 Flask 中，钩子函数是一种特殊的函数，它们允许在应用程序的生命周期中的特定时刻执行自定义代码。

钩子函数通常用于在请求处理过程中的不同阶段执行代码，例如在请求开始时、在视图函数之前或之后、在请求结束时等。

可以使用其他钩子函数装饰器注册相应的钩子函数。这些钩子函数允许我们在不同的阶段执行自定义代码，从而实现更高级的功能，例如中间件处理、数据预处理、数据清理等。

# 常用钩子

Flask 提供了以下钩子函数（有些钩子函数在3.0版本已被废弃）：

1. **`before_request`**: 在每个请求处理开始时调用。您可以使用此钩子函数来执行一些全局的操作，例如设置全局变量、检查用户身份等。
2. `before_first_request`: 在第一个请求处理开始时调用。您可以使用此钩子函数来执行一些只需要在应用启动时执行一次的操作，例如初始化数据库、加载配置文件等。
3. **`after_request`**: 在每个请求处理完成后调用，但在响应被发送到客户端之前。您可以使用此钩子函数来修改响应内容，例如添加自定义头部、压缩响应体等。
4. **`teardown_request`**: 在每个请求处理完成后调用，即使在异常处理中。您可以使用此钩子函数来执行一些清理操作，例如关闭数据库连接、释放资源等。
5. `teardown_appcontext`: 在应用上下文销毁时调用。您可以使用此钩子函数来执行一些全局的清理操作，例如关闭数据库连接、释放资源等。
6. `url_value_preprocessor`: 在视图函数之前调用，用于修改 URL 值。您可以使用此钩子函数来根据 URL 值执行一些自定义操作，例如解析 URL 参数、验证用户身份等。
7. `url_defaults`: 在视图函数之前调用，用于设置 URL 默认值。您可以使用此钩子函数来为 URL 设置默认值，例如默认的页码、排序方式等。
8. **`errorhandler`**: 在发生异常时调用，用于处理错误（可以与`abort(status_code)`配合使用）。您可以使用此钩子函数来处理自定义异常、渲染错误页面等。
9. `template_render`: 在模板渲染之前调用，用于修改模板渲染上下文。您可以使用此钩子函数来修改模板渲染上下文，例如添加自定义变量、修改模板继承等。
10. **`context_processor`**: 在模板渲染之前调用，用于向模板传递全局变量（字典类型）。您可以使用此钩子函数来向所有模板传递全局变量，例如当前用户、站点名称等。
11. `template_finalize`: 在模板渲染之后调用，用于修改渲染结果。您可以使用此钩子函数来修改渲染结果，例如压缩 HTML、添加水印等。
12. `template_filter`: 向模板注册自定义过滤器。您可以使用此钩子函数来向模板注册自定义过滤器，例如实现字符串格式化、数据转换等功能。
13. `request_started`: 在请求处理开始时调用，用于处理请求开始事件。您可以使用此钩子函数来执行一些与请求开始相关的操作，例如记录请求日志、设置性能监控等。
14. `request_finished`: 在请求处理完成后调用，用于处理请求完成事件。您可以使用此钩子函数来执行一些与请求完成相关的操作，例如记录请求日志、清理性能监控等。
15. `request_tearing_down`: 在请求处理完成后调用，用于处理请求销毁事件。您可以使用此钩子函数来执行一些与请求销毁相关的操作，例如关闭数据库连接、释放资源等。
16. **`appcontext_tearing_down`**: 在应用上下文销毁时调用，用于处理应用上下文销毁事件。您可以使用此钩子函数来执行一些与应用上下文销毁相关的操作，例如关闭数据库连接、释放资源等。

# 使用

如`before_request`：

```python
from flask import Flask

app = Flask(__name__)


@app.before_request
def before_request():
    print('处理请求之前')


@app.get('/')
def index():
    print("处理中的请求")
    return 'hello world'


if __name__ == '__main__':
    app.run(debug=True)

```

