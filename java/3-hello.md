---
title: 3.Hello World
date: 2024-03-22 16:24:00
categories: Java语言
tags: 
  - Hello World
  - 第一个程序
---

# 第一个程序

1. 编程

创建一个**HelloWorld.java**文件，添加如下内容：

```java
public class HelloWorld {
    public static void main(String[] args) {
        System.out.println("Hello, World!"); // Display the string.
    }
}
```

> 在Java中，文件名需要和Public的类名一致。

2. 编译

```bash
javac HelloWorld.java
```

> 会产生一个**HelloWorld.class**字节码文件。

3. 运行

```bash
java HelloWorld
```

> 控制台输出`Hello World!`。

注意：不要携带`.class`后缀。

