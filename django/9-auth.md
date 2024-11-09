---
title: 9.auth系统
category: Django框架
tags:
  - framework
  - django
  - auth
date: 2023-1-9 00:00:00
---



## admin

详情参见[Django 管理站点](https://docs.djangoproject.com/zh-hans/4.2/ref/contrib/admin/)。

django自带的admin后台系统，用于管理应用，一般给开发人员使用。

路由如下：

```http
http://127.0.0.1/admin/
```

创建超级管理员如下：

```bash
python3 manage.py createsuperuser
```

> 存储在`auth_user`表中。

注册自定义模型表如下：

```python admin.py
from django.contrib import admin

class UserAdmin(admin.ModelAdmin):
  
  # 添加的字段及顺序
  fields = ["name", "age", "sex"]
  # 添加的字段分组
  fieldsets = [
    ("第一列", {"fields": ["name", ...]}),
    ...
  ]
  
  # 展示的字段
  list_display = ["name", "age"]
	# 过滤的字段
  list_filter = ["id"]
  # 可搜索字段
  search_fields = ["name"]
  # 分页
  list_per_page = 3
  
admin.site.register(UserModel, UserAdmin)
```

> 注册文件一般是admin.py。



## auth表

`auth_user`：用户表。

`auth_group`：用户组表。

`auth_permission`：权限表。

`auth_user_group`：用户，用户组关联表。

`auth_group_permissions`：用户组，权限关联表。

`auth_user_user_permissions`：用户，用户权限关联表。

> 表模型类在：`django.contrib.auth.models`。



## auth认证

详情参见[使用 Django auth认证](https://docs.djangoproject.com/zh-hans/4.2/topics/auth/default/#user-objects)。

常用方法如下：

1. create_user：创建用户。
2. authenticate：验证登录。
3. login：保存登录状态。
4. logout：退出登录。
5. set_password：修改密码

常用实例属性如下：

1. is_authenticated：判断是否登录。
2. login_required：判断用户是否登录的装饰器，可在settings.py配置`LOGIN_URL`来设置引导未登录路由。



## 权限认证

详情参见[使用 Django 限制对未登录用户的访问 ](https://docs.djangoproject.com/zh-hans/4.2/topics/auth/default/#limiting-access-to-logged-in-users)。

权限校验请查看`permission_required` 装饰器，也可使用`method_decorator`装饰器包裹使用在类视图方法 。

添加权限请在关联表中添加数据。

