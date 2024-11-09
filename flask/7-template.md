---
title: 7.Jinja模板
date: 2023-12-18 17:18:51
categories: Flask框架
tags: 
  - template
  - url_for
  - filter
  - macro
  - extend
  - block
  - static
  - import
---

使用Jinja模板可以避免响应内容臃肿等问题。

所谓的模板就是一个HTML文件，并且模板的默认存放位置位于*templates*目录作为根目录，可以在`Flask(template_folder='目录名称')`进行修改。

# 模板传参

视图函数传参：

```python
from flask import render_template

@app.route('/')
def index_view():
    context = {
      "framework": "Flask",
      "person": {
        "name": "p1",
        "age": 20,
        "gender": "female"
      }
    }
    return render_template("template", **context)
```

模版文件接收：

```html
<!--  直接获取  -->
{% raw %}{{ framework }}{% endraw %}
<!--  获取子属性  -->
{% raw %}{{ person.name }}
{{ person["age"] }}
{{ person.get("gender") }}{% endraw %}
```

# url_for

模板中也是可以使用`url_for`函数的，与视图函数中的使用方法一样。

```html
{% raw %}href="{{ url_for('login_view') }}"{% endraw %}
```

# 过滤器

将传递的数据按指定格式化输出。

```html
{% raw %}{{ data | filter }}{% endraw %}
```

## 内置过滤器

[Template Designer Documentation — Jinja Documentation (3.1.x)](https://jinja.palletsprojects.com/en/3.1.x/templates/#builtin-filters)

| 过滤器                                     | 说明                                                     |
| ------------------------------------------ | -------------------------------------------------------- |
| int(value)                                 | 返回整型数据                                             |
| abs(value)                                 | 返回一个数值的绝对值                                     |
| float(value)                               | 返回浮点型数据                                           |
| string(value)                              | 返回字符串数据                                           |
| first(value)                               | 返回序列的第一个元素                                     |
| last(value)                                | 返回序列的最后一个元素                                   |
| length(value)                              | 返回序列或字典的长度                                     |
| join(value, d='-')                         | 返回序列以d拼接的字符串                                  |
| lower(value)                               | 字符串转小写                                             |
| upper(value)                               | 字符串转大写                                             |
| replace(value, old, new)                   | 替换字符串的子串                                         |
| truncate(value, length=n, killwords=False) | 截取length长度的字符串                                   |
| striptags(value)                           | 删除字符串中HTML标签，并压缩空格                         |
| trim(value)                                | 去除两侧空白符                                           |
| wordcount(value)                           | 计算一个长字符串中单词的个数                             |
| default(value, data, boolean=False)        | value无数据时，返回data；boolean=True，value为空返回data |
| safe(value)                                | 关闭转义（默认开启转义`{%autoescape true%}`）            |
| escape(value)、e(value)                    | 启用转义（HTML符号）                                     |
| format(value, *args, **kwargs)             | 格式化字符串                                             |

## 自定义过滤器

使用一个装饰器即可自定义过滤器。

```python
@app.template_filter(filterName)
def my_upper(value):
  	# 处理数据
    return value
```

# 流程控制

## 选择控制

```html
{% raw %}{% if num % 2 == 0 %}{% endraw %}
		<p>偶数</p>
{% raw %}{% elif num % 2 == 1 %}{% endraw %}
		<p>奇数</p>
{% raw %}{% else %}{% endraw %}
		<p>非奇非偶</p>
{% raw %}{% endif %}{% endraw %}
```

## 循环控制

```html
{% raw %}{% for item in data ｜ filter %}{% endraw %}
	<p>{% raw %}{{ item }}{% endraw %}</p>
{% raw %}{% endfor %}{% endraw %}
```

> 循环中无break及continue。

循环包含一些变量可使用

| 变量名      | 说明                |
| ----------- | ------------------- |
| loop.index  | 返回从1开始的索引值 |
| loop.index0 | 返回从0开始的索引值 |
| loop.first  | 判断是否是第一个    |
| loop.last   | 判断是否数最后一个  |
| loop.length | 返回迭代器长度      |

# 变量

Jinja模板中也允许定义变量使用。

## set

定义：

```html
{% raw %}{% set name = 'absinthe' %}{% endraw %}
```

使用：

```html
{% raw %}{{ name }}{% endraw %}
```

> 在整个模板文件都可以使用。

## with

```html
{% raw %}{% with name = 'absinthe' %}
	{{ name }}
{% endwith %}{% endraw %}
```

> 该变量只能在with代码段内使用，而不能在其他位置使用。
>
> 可以与with嵌套使用。

# 宏

Jinja模板允许我们将一段模板作为返回值并定义为一个函数在模板中使用。

## 定义宏

```html
{% raw %}{% macro my_h1(content, color='#000') %}{% endraw %}
    <h1 style="color: {% raw %}{{color}}{% endraw %}">{% raw %}{{ content }}{% endraw %}</h1>
{% raw %}{% endmacro %}{% endraw %}
```

> 可专门创建一个文件夹存放HTML宏文件。

## 引入宏

和Python导包一样。

```html
{% raw %}{% from 'macros/my_macro.html' import my_h1 with context %}{% endraw %}
```

> `with context`可以将视图函数传递过来的数据在引入的宏文件中使用。

## 使用宏

```html
{% raw %}{{ my_h1("Hello Flask", color="#ffa500") }}{% endraw %}
```

# 模板引入

如果模板中存在一些相同效果，则可以使用模板之间的引入。

```html
{% raw %}{% include 'common/header.html' %}{% endraw %}
<div>主体内容</div>
{% raw %}{% include 'common/footer.html' %}{% endraw %}
```

> 不需要使用`with context`，被引入的模板也存在context，即被引入的模板也可以使用视图函数传递的数据。

# 模板继承

模板继承可以把一些公共的代码单独提取出来作为父模板，子模板继承父模板后重写父模板的block即可。

父模板：

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>base.html</title>
</head>
<body>
<header>这是头部信息</header>
<main>
{% raw %}{% block content1 %}
    这是父模板的内容一
{% endblock %}

{% block content2 %}
    这是父模板的内容二
{% endblock %}{% endraw %}
</main>
<footer>这是页脚信息</footer>
</body>
</html>
```

子模板：

```html
{% raw %}{% extends 'base.html' %}

{% block content1 %}
这是子模板的内容一
{{super()}}
{{self.content2()}}
{% endblock %}{% endraw %}
```

> `super()`可使用父模板的block。
>
> `self.blockName()`可使用其他block。

# 静态文件

静态文件默认存放在*static*目录作为根目录，可以在`Flask(static_folder='目录名称')`进行修改。

```html
<!--方式一-->
src="/static/filePath"
<!--方式二-->
{% raw %}src="{{ url_for('static', filename='filePath') }}"{% endraw %}
```

