---
title: 5.镜像与容器的关系
date: 2023-11-09 18:20:40
categories: Docker技术
tags: 
  - 镜像
  - 容器
  - 关系
---

# 关系图

![镜像与容器关系图](image-20231109182315023.png "关系图")

# 操作

## 提交

作用：根据容器生成一个镜像（基于该容器）。

命令格式：

```bash
docker commit [OPTIONS] CONTAINER [REPOSITORY[:TAG]]
```

命令参数（OPTIONS）：

* -a，--author string：指定作者。
* -c，--change list：添加Dockerfile命令。
* -m，--message string：提交时添加注释。
* -p，--pause：提交时暂停容器（默认true）。

## 导出

作用：将容器当前的文件系统导出为tar文件。

命令格式：

```bash
docker export [OPTIONS] CONTAINER
```

命令参数（OPTIONS）：

* -o，--output string：指定导出的文件名和路径（默认STDOUT）。

## 导入

作用：将导出的tar文件导入到本地镜像库为一个新镜像。

命令格式：

```bash
docker import [OPTIONS] file|URL|- [REPOSITORY[:TAG]]
```

命令参数（OPTIONS）：

* -c，--change list：添加Dockerfile命令。
* -m，--message string：导入时添加注释

# 联合文件

Docker镜像与容器的底层都是基于联合文件系统的。

![底层关系](image-20231109191757777.png "底层关系")

通过`inspect`命令可以查看镜像的Layer层级，通过`commit`命令修改文件系统会覆盖Layer。

![镜像的Layer](image-20231109185812609.png "镜像的Layer")

![镜像的视角](image-20231109190024949.png "镜像的视角")

通过`create`命令会创建一层可读写的Layer，通过`commit`命令会将可读写Layer添加为一层只读Layer，与之前的Layer合并为一个镜像。

![容器的Layer](image-20231109191245557.png "容器的Layer")

![容器的运行](image-20231109191358752.png "容器的运行")

![容器的视角](image-20231109191607957.png "容器的视角")
