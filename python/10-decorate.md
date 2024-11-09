---
title: 10.装饰器
date: 2022-10-10 00:00:00
category: Python语言
tags:
  - decorate
  - function
  - 装饰器
---

装饰器是Python中的一个语法糖，本质就是一个函数。

# 简单理解

```python
def out_func(func):
    print("out函数开始")
    def inner_func(*args, **kwargs):
        print("inner函数开始")
        result = func(*args, **kwargs)
        print("inner函数结束")
        return result
    print("out函数结束")
    return inner_func


# 装饰器用法
@out_func
def my_func(n):
    print("my_func运行")
    return n + 1


result = my_func(1)
print(result, '\n')


# 普通用法，完全等价
def my_func(n):
    print("my_func运行")
    return n + 1


result = out_func(my_func)(1)
print(result)
```

> 本质是将==被装饰函数作为参数传入到装饰器函数==中，并将==被装饰函数的实参传入到装饰器函数的内部函数==进行调用和返回，其中使用到了Python中的闭包。

# 带参装饰器

```python
def out_func(x):
    print('out函数开始')

    def wrapper(func):
        print('wrapper函数开始')

        def inner_func(*args, **kwargs):
            print("inner函数开始")
            result = func(*args, **kwargs) * x
            print("inner函数结束")
            return result
        print('wrapper函数结束')
        return inner_func
    print('out函数结束')
    return wrapper


# 装饰器用法
@out_func(1000)
def my_func(n):
    print("my_func函数开始")
    print("my_func运行中.....")
    print("my_func函数结束")
    return n + 1


result = my_func(1)
print(result, '\n')


# 普通用法，完全等价
def my_func(n):
    print("my_func函数开始")
    print("my_func运行中.....")
    print("my_func函数结束")
    return n + 1


result = out_func(1000)(my_func)(1)
print(result)
```

> 依次将==装饰器实参==、==被装饰函数==、==被装饰函数的实参==由外到内传入到装饰器，可以理解为闭包套娃。
