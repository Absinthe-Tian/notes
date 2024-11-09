---
title: 6.模板
category: Django框架
tags:
  - framework
  - django
  - template
  - html
  - render
date: 2023-1-06 00:00:00
---



用于渲染项目页面，在html文件中使用特定的语法，来达到特定的效果。

一般项目中不使用该技术，而是采用全后端分离的技术来实现。

> 模板文件的根目录是`/templates`，没有该文件夹需要自己创建，并在settings.py中进行[配置模板](https://www.knight-blog.cn/django/5a8fc734/#%E6%A8%A1%E6%9D%BF%E9%85%8D%E7%BD%AE)。



## 渲染方式

### 字符串返回

视图如下：

```python views.py
from django.http import HttpResponse

def index_view(request):
    return HttpResponse("<h1>This is user's index</h1>")
```

### loader返回

HTML模板如下：

```html templates/user/index.html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>User index</title>
</head>
<body>
    <h1>This is user's index</h1>
</body>
</html>
```

> 使用模板必须先进行APP注册。

视图如下

```python views.py
from django.template.loader import get_template

def loader_template(request):
    html = get_template('user/index.html')
    return HttpResponse(html.render(context={
      "key":value,
      ...
    }))
```

> context：给模板传递数据，value可以是任意类型，其中函数和方法取返回值。

### render返回

视图如下：

```python views.py
from django.shortcuts import render

def render_template(request):
    return render(request, 'user/index.html', context={
      "key":value,
      ...
    })
```



## 静态文件

请先创建并[配置静态文件](https://www.knight-blog.cn/django/5a8fc734/#%E9%9D%99%E6%80%81%E6%96%87%E4%BB%B6)目录static。

```html
{% raw %}{% load static %}{% endraw %}
...
<img src="{% raw %}{% static 'images/index.png' %}{% endraw %}" alt="图片加载失败">
<link rel="stylesheet" href="{% raw %}{% static 'css/reset.css' %}{% endraw %}">
<script src="{% raw %}{% static 'js/index.js' %}{% endraw %}"></script>
```

> 使用静态文件前，需要在模板文件首行使用`{% raw %}{% load static %}{% endraw %}`加载静态文件。
>
> 引入静态文件使用`{% raw %}{% static '资源路径' %}{% endraw %}`。



## 模板变量

视图通过使用context属性携带的数据渲染模板时，可以在模板中使用这些数据。

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8">
    <title>User index</title>
  </head>
  <body>
    "str": "knight",
    "int": 999,
    "arr": [1, 2, 3],
    "dic": {
    "name": "张三",
    "gender": "男"
    },
    "func": lambda: "this is function",

    <h2>This is user's index</h2>

    <h3>str: {% raw %}{{ str }}{% endraw %}</h3>
    <h3>int: {% raw %}{{ int }}{% endraw %}</h3>
    <h3>arr: {% raw %}{{ arr.0 }}{% endraw %}</h3>
    <h3>dic: {% raw %}{{ dic.name }}{% endraw %}</h3>
    <h3>func: {% raw %}{{ func }}{% endraw %}</h3>
  </body>
</html>
```

> 通过使用`{% raw %}{{ key }}{% endraw %}`的方式获取视图传递过来的模板数据，该key即称为模板变量。
>
> 当模板变量的类型为列表、字典、实例时，可以通过`.`符号获取其指定内部属性。
>
> 模板变量名应和Python变量一样遵守命名规范。



## 过滤器

内置过滤器参见[内置过滤器](https://docs.djangoproject.com/zh-hans/4.1/ref/templates/builtins/#ref-templates-builtins-filters)。

```html
{% raw %}{{ 模板变量|过滤器:参数 }}{% endraw %}
```

> 是否需要传入参数，请依据具体的过滤器。
>
> 默认情况下，模板变量会自动转义，即显示源码，使用`safe`过滤器取消转义，也可使用`autoescape`标签设置off进行统一关闭。

**注意**：除括号两端可以键入空格，`|`两端不要使用空格。



## 标签

内置标签参见[内置模板标签](https://docs.djangoproject.com/zh-hans/4.1/ref/templates/builtins/#built-in-tag-reference)。

```html
{% raw %}{% 开始标签 %}{% endraw %}

{% raw %}{% 结束标签 %}{% endraw %}
```

> 一般由开始标签和结束标签组成，个别标签无结束标签。

**注意**：标签里的空格不能随意使用，需要根据官方文档规范输入。



## 模板继承

详情参见[Django 模板继承](https://docs.djangoproject.com/zh-hans/4.1/ref/templates/language/#template-inheritance)。

用于模板公共部分的复用。

父级模板如下：

```html base.html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>{% raw %}{% block title %}这是父级标题{% endblock %}{% endraw %}</title>
</head>
<body>
<h2>这是父级的头部</h2>

{% raw %}{% block body %}{% endraw %}
    <h3>这是父级的内容</h3>
{% raw %}{% endblock %}{% endraw %}

<h2>这是父级的尾部</h2>
</body>
</html>
```

> 使用`block`标签，给继承的子级模板开放接口。

子级模板继承父级模板内容如下

```html child.html
{% raw %}{% extends 'user/base.html' %}{% endraw %}

{% raw %}{% block title %}{% endraw %}
这是子级的标题
{% raw %}{% endblock %}{% endraw %}

{% raw %}{% block body %}{% endraw %}
<h3>这是子级的内容</h3>
{% raw %}{{ block.super }}{% endraw %}
{% raw %}{% include 'user/other.html' %}{% endraw %}
{% raw %}{% endblock %}{% endraw %}

未在父级定义的block中，不会渲染。
```

> 1. 使用`extends`标签继承父级模板，且必须是第一个标签。
>
> 2. 继承父级的模板后，拥有父级所有的内容，但是仅能修改使用`block`标签包裹的内容，修改时，需要指定blockName。
>
> 3. 使用父级的内容时，可以使用`block.super`模板变量引入父级的内容。
> 4. 子级模板继承父级模板后，子级内的所有内容都必须出现在父级模板定义好的block中，否则将不会渲染。
> 5. 使用`include`标签，引入其他模板。



## 自定义标签及过滤器

详情参见[自定义的模板标签和过滤器](https://docs.djangoproject.com/zh-hans/4.2/howto/custom-template-tags/)。

创建`common/templatetags`文件夹，并将common注册进APP。

### 自定义标签

1. 简单标签

详情参见[自定义简单标签](https://docs.djangoproject.com/zh-hans/4.2/howto/custom-template-tags/#simple-tags)。

自定义标签如下

```python common/templatetags/my_tags.py
from django import template

# 获取注册对象
register = template.Library()

# 注册简单标签，携带一个参数。
@register.simple_tag(name="my_time")
def get_time_tag(format_string):
  return datetime.datetime.now().strftime(format_string)

# 注册简单标签，获取上下文数据（模板变量）。
@register.simple_tag(takes_context=True)
def get_time_from_context(context):
  return datetime.datetime.now().strftime(context.get("time_format"))
```

> 上下文数据来源视图函数通过`context`参数传递到模板变量的数据。

使用简单标签如下：

```html
{% raw %}{% load my_tags %}{% endraw %}
...
{% raw %}{% my_time "%Y-%m-%d %H:%M:%S" as time %}{% endraw %}
当前时间：{% raw %}{{ time }}{% endraw %}
<br>
北京时间：{% raw %}{% get_time_from_context %}{% endraw %}
```

> 可以使用`as`关键字引入到模板变量中。
>
> 标签参数在标签名后由空格隔开。

2. 包含标签

详情参见[自定义包含标签](https://docs.djangoproject.com/zh-hans/4.2/howto/custom-template-tags/#inclusion-tags)。

包含标签的内容是一个模板文件。

内容模板如下：

```html include_tag.html
{% raw %}{% for i in values %}{% endraw %}
	<li>{% raw %}{{ i }}{% endraw %}</li>
{% raw %}{% endfor %}{% endraw %}
```

自定义包含标签如下：

```python common/templatetags/my_tags.py
from django import template

# 获取注册对象
register = template.Library()

# 注册一个包含标签，携带不定长参数。
@register.inclusion_tag(filename="user/include_tag.html")
def my_for_tag(*args):
    return {"values": args}
```

使用自定义包含标签如下：

```html
{% raw %}{% load my_tags %}{% endraw %}
...
{% raw %}{% my_for_tag 1 2 3 4 "a" "b" "c" %}{% endraw %}
```

### 自定义过滤器

自定义过滤器如下

```python common/templatetags/my_filter.py
from django import template

# 获取注册对象
register = template.Library()


# 注册过滤器，不带参。
@register.filter("my_square")
def square_filter(value):
  return value * value

# 注册过滤器，携带参。
@register.filter("my_multi")
def multi_filter(value, args):
    return value * args
```

> value接收过滤`|`左边的值，args接收过滤器`:`右边的值。
>
> 不指定过滤器名时，默认使用函数名作为过滤器名。

使用自定义过滤器如下

```html
{% raw %}{% load my_filter %}{% endraw %}
...
<h3>my_square: {% raw %}{{ 1024|my_square }}{% endraw %}</h3>
<h3>int: {% raw %}{{ 1024|my_multi:2 }}{% endraw %}</h3>
```

> 自定义的过滤器在使用前，需要使用`load`标签加载自定义过滤器所在的py文件。

