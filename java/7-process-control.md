---
title: 7.Java流程控制
date: 2024-03-24 07:11:29
categories: Java语言
tags: 
  - if
  - else
  - switch
  - while
  - for
  - break
  - continue
  - return
  - 随机数
---

Java 中的流程控制指的是通过各种语句来控制程序执行流程的过程。

# 条件语句

一般用于范围判断。

完整语句：

```java
if(关系表达式) {
    为true，执行语句体
} else if(关系表达式2) {
    为true，执行语句体
} else if...
} else {
    否则，执行语句体
}
```

注意：执行语句体中只有一条语句，那么可以省略大括号。

# 分支语句

一般用于例举判断。

方式一

```java
switch(表达式){
    case 字面量值1,值2...:
        匹配值，执行语句体
        break;
    case 字面量值3,值4...:
        匹配值，语句体
        break;
		...
    default:
        否则，执行语句体
        break;
}
```

方式二

```java
变量 = switch (number){
  case 字面量值1,值2... -> {
      匹配值，执行语句体
  }
  case 字面量值3,值4... -> {
      匹配值，执行语句体
  }
  ...
  default -> {
      否则，执行语句体N+1
  }
}
```

> 可以将匹配值赋值给变量。

注意：表达式类型（Java12及以上）取byte、short、int、long、char、boolean、枚举、String、包装类、新变量。

# 循环语句

1. for循环

```java
for (初始化语句; 条件判断语句; 条件控制语句) {
    循环体
}
```

2. while循环

```java
初始化语句;
while (条件判断语句) {
    循环体
    条件控制语句;
}
```

注意：如果缺少条件控制语句，将会产生死循环。

3. do...while循环

```java
初始化语句;
do {
  循环体
  条件控制语句;
}
while (条件判断语句);
```

# 跳转语句

- **break**：用于跳出循环或 switch 语句，通常与循环或 switch 语句配合使用。
- **continue**：用于结束当前循环的迭代并开始下一次循环的迭代。
- **return**：用于从方法中返回值，并且结束方法的执行。

# 随机数

1. 使用 `java.util.Random` 类：

```java
int randomNumber = random.nextInt(max - min + 1) + min;
```

2. 使用 `Math.random()` 方法：

```java
int randomNumber = (int) (Math.random() * (max - min + 1)) + min;
```

