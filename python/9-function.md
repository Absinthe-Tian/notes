---
title: 9.函数
date: 2022-10-09 00:00:00
category: Python语言
tags:
  - function
  - lambda
  - 闭包
  - 递归
---

作用：将一段有独立功能的代码块整合到一起并命名（封装），在需要的位置进行调用这个名称即可实现该部分的功能，从而实现代码的复用。

# 简单使用

```python
该区域的变量为全局变量
# 定义函数
def 函数名(形参):
  """ 函数的说明，可使用help(函数名)进行查看。 """
  代码
  该区域的变量为局部变量，使用global关键字即可声明为全局变量
  ...
  return 返回值
  
# 调用函数
函数名(实参)
```

> 形参与实参统称为参数，用于函数接收数据，在实际开发过程中根据需要给出，其中形参可以是`*args`（元祖不定长）、`**kwargs`（字典不定长），用于接收不确定长度的实参。
>
> 返回值用于将一个结果返回给调用者，以获取某个数据，在实际开发过程中根据需要给出。

使用案例：

```python
# 求平方和
def my_sum(*args):
    result = 0
    for i in args:
        result += i**2
    return result


list_example = [1, 2, 3]
# * 可以直接理解为拆包
result = my_sum(*list_example)
print(result)
```

# 闭包

作用：将一个函数作为返回值的函数，可以用于创建回调函数及二次封装。

```python
def 外层函数(形参):
    代码
    ...
    def 内层函数(形参):
        代码
        可以使用外层函数的变量，使用nonlocal关键字声明为局部变量才能修改。
        ...
    # 外函数的返回值是内函数的引用
    return 内层函数
```

# lambda表达式

作用：简化版函数。

```python
lambda 参数: 表达式
```

> lambda表达式只能返回一个表达式的值，且参数可有可无。

使用案例

```python
func = lambda a: a**2
print((lambda a: a**2)(5))
print(func)
print(func(5))
```

# 递归函数

一种函数的使用技巧

```python
def my_sum(num):
  if num == 1:
    return 1
  # 函数自调用
  result = num + my_sum(num-1)
  return result
```

# 内置函数

更多内置函数参考[Python3 内置函数](https://www.runoob.com/python3/python3-built-in-functions.html)。

## 常用高级函数

### map、reduce、filter函数

1. `map(function, iterable, ...)`：将序列的每一个元素应用一个函数，并返回一个迭代器对象。

```python
nums = [1, 2, 3, 4]
result = map(lambda x: x**2, nums)
print(list(result))
```

2. `reduce(function, iterable)`：将序列的每一个元素通过function(x, y)逐一操作归纳为一个值。

```python
from functools import reduce

nums = [1, 2, 3, 4]
result = reduce(lambda x,y: x+y, nums)
print(result)
```

3. `filter(function, iterable)`：将序列的每一个元素通过函数返回值的True和False，过滤为一个过滤器对象。

```python
nums = [1, 2, 3, 4]
result = filter(lambda x:x<3, nums)
print(list(result))
```

### Counter计数器

用于追踪值的出现次数，Counter类继承dict类，可以使用其方法。

1. 对iterable进行计数

```python
import collections

counter = collections.Counter(['a', 'b', 'c', 'a', 'b', 'a'])
print(counter)
```

2. 追加内容

```python
counter.update(['c', 'b', 'c', 'd'])
print(counter)
```

3. 返回最高频率前n的数据

```python
counter.most_common(n)
```

### defaultdict默认字典

Python的字典中，访问不存在的key会报错，一般需要初始化。defaultdict可以传入value的类型完成初始化。

1. 自动初始化

```python
import collections

dict_int = collections.defaultdict(int)
dict_int['num'] += 10
print(dict_int)
```

2. 继承类型方法

```python
dict_list = collections.defaultdict(list)
dict_list['nums'].append(10)
print(dict_list)
```

### sorted排序

`sorted(iterable, key=None, reverse=False)`可以将序列按照一定的规则进行排序，并返回一个新的序列。

```python
nums = [2, 3, 5, 1, 8, 6]
# 从大到小排序
sorted(nums, lambda x: -x)
```

### enumerate函数

`enumerate(sequence, [start=0])`可以将可迭代对象组合为一个索引字典，即**索引:值**。

```python
fruits = ['apple', 'banana', 'cherry']  
for index, fruit in enumerate(fruits):  
    print(index, fruit)
```

### groupby函数

`groupby(iterable, key=None)`可以将相邻相同元素进行聚合，配合sorted可以完成根据key的相同值进行分组。

```python
from itertools import groupby

students = [
  {'id': 1, 'name': 't1', 'gender': 'male'},
  {'id': 2, 'name': 't2', 'gender': 'male'},
  {'id': 3, 'name': 't3', 'gender': 'female'},
  {'id': 4, 'name': 't4', 'gender': 'male'},
  {'id': 5, 'name': 't5', 'gender': 'female'},
]
students_copy = sorted(students, key=lambda x: x['gender'])
result = groupby(students, lambda x: x['gender'])
for k,v in result:
  print(k, list(v))
```

### zip压缩

`zip([iterable, ...])`可以将多个可迭代对象中相同索引的元素打包为一个个的元祖/字典，并返回一个zip对象，可使用`*`对zip对象进行解压为多个可迭代对象。

```python
a = [1, 2, 3]
b = [4, 5, 6]

c = zip(a, b)
# [(1, 4), (2, 5), (3, 6)]
print(list(c))

c = zip(a, b)
# {1: 4, 2: 5, 3: 6}
print(dict(c))

c = zip(a, b)
d,e = zip(*c)
print(d,e)
```

> 注意：zip对象是特殊的生成器，因此只能使用一次数据。

### ord、chr函数

`ord(char)`函数将一个字符返回其对应的 ASCII 数值，或者 Unicode 数值。

`chr(int)`函数将一个整数返回其对应的字符。

```python
# 97
print(ord('a'))

# A
print(chr(65))
```

