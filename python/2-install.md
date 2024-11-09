---
title: 2.Python安装
date: 2022-10-02 00:00:00
category: Python语言
tags:
  - 安装
  - 卸载
  - 解释器
  - CPython
---

本节会从常见的三大平台分别进行安装指导(版本根据个人情况而定，此教程以Python3.8.6为例进行安装)。一般情况下，只有Windows需要手动安装，其他平台都自带Python3。

# 解释器

Python解释器作用：运⾏⽂件

Python解释器种类 ：

* CPython，C语⾔开发的解释器[官方]，应用广泛的解释器。 

* IPython，基于CPython的⼀种交互式解释器。

* PyPy，基于Python语⾔开发的解释器。
*  Jython，运行在Java平台的解释器，直接把Python代码编译成Java字节码执⾏。 
* IronPython，运行在微软.Net平台上的Python解释器，可以直接把Python代码编译成.Net的字节码。

# Windows平台

Python官网Windows安装包[下载地址](https://www.python.org/downloads/windows/)。

## 下载

说明：左侧++Stable Releases++一列为稳定版本，右侧++Pre-releases++一列为尝鲜版本。

在浏览器中按下 [Ctrl]{.kbd} + [F]{.kbd}搜索Python3.8.6(注意区分版本后是否带有`rcl`，我们选择不带`rcl`的版本)。

找到对应的版本后，会看到有7条下载地址(后续的版本中仅有5条)供我们选择。

* `help file`：该版本的帮助文档，一般安装程序中会自带帮助文档，无需我们下载。
* `x86-64 XXX`和`x86 XXX`：64位和32位的安装程序，根据个人电脑操作系统的配置信息进行对应选择即可(查看方法：左下角Win标 -> 设置 -> 系统 -> 关于 -> 系统类型)
* `embeddable zip file`：绿色便携版zip压缩包，解压即安装，包含Python解释器，但不包含pip(包管理工具)，后续的版本中统一命名为`embeddable package (xx-bit)`，不推荐。
* `executable installer`：离线安装包可执行文件，包含了帮助文档和pip，一键式操作，适合所有人群安装，后续的版本中统一命名为`installer (xx-bit)`。
* `web-based installer`：在线安装程序可执行文件，运行程序后自动开始下载安装文件，需要两次下载，后续的版本中已经不再使用该方法安装，不推荐。

对于ARM架构的操作系统，请选择支持ARM的版本。

这里我们选择[Windows x86-64 executable installer](https://www.python.org/ftp/python/3.8.6/python-3.8.6-amd64.exe)进行下载。

## 安装

1. 双击可执行文件，运行安装包。
2. 勾选环境变量，我们推荐选择默认安装。

![](install-step2.png "step2")

3. 等待安装。
4. 安装完成，关闭程序。

![](install-step4.png "step4")

5. 环境测试。

快捷键 [Win]{.kbd} + [R]{.kbd}打开运行，输入`cmd`回车，在命令提示符窗口输入`python --version`，出现安装版本即安装成功。

![](install-step5.1.png "step5")

![](install-step5.2.png "step5")

## 问题

1. 环境测试时，没有出现版本号或者提示不是命令、程序、文件。

原因：在安装时没有勾选`Add Python 3.8 to PATH`，添加到环境变量。

关于：环境变量类似于一个软连接、快捷方式，可以在其他路径下使用。有用户变量和系统变量两类，用户变量只能在该用户下才能使用，系统变量可以在所有用户下使用。

解决：Win开始 -> 设置 -> 系统 -> 关于 -> 高级系统设置 -> 环境变量 -> 用户变量里找到`Path`双击 -> 新建以下两个路径 -> 添加好后将这两个路径通过上移置顶 -> 最后一路点击确定

```path
C:\Users\马添添\AppData\Local\Programs\Python\Python38\Scripts\
```

```path
C:\Users\马添添\AppData\Local\Programs\Python\Python38\
```

## 卸载

Win开始 -> Windows系统 -> 控制面板 -> 程序 -> 程序和功能 -> 找到Python -> 双击卸载/右键卸载

# MacOS平台

Python官网MacOS安装包[下载地址](https://www.python.org/downloads/macos/)。

## 下载

说明：左侧++Stable Releases++一列为稳定版本，右侧++Pre-releases++一列为尝鲜版本。

在浏览器中按下 [Command]{.kbd} + [F]{.kbd}搜索Python3.8.6(注意区分版本后是否带有`rcl`，我们选择不带`rcl`的版本)。

这里我们选择[macOS 64-bit installer](https://www.python.org/ftp/python/3.8.6/python-3.8.6-macosx10.9.pkg)进行下载。

另外，安装了[Homebrew](https://brew.sh/)的小伙伴可以直接通过终端命令进行下载安装。

```bash
# 查询python版本
brew search python
# 安装需要的python版本
brew install python3.8.6
```

## 安装

根据提示一直点击继续即可。

![](mac-install.png "step1")

## 卸载

```bash
# 卸载Python
brew uninstall --ignore-dependencies python
# 查看pip框架目录
pip3 -V
# 删除pip框架
sudo rm -rf /Library/Frameworks/Python.framework
```

# Linux平台

Python官网Linux安装包[下载地址](https://www.python.org/downloads/source/)。

## 下载

说明：左侧++Stable Releases++一列为稳定版本，右侧++Pre-releases++一列为尝鲜版本。

在浏览器中按下 [Ctrl]{.kbd} + [F]{.kbd}搜索Python3.8.6(注意区分版本后是否带有`rcl`，我们选择不带`rcl`的版本)。

* `Gzipped source tarball`：tgz压缩包程序。
* `XZ compressed source tarball`：tar.xz压缩包程序。

任选择一种下载即可，这里我们选择[Gzipped source tarball](https://www.python.org/ftp/python/3.8.6/Python-3.8.6.tgz)进行下载。

也可以使用`wget`工具进行下载，可自行更改版本号

```bash
wget https://www.python.org/ftp/python/3.8.6/Python-3.8.6.tgz
```

## 安装

1. 安装相关依赖

```bash
yum -y install wget gcc zlib-devel bzip2-devel openssl-devel ncurses-devel sqlite-devel readline-devel tk-devel gdbm-devel db4-devel libpcap-devel xz-devel libffi-devel
```

2. 进入安装包所在目录，我的放在`/opt/`目录下了。

```bash
cd /opt/
```

3. 解压安装包，解压后会多一个Python-3.8.6文件夹。

```bash
tar -xvf Python-3.8.6.tgz
```

4. 进入Python-3.8.6文件夹。

```bash
cd Python-3.8.6
```

5. 配置及编译安装。

```bash
# prefix参数用于指定安装路径，默认路径是/usr/local/
# enable-optimizations参数用于启用Profile引导优化(PGO)和链路时间优化(LTO)，以减缓构建过程为代价，提升10%左右性能
./configure --prefix=/usr/local/python38 --enable-optimizations
# 编译及安装
make && make install
```

6. 配置环境变量

```bash
# 编辑文件
vim ~/.bash_profile
# 设置环境变量
export PATH=$PATH:/usr/local/python38/bin
# 更新环境变量
source ~/.bash_profile
```

如果嫌以上方式麻烦，可以直接使用以下命令一键安装

```bash
# Ubuntu
sudo apt-get install python3
sudo apt-get install python3-pip

# CentOS
yum -y install python3
yum -y install python3-pip
```

7. 环境测试

```bash
# 查看Python版本
python3 -V
```

## 问题

1. 编译安装过程中，报错`can't decompress data; zlib not available`。

原因：缺少`zlib`依赖包。

关于：`zlib`是一类解压缩库。

解决：通过软件包工具进行下载安装，再次编译安装即可。

Ubuntu安装如下：

```bash
sudo apt install zlib*
```

CentOS安装如下：

```bash
yum -y install zlib*
```

## 卸载

该方法会将Python环境彻底移除，可能会导致其他功能无法使用，请谨慎使用。

```bash
# Ubuntu
sudo apt-get remove python3.6/3.8
sudo apt-get remove --auto-remove python3.6/3.8
sudo apt-get purge python3.6/3.8

# CentOS
rpm -qa |grep python3|xargs rpm -ev --allmatches --nodeps
whereis python3 |xargs rm -frv
whereis python
```