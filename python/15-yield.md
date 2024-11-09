---
title: 15.迭代器和生成器
date: 2022-10-15 00:00:00
category: Python语言
tags:
  - 生成器
  - 迭代器
  - yield
  - iterator
  - next
---

# 迭代器

迭代器的本质是一个类，该类实现了`__next__`方法，从而可以在调用`next()`函数时返回下一个数据，而在官方文档中要求实现该方法时，应该尽可能的是一个可迭代对象，数据集使用`iter()`函数可以返回一个可迭代对象，而迭代器实现`__iter__`方法即可返回一个特殊的可迭代对像，因此，一般一个迭代器需要实现`__iter__`和`__next__`两个方法。

```python
class MyIter:

    def __init__(self, n):
        self.n = n

    # 返回可迭代对象的魔法方法
    def __iter__(self):
        return self

    # 迭代器的魔法方法
    def __next__(self):
        # 可抛出异常来防止出现无限循环的情况
        if self.n > 20:
            raise StopIteration

        current = self.n
        self.n += 1
        return current


# 获取迭代器对象
myiter = MyIter(10)

print(next(myiter))
print(next(myiter))
print(next(myiter))
print(next(myiter))

for i in myiter:
    print(i)
```

# 生成器

生成器本质是一个使用了`yield`关键字的函数，它可以在迭代过程中逐步产生多个值，而不是一次性全部给出，可以理解为是一个特殊的迭代器。

```python
import sys


# 使用生成器实现斐波那契数列
def fibonacci(n):
    a, b, count = 0, 1, 0
    while True:
        if count > n:
            return
        yield a
        a, b = b, a+b
        count += 1


f = fibonacci(20)

while True:
    try:
        print(next(f), end='\n')
    except StopIteration:
        sys.exit()
```

> 生成器函数的优势是它们可以按需生成值，避免一次性生成大量数据并占用大量内存。