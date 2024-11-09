---
title: 1.Django介绍
category: Django框架
tags:
  - framework
  - intro
  - django
  - drf
  - http
date: 2023-1-01 00:00:00
---

## 初识

[ Django](https://www.djangoproject.com/)是基于python语言的web后端框架。

> *Django makes it easier to build better web apps more quickly and with less code.*
>
> Django可以让我们用更少的代码更快速地构建更好的web应用。

这是摘自官网的介绍，可以很直接的说明了这个框架优势。

与传统框架类似，也是MTV（Model Template View）模式。

采用的是[RESTful API 一种流行的 API 设计风格](https://restfulapi.cn/)。

一般与DRF（[Django REST framework ](https://www.django-rest-framework.org/)）拓展插件一起搭配开发。



## 开发工具及环境

1. python3.6
2. Ubuntu18.04
3. MySQL8.0
4. Redis7.0
5. pycharm2021.2



## HTTP请求

1. 请求行：请求方式（GET、POST、PUT、DELETE）、请求资源、协议版本。
2. 请求头：content-type、cookie，参见[HTTP headers - HTTP | MDN (mozilla.org)](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers)。
3. 请求体：发送请求的主体数据。

## HTTP响应

1. 响应行：HTTP协议版本、状态码。
2. 响应头：Set-Cookie、Content-type。
3. 响应体：根据响应类型，服务端返回给客户端的相应正文内容。



## URL

```http
schema://host[:port]/path/.../[?query=value][#anchor]
```

> schema：使用的协议，如http、https等等。
>
> host：主机名IP或域名。
>
> port：服务端口。
>
> path：资源路径。
>
> query：查询参数，用于传递查询参数。
>
> anchor：锚点，用于定位页面内容。
