---
title: 2.Java安装
date: 2024-03-22 15:49:31
categories: Java语言
tags: 
  - 安装
  - Java
  - JDK
  - Oracle
---

Java是一个跨平台的高级语言，因此可以在多个平台进行安装使用。

# 下载

官网下载地址：[Java Downloads | Oracle](https://www.oracle.com/java/technologies/downloads/)

选择需要下载的Java版本号，目前为止，长期支持版本（LTS）有17、21、22，建议优先选择这几个版本。

选择与自己电脑对应平台的安装程序，大致有Linux、Mac OS、Windows三个平台，其他平台可使用包管理工具进行下载。

# 安装

如果下载的是安装程序，一键式电机安装即可，如果下载的是压缩包，使用解压指令`tar -xzvf 文件名.tar.gz`即可（注意存放路径）。

# 配置

安装完后，还需要配置环境变量才能方便在项目中使用，以下配置以Mac OS为例。

1. 查看Java版本及路径

```bash
/usr/libexec/java_home -V
```

> Matching Java Virtual Machines (1):
>     **17.0.10 (arm64)** "Oracle Corporation" - "Java SE 17.0.10" **/Library/Java/JavaVirtualMachines/jdk-17.jdk/Contents/Home**

2. 配置环境变量

```bash
vim ~/.bash_profile
```

添加以下内容：

```bash
export JAVA_HOME=/Library/Java/JavaVirtualMachines/jdk-17.jdk/Contents/Home
export PATH=$JAVA_HOME/bin:$PATH
```

更新终端：

```bash
source ~/.bash_profile
```

检验配置：

```bash
echo $JAVA_HOME
```

> 输出Java路径代表成功！

# 结构

* JVM：Java虚拟机，真正运行Java程序的部分。
* JRE：Java运行环境，由JVM、核心内库、运行工具（如java）构成。
* JDK：Java开发工具包，由JVM、核心内库、开发工具（如javac、java、javadoc、jdb、jhat）构成。

JDK > JRE > JVM
