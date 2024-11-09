---
title: 11.字符串API
date: 2024-03-28 13:35:13
categories: Java语言
tags:
  - 字符串
  - API
  - String
  - StringBuilder
  - StringJoiner
---

Java API：JDK中提供了很多各种功能的Java类，这些类将底层的实现进行封装，我们只需要学习如何使用即可。

# String

不可变字符串。

## 创建

1. 直接赋值

```java
String 变量名 = "字符串";
```

> 会使用字符串常量池，有则用，无则新。

2. 使用构造方法

| 构造方法                | 说明                       |
| ----------------------- | -------------------------- |
| String()                | 空白字符串，不包含任何内容 |
| String(String original) | 传入字符串                 |
| String(char[] chs)      | 传入字符数组               |
| String(byte[] chs)      | 传入字节数组（转换为字符） |

## 拼接

String在使用`+`拼接时

JDK8以前：底层实际通过创建一个StringBuilder对象，使用`append`方法进行拼接，再使用`toString`返回一个拼接后的新String对象，每次拼接都会创建一个StringBuilder对象，浪费内存。

JDK8及以后：系统会预估字符串拼接之后的总大小，把药拼接的内容都存放在数组中，此时也是产生一个新的字符串。

拼接若无变量参与，则触发优化机制，在编译的时候就会直接拼接成完整的字符串，也可使用字符串常量池。

## 比较

使用`==`比较时，只有在字符串常量池的才能直接比较，因此应使用以下方法进行字符串比较。

| 方法                                           | 说明       |
| ---------------------------------------------- | ---------- |
| boolean equals(Object anObject)                | 直接比较值 |
| boolean equalsIgnoreCase(String anotherString) | 忽略大小写 |

## 遍历

可以使用以下方法遍历字符串。

| 方法                   | 说明             |
| ---------------------- | ---------------- |
| char charAt(int index) | 根据索引返回字符 |
| int length()           | 返回字符串的长度 |

## 截取

| 方法                                           | 说明         |
| ---------------------------------------------- | ------------ |
| String substring(int beginIndex)               | 截取到末尾   |
| String substring(int beginIndex, int endIndex) | 左闭右开截取 |

## 替换

| 方法                                                         | 说明     |
| ------------------------------------------------------------ | -------- |
| String replace(CharSequence target, CharSequence replacement) | 字符替换 |

# StingBuilder

可变字符串的容器，可提高字符串的执行效率（不会有过多的内存开销）。

默认容量16长度，当添加的内容长度大于16时，会自动扩容（原来容量*2+2=34），若扩容还不够，则以实际长度为准。

## 创建

| 构造方法                  | 说明               |
| ------------------------- | ------------------ |
| StringBuilder()           | 空白可变字符串对象 |
| StringBuilder(String str) | 传入字符串         |

## 方法

| 方法                                       | 说明           |
| ------------------------------------------ | -------------- |
| StringBuilder append(任意类型)             | 拼接数据       |
| StringBuilder insert(int offset, 任意类型) | 插入数据       |
| StringBuilder delete(int start, int end)   | 删除数据       |
| StringBuilder reverse()                    | 反转数据       |
| int length()                               | 返回长度       |
| int capacity()                             | 返回当前容量   |
| String toString()                          | 返回字符串类型 |

# StringJoiner

可变字符串的容器，多应用于字符串格式拼接。

## 创建

| 构造方法                                                     | 说明                             |
| ------------------------------------------------------------ | -------------------------------- |
| StringJoiner(CharSequence delimiter)                         | 指定拼接间隔符号                 |
| StringJoiner(CharSequence delimiter, CharSequence prefix, CharSequence suffix) | 指定间隔符号、开始符号、结束符号 |

## 方法

| 方法                                      | 说明           |
| ----------------------------------------- | -------------- |
| StringJoiner add(CharSequence newElement) | 拼接字符串     |
| int length()                              | 返回长度       |
| String toString()                         | 返回字符串类型 |

