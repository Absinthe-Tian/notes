---
title: 5.Java数据类型
date: 2024-03-22 19:02:36
categories: Java语言
tags: 
  - 变量
  - 基本数据类型
  - 标识符
---

# 变量

Java变量是用于存储数据的容器，其值可以在程序执行过程中改变。

```java
数据类型 变量名 = 数据;
```

用法：

```java
public class HelloWorld {
    public static void main(String[] args) {
        int a = 10, b = 20; // 初始化a变量和b变量
        int c = a + b; // 计算a变量和b变量的和并赋值给c变量
        a = 5; // 修改a变量的值
        System.out.println(a);  // 输出a变量的值
        System.out.println(b);  // 输出b变量的值
        System.out.println(c);  // 输出c变量的值
    }
}
```

注意：

* 变量只能保存一个数据。
* 变量名不能重复。
* 一条语句可以定义多个变量。
* 变量在使用前一定要进行赋值。
* 注意变量的作用范围。

# 基本数据类型

Java中基本数据类型有4类8种。

其他类型为引用类型。

默认初始化值：

* 整数 = 0
* 浮点 = 0.0
* 字符 = '\u0000' 空格
* 布尔 = false
* 引用 = null

1. **整数类型**：
   - `byte`：1 字节，取值范围为 -128 到 127。
   - `short`：2 字节，取值范围为 -32768 到 32767。
   - `int`：4 字节，取值范围为 -2147483648 到 2147483647。
   - `long`：8 字节，取值范围为 -9223372036854775808 到 9223372036854775807。
2. **浮点类型**：
   - `float`：4 字节，单精度，取值范围为约 ±3.40282347e+38F。
   - `double`：8 字节，双精度，取值范围为约 ±1.79769313486231570e+308。
3. **字符类型**：
   - `char`：2 字节，Unicode 字符，取值范围为 '\u0000'（0）到 '\uffff'（65535）。
4. **布尔类型**：
   - `boolean`：1 比特，表示 true 或 false。

注意：long类型需要在数据后添加`L`，float类型需要在数据后添加`F`。

# 包装类

Java基本数据类型（如int、double、char等）是不具备对象特性的，不能参与面向对象的操作。为了使这些基本数据类型能够以对象的形式参与面向对象的操作，Java提供了对应的包装类（Wrapper Classes）。

这些包装类都是不可变的（immutable），意味着一旦创建就不能修改其值。在需要对基本数据类型进行操作时，可以使用对应的包装类来进行处理。此外，自动装箱（autoboxing）和自动拆箱（autounboxing）功能使得基本数据类型和其对应的包装类之间的转换更加方便。

自动装箱：基本数据类型 ——> 包装类

自动拆箱：包装类 ——> 基本数据类型

| 基本数据类型 | 包装类    |
| ------------ | --------- |
| byte         | Byte      |
| short        | Short     |
| char         | Character |
| int          | Integer   |
| long         | Long      |
| float        | Float     |
| double       | Double    |
| boolean      | Boolean   |

# 标识符

在Java中，标识符是用来给变量、方法、类、包等命名的字符序列。标识符必须遵循以下规则：

1. **字符集**：标识符可以由字母、数字、下划线和美元符号组成。
2. **起始字符**：标识符的第一个字符不能是数字。
3. **关键字**：标识符不能是Java的关键字或保留字。
4. **大小写敏感**：Java中的标识符是大小写敏感的。

除此之外，还应遵循以下规范：

1. **小驼峰命名（方法、变量）**：第一个单词小写，其他单词首字母大写，如`firstName`。
2. **大驼峰命名（类名）**：每个单词首字母都大写，如`BadPerson`。

# 键盘输入

Java和很多其他语言一样，同样支持用户使用键盘输入，使用的是Scanner类。

```java
// 导入Scanner类
import java.util.Scanner;

public class ScannerDemo {
    public static void main(String[] args) {
        // 创建Scanner对象
        Scanner scanner = new Scanner(System.in);
        // 打印输入提示
        System.out.print("请输入：");
        // 接收键盘输入
        String input = scanner.nextLine();
        // 关闭Scanner输入
        scanner.close();
        // 输出键盘内容
        System.out.println("输入的内容是：" + input);
    }
}
```

