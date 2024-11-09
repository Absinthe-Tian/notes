---
title: 1.Flask简介
date: 2023-12-17 14:32:06
categories: Flask框架
tags: 
  - 简介
  - 框架
  - 扩展列表
  - python
---

[Flask](https://flask.palletsprojects.com/en/latest/)发布于2010年非常流行的Python Web框架。

# MVT

![MVT](image-20231218173449520.png)

M：Model，负责与数据库交付，进行数据处理。

V：View，负责接收请求，进行业务处理，返回响应。

T：Template，负责封装构造页面。

# 特点

* 微框架、简洁、灵活，给开发者提供了很大的扩展性。
* Flask生态做的很好，用起来很舒服。
* 开发效率非常高，如SQLAlchemy的ORM操作数据库可以节省大量编写SQL语句的时间。

FLask相当于一个内核，其他功能都可以使用第三方扩展来实现，如邮件(Flask-Mail)、用户认证(Flask-Login)、数据库(SQLAlchemy)等等。

其WSGI工具箱采用**[Werkzeug](https://werkzeug.palletsprojects.com/)**（路由模块），模版引擎采用**[Jinja](https://jinja.palletsprojects.com/)**，同属于FLask框架的核心。

# 常用扩展

扩展列表：http://flask.pocoo.org/extensions/

* Flask-SQLalchemy：操作数据库。
* Flask-script：插入脚本。
* Flask-migrate：管理迁移数据库。
* Flask-Session：Session存储方式指定。
* Flask-WTF：表单。
* Flask-Mail：邮件。
* Flask-Bable：提供国际化和本地化支持，翻译。
* Flask-Login：认证用户状态。
* Flask-OpenID：认证。
* Flask-RESTful：开发REST API的工具。
* Flask-Bootstrap：集成前端Twitter Bootstrap框架。
* Flask-Moment：本地化日期和时间。
* Flask-Admin：简单而可扩展的管理接口的框架。
