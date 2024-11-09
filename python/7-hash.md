---
title: 7.散列类型
date: 2022-10-07 00:00:00
category: Python语言
tags:
  - 散列
  - 字典
  - 集合
  - dict
  - set
---

# 字典(dict)

* 可变
* 可迭代

## 简单用法

```python
# 与新华字典一样，具备一对关键字和值（键值对，key:value）
dict_example = {
    "name":"苦艾酒", "age":22
}

# 输出dict类型
print(type(dict_example))
```

## 常用方法

```python
# 获取属性
字典[key]
# 获取属性值
字典.get(key, 默认值)
# 获取字典所有key的列表
字典.keys()
# 获取字典所有value的列表
字典.values()
# 获取字典所有键值对元祖的列表
字典.items()

# 新增或修改属性
字典[key] = value
# 删除字典
del 字典
# 删除字典属性
del 字典[key]
# 清空字典
字典.clear()
```

## 字典推导式

```python
# 生成10以内的平方
dict_example = {i: i**2 for i in range(10)}

# 条件筛选到5以内的平方
dict_example = {key: value for key, value in dict_example.items() if key < 5}
```

# 集合(set)

* 不可变
* 可迭代

## 简单用法

```python
# 与数学中的集合一样，自带去重效果，空集合使用 set()创建
set_example = {0, 1, 2}

# 输出set类型
print(type(set_example))
```

## 常用方法

```python
# 添加单个元素
集合.add(数据)
# 批量添加序列元素
集合.update(序列)

# 删除元素，不存在则报错
集合.remove(元素)
# 删除元素，不存在不报错
集合.discard(元素)
# 删除随机元素并返回该元素
集合.pop()

# 判断数据是否在集合内
数据 in 集合
# 判断数据是否不在集合内
数据 not in 集合
```

## 集合推导式

```python
# 生成10以内的集合
set_example = {i for i in range(10)}
```