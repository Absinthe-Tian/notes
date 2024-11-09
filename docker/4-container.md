---
title: 4.Docker容器
date: 2023-11-09 14:49:29
categories: Docker技术
tags: 
  - 容器
  - 指令
  - 管理
  - 生命周期
---

# 简介

容器（Container）是一种轻量级、可移植、可将程序打包的技术，使应用程序可以在几乎任何地方以相同的方式运行。

Docker将镜像文件运行起来后，产生的对象就是容器，容器相当于是镜像运行起来的一个实例，具备一定的生命周期。

# 容器与虚拟机

相同点：

* 都会对物理硬件资源进行共享使用。
* 生命周期比较相似（创建、运行、暂停、关闭等等）。
* 都可以安装各种应用，如redlis、 mysql、 nginx等。
* 资源会存储在宿主机上：linux上位于*/var/lib/docker/containers*下。

不同点：

* 虚拟机的生命周期基于一个完整的操作系统，而容器则直接运行在宿主的内核上，由一系列进程结合。
* 虚拟机是重量级的，会消耗更多资源，而容器是轻量级的，不需要额外的资源来管理（Hypervisor、GuestOS）程序。

 # 生命周期

![容器的生命周期](image-20231109152004579.png)

# 容器管理

## 创建

作用；使用镜像创建一个Created状态的待启动的容器。

命令格式：

```bash
docker create [OPTIONS] IMAGE [COMMAND] [ARG...]
```

命令参数（OPTIONS）：

* -i，--interactive：关联STDIN。

* -t，--tty：分配一个伪TTY（虚拟终端）。
* --name：容器命名。
* --rm：关闭容器时自动删除容器。

命令参数（COMMAND/ARG）：

* COMMAND：容器启动后，在容器内执行的命令，也是容器的主进程。
* ARG：COMMAND执行时提供的参数。

## 启动

作用：启动一个或多个容器。

命令格式：

```bash
docker start [OPTIONS] CONTAINER [CONTAINER...]
```

命令参数（OPTIONS）：

* -a，--attach：关联STDOUT/STDERR。
* -i，--interactive：关联STDIN。

## 重启

作用：重启一个或多个容器。

命令格式：

```bash
docker restart [OPTIONS] CONTAINER [CONTAINER...]
```

命令参数（OPTIONS）：

* -t，--time int：等待N秒后重启容器（默认10s）。

## 运行

作用：使用镜像创建并启动一个容器。

指令格式：

```bash
docker run [OPTIONS] IMAGE [COMMAND] [ARG...]
等价
docker create + docker start -a
```

命令参数（OPTIONS）：

* -i，--interactive：保持STDIN打开。
* -t，--tty：分配一个伪TTY（虚拟终端）。
* -d，--detach：容器运行在后台并打印容器ID。
* -e，--env list：设置容器中运行时的环境变量。
* -a，--attach list：关联STDIN、STDOUT、STDERR。
* -v，--volume list：绑定数据卷。
* --rm：关闭容器时自动删除容器。
*  --restart string：重启配置（默认`no`，如`always`）

## 暂停

作用：暂停一个或多个容器的所有进程。

命令格式：

```bash
docker pause CONTAINER [CONTAINER...]
```

## 恢复

作用：恢复一个或多个暂停的容器。

命令格式：

```bash
docker unpause CONTAINER [CONTAINER...]
```

## 关闭

作用：关闭一个或多个运行/暂停的容器。

命令格式：

```bash
docker stop [OPTIONS] CONTAINER [CONTAINER...]
```

> 先发送`SIGTERM`，后发送`SIGKILL`。

命令参数（OPTIONS）：

* -t，--time int：等待N秒后关闭容器（默认10s）。

## 杀死

作用：杀死一个或多个运行/暂停的容器。

命令格式：

```bash
docker kill [OPTIONS] CONTAINER [CONTAINER...]
```

> 直接发送`SINGKILL`

命令参数（OPTONS）：

* -s，--signal：发送一个信号给容器（默认`kill`）。

## 删除

作用：删除一个容器。

命令格式：

```bash
docker rm [OPTIONS] CONTAINER [CONTAINER...]
```

命令参数（OPTIONS）：

* -f，--force：强制删除一个运行中的容器。
* -l，--link：删除指定链接。
* -v，--volumes：删除指定数据卷。

## 详情

作用：查看一个或多个本地容器的详情信息。

命令格式：

```bash
docker container inspect [OPTIONS] CONTAINER [CONTAINER...]
或
docker inspect [OPTIONS] CONTAINER [CONTAINER...]
```

> 可搭配` | grep keyword`输出指定字段信息。

命令参数（OPTIONS）：

* -f，--format string：使用Go语言format格式化输出结果。
* -s，--size：显示总大小。

## 日志

作用：查看容器日志信息（来自COMMAND产生的STDOUT）。

命令格式：

```bash
docker logs [OPTIONS] CONTAINER
```

命令参数（OPTIONS）：

* --details：显示额外信息。
* -f，--follow：动态跟踪显示日志信息。
* --until string：只显示某时间节点之前的信息。
* --since string：只显示某时间节点之后的信息。
* --tail string：显示倒数几条信息（默认全部）。
* -t，--timestamps：显示timestamps时间。

## 改名

作用：重命名一个容器。

命令格式：

```bash
docker rename CONTAINER NEW_NAME
```

## 拷贝

作用：用于宿主机与容器之间、容器与容器之间的文件拷贝。

命令格式：

```bash
docker cp [OPTIONS] CONTAINER:SRC_PATH DEST_PATH|SRC_PATH CONTAINER:DEST_PATH
```

命令参数（OPTIONS）：

* -a，--archive：归档模式（复制所有uid/gid等信息）。
* -L，--follow-link：在SRC_PATH携带link符号。
* -q，--quiet：拷贝过程中不输出内容。

# 运行时操作

## 连接

作用：将当前终端的STDIN、STDOUT、STDERR关联到正在运行容器的主进程上实现连接。

命令格式：

```bash
docker attach [OPTIONS] CONTAINER
```

> 退出会关闭主进程而导致容器关闭。

命令参数（OPTIONS）：

* --no-stdin：不关联STDIN。

## 执行

作用：在容器中运行命令。

命令格式：

```bash
docker exec [OPTIONS] CONTAINER COMMAND [ARG...]
```

> 退出不会影响主进程，因此退出不会导致容器关闭。

命令参数（OPTIONS）：

* -d，--detach：后台运行命令。
* -i，--interactive：关联STDIN。
* -t，--tty：分配一个伪TTY（虚拟终端）。
* -w，--workdir string：指定到容器的工作目录。
* -e，--env list：设置容器运行时的虚拟环境。

命令参数（COMMAND/ARG）：

* COMMAND：在容器内执行的命令。
* ARG：COMMAND执行时提供的参数。
