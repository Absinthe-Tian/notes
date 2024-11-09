---
title: 4.基础语法
date: 2024-03-22 17:18:44
categories: Java语言
tags: 
  - Grammer
  - 注释
  - 关键字
  - 字面量
  - 计算机储存
  - 进制
---

# 注释

* 单行注释

```java
// 单行注释
```

* 多行注释

```java
/* 
 * 多行注释2
 * 多行注释3
 */
```

* 文档注释

```java
/**
 * @author 作者
 * @version 1.0
*/
```

> 可以使用`javadoc`生成文档。

# 关键字

Java 提供了一组保留关键字（Reserved Keywords），这些关键字在 Java 中具有特定的含义，不能被用作标识符（如变量名、方法名、类名等）。

```java
abstract, assert, boolean, break, byte, case, catch, char, class, const, continue, default, do, double, else, enum, extends, final, finally, float, for, if, implements, import, instanceof, int, interface, long, native, new, null, package, private, protected, public, return, short, static, strictfp, super, switch, synchronized, this, throw, throws, transient, try, void, volatile, while
```

# 字面量

Java字面量是指表示固定值的数据。

| 字面量类型 |             说明             |     案例     |
| :--------: | :--------------------------: | :----------: |
|  整数类型  |       不带小数点的数字       |      18      |
|  浮点类型  |       携带小数点的数字       |     3.14     |
| 字符串类型 |      用双引号包含的内容      | "HelloWorld" |
|  字符类型  | 用单引号包含的内容且只有一个 |     'A'      |
|  布尔类型  |       仅有真和假两个值       | true、false  |
|   空类型   |          特殊值为空          |     null     |

# 计算机储存

计算机以二进制的形式储存数据，文字及字母由不同的码表对应储存，常见的码表有ASCII、GBK、Unicode、UTF-8等等。

* 二进制：由0和1组成，代码中以`0b`开头。
* 十进制：由0-9组成，代码默认进制。
* 八进制：由0-7组成，代码中以`0`开头。
* 十六进制：由0-9和a-f组成，代码中以`0x`开头。

任意进制转十进制：系数 * 基数的权次幂 相加

十进制转其他进制（除基取余）：不断除以基数得到余数，直到商为0，再将余数倒拼即可。

系数：每一位上的数。
基数：当前进制。
权：从右到左，从0开始。

# 代码块

1. 局部代码块

```java
方法() {
  {局部代码块}
}
```

> 局部变量的生命周期，节省空间。

2. 构造代码块

```java
类 {
  {构造代码块}
}
```

> 可以抽离多个构造方法的重复代码。
>
> 优先构造方法执行。

一般使用`this()`调用需要包含代码的构造方法来实现。

3. 静态代码块

```java
类 {
  static {静态代码块}
}
```

> 随类加载，只执行一次。
>
> 初始化时使用。
