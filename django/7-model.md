---
title: 7.模型
category: Django框架
tags:
  - framework
  - django
  - model
  - field
  - database
date: 2023-1-07 00:00:00
---



Django的ORM（Object Relational Mapping）系统，用于定义模型类，来映射对应数据库表的字段名、字段类型、字段约束、字段主键外键等等，不必编写SQL语句，只需要操作对象即可进行数据库操作。

参见[模型 | Django 文档 | Django (djangoproject.com)](https://docs.djangoproject.com/zh-hans/4.1/topics/db/models/)。



## 定义模型

请先[配置数据库](https://www.knight-blog.cn/django/5a8fc734/#%E6%95%B0%E6%8D%AE%E5%BA%93%E9%85%8D%E7%BD%AE)。

模型类必须编写在APP内的models.py文件。

```python models.py
from django.db import models
from django.utils.translation import gettext_lazy as _


# 用户表
class User(models.Model):
  	# 性别枚举
    class SexChoices(models.IntegerChoices):
        FEMALE = 0, _('female')
        MALE = 1, _('male')
        __empty__ = _('(Unknown)')

    # id，默认自动配置，可省略
    user_id = models.AutoField(primary_key=True)
    # 密码
    password = models.CharField(verbose_name="密码", max_length=16)
    # 昵称
    nickname = models.CharField(verbose_name="昵称", max_length=16)
    # 生日
    birth_day = models.DateField(verbose_name="生日", blank=True, null=True)
    # 性别
    gender = models.IntegerField(verbose_name="性别", choices=SexChoices.choices, blank=True, null=True)
    # 手机号
    phone_number = models.CharField(verbose_name="手机号", max_length=16, unique=True, blank=True, null=True)
    # 邮箱
    email = models.CharField(verbose_name="邮箱", max_length=256, unique=True)
    # 注册时间
    create_time = models.DateTimeField(verbose_name="注册时间", auto_now_add=True)
    # 更新时间
    update_time = models.DateTimeField(verbose_name="更新时间", auto_now=True)
    # 逻辑删除
    is_delete = models.BooleanField(verbose_name="逻辑删除", default=False)

    # 重写删除方法，实现逻辑删除
    def delete(self, using=None, keep_parents=False):
        self.is_delete = True
        self.save()
		
    # 定义元信息
    class Meta:
        # 定义表名
        db_table = 'user'
        # 定义排序
        ordering = ['user_id']
```

> 模型类需要继承`models.Model`，否则无法识别。

**注意**：定义完模型类后，一定要执行迁移与映射指令，否者不会生效。

修改表操作如下：

1. 删除APP下migrations包下的所有映射文件。
2. 数据库下django_migrations表里，删除对应APP数据。
3. 重新映射提交。



## 官方文档

1. 字段类型参见[模型字段参考 | 字段类型](https://docs.djangoproject.com/zh-hans/4.1/ref/models/fields/#field-types)。

2. 字段选项参见[模型字段参考 | 字段选项](https://docs.djangoproject.com/zh-hans/4.1/ref/models/fields/#field-options)。

3. 关系字段参见[模型字段参考 | 关系字段](https://docs.djangoproject.com/zh-hans/4.1/ref/models/fields/#module-django.db.models.fields.related)。

4. 模型实例方法参见[模型实例参考 | 实例方法](https://docs.djangoproject.com/zh-hans/4.1/ref/models/instances/#model-instance-methods)。

5. 模型元信息参见[模型 Meta 选项](https://docs.djangoproject.com/zh-hans/4.1/ref/models/options/)。



## 数据操作

数据的操作是在视图中进行的。

### 添加数据

```python views.py
# 方法一
user = User(name="knight", age=23, ...)
user.save()

# 方法二
User.objects.create(name="knight", age=23, ...)
# 或
User.objects.get_or_create(name="knight", age=23, ...)

# 关联添加数据
# 正向关联
关联表对象.关联字段名.add(被关联表对象)
# 反向关联
被关联表对象.关联表名小写_set.add(关联表对象)

# 正向创建并关联
关联表对象.关联字段名.create(被关联表属性)
# 反向创建并关联
被关联表对象.关联表名小写_set.create(关联表属性)
```

> 正向：定义关联字段的表到被关联表。
>
> 反向：被关联表到定义关联字段的表。
>
> 无论是正向还是反向，目的都是先获取关联属性，然后操作关联对象；正向的关联属性是==关联字段==，反向的关联属性是==关联表名小写_set==。一对一时，不需要`_set`。
>
> 此处的表名是指模型类小写。
>
> 可在关联字段添加`related_name`指定反向关联属性名，而不再是`关联表名小写_set`。

### 查询数据

详情参见[QuerySet API](https://docs.djangoproject.com/zh-hans/4.2/ref/models/querysets/)。

1. 返回查询集方法

```python views.py
# 查询所有数据
res = User.objects.all()

# 过滤查询
res = User.objects.filter(查询条件)

# 唯一查询
res = User.objects.get(id=1)

# 查询第一条
res = User.objects.first()

# 查询最后一条
res = User.objects.last()

# 排除查询
res = User.objects.exclude(排除条件)

# 排序查询
res = User.objects.order_by("age", "-name")

# 查询集转列表查询
res = User.objects.all().values()

# 查询总数
res = User.objects.all().count()

# 关联查询
res = 表对象.关联属性.查询方法|属性
```

> 查询出来的结果是一个是查询集对象，需要进行序列化才能返回。

2. Field查找

```python views.py
# 匹配查询
res = User.objects.filter(name="knight")

# 开头查询
res = User.objects.filter(name__startswith="k")

# 包含查询
res = User.objects.filter(name__contains="i")

# 结尾查询
res = User.objects.filter(name__endswith="t")

# 成员查询
res = User.objects.filter(age__in=[18, 20])

# 范围查询，lt：小于，gt：大于，lte：小于等于，gte：大于等于
res = User.object.filter(age__lt=20)

# 区间查询，闭区间都包含
res = User.objects.filter(age__range=(18,20))

# 多表查询，查询的是表数据，可以连续关联
res = 表.objects.filter(关联属性__关联表属性=条件)
```



### 修改数据

```python views.py
# 方法一
res = User.objects.get(id=1)
res.name = "马添添"
res.save()

# 方法二
User.objects.filter(name="马添添").update(age=22)

# 关联修改
res = 表对象.关联属性.关联表属性|方法
```

> update方法不能用于get查询。

### 删除数据

```python views.py
User.objects.get(id=1).delete()
# 或
User.objects.filter(name="knight").delete()
# 或
User.objects.all().delete()

# 关联删除
res = 表对象.关联属性.remove(关联表对象)
```



## Django表单

详情参见[使用表单 | Django 文档](https://docs.djangoproject.com/zh-hans/4.2/topics/forms/)。

