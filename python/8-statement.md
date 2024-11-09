---
title: 8.逻辑语句
date: 2022-10-08 00:00:00
category: Python语言
tags:
  - if
  - elif
  - else
  - for
  - while
  - break
  - continue
---

# 条件语句

## if语句

作用：如果if语句的条件成立则执行缩进内的代码。

```python
if 条件:
  条件成立执行的代码
  注意代码的缩进
```

使用案例：

```python
# 修改True => False使条件不成立
flag = True

if flag:
  print("我是缩进的代码")
  print("只有条件成立才执行")

print("我不是缩进的代码，条件是否成立与我无关。")
```

## else语句

作用：与if语句配合使用，如果条件不成立时执行缩进内的代码。

```python
if 条件:
  条件成立所执行的代码
else:
  条件不成立所执行的代码
```

使用案例：

```python
age = 20

if age >= 18:
  print("已成年")
  print("终于可以去网吧了！")
else:
  print("未成年")
  print("还不能去网吧哦！")
```

## elif语句

作用：用于多重条件判断，else if的缩写，即if语句的条件不成立，并且本语句条件成立时执行缩进内的代码。

```python
if 条件1:
  条件1成立执行的代码
elif 条件2:
  条件2成立执行的代码
elif 条件3:
  条件3成立执行的代码
else:
  以上条件都不成立执行的代码
```

> else需要在语句的最后使用。

使用案例：

```python
level = int(input("请输入您的等级："))

if level > 0 and level <= 10:
  print("您是魂士")
elif 10 <= level <= 20:
  print("您是魂师")
elif 20 <= level <= 30:
  print("您是大魂师")
elif 30 <= level <= 40:
  print("您是魂尊")
elif 40 <= level <= 50:
  print("您是魂宗")
elif 50 <= level <= 60:
  print("您是魂王")
elif 60 <= level <= 70:
  print("您是魂帝")
elif 70 <= level <= 80:
  print("您是魂圣")
elif 80 <= level <= 90:
  print("您是魂斗罗")
elif 90 <= level < 100:
  print("您是封号斗罗")
else:
  print("恭喜，您已成神！")
```

## 三目运算符

作用：简化if-else语句。

```python
条件成立执行的代码 if 条件 else 条件不成立执行的代码
```

> 该运算符可以有返回值

# 循环语句

## while循环

作用：在条件成立时，循环执行缩进内的代码。

```python
while 条件:
  条件成立时循环执行的代码
  每次循环时都会判断条件
else:
  循环正常结束时执行的代码
```

使用案例：

```python
# 输出100遍
num = 100
while num > 0:
  print("媳妇，我错了！")
  num -= 1
else:
  print("我真的知道错了")
print("原谅你了")
```

## for循环

作用：循环遍历序列。

```python
for 临时变量 in 序列:
  循环执行的代码
else:
  循环正常结束执行的代码
```

使用案例：

```python
# 输出10以内的奇数
for i in range(10):
  if i % 2 == 0:
    continue
  print(i)
```

> continue：结束本次循环，开始下次循环。
>
> break：强行跳出循环，else语句不会执行。
