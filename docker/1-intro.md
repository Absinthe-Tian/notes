---
title: 1.Docker介绍
date: 2023-11-08 11:58:00
category: Docker技术
tags:
  - docker
  - 引擎
  - 结构
  - 底层技术
  - 简介
---

# 介绍

Docker是开发，运行和部署应用程序的开放管理平台。

* 开发人员能利用Docker开发和运行应用程序。
* 运维人员能利用Docker部署和管理应用程序。

> 可以简单理解为：使用主机内核的虚拟机，不需要和虚拟机一样需要自己独立的操作系统，因此比虚拟机更轻量。

# 作用及解决

* Docker可以将应用程序和基础架构分开，便于程序和软件的交付。
* 借助Docker可以像管理应用程序一样管理基础架构。
* 借助Docker可以快速进行传输、测试和部署，可以缩短代码的编写和在生产环境运行之间的时间差。

Docker主要解决的问题：

* 保证程序运行环境的一致性。
* 降低配置开发环境和生产环境的复杂度和成本。
* 实现程序的快速部署和开发。

# Docker引擎

Docker Engine是一个包含以下组件的客户端-服务端（C/S）应用程序。

* 服务端：一个长时间运行的守护进程（Docker Daemon）。
* Rest API：一套用于与Docker Daemon通信并指示其执行操作的接口。
* 客户端：命令行接口CLI（Command Line Interface）。

> * CLI利用Docker命令通过Rest API直接操作Docker Daemon运行。
> * Docker Daemon负责创建并管理Docker的对象（镜像、容器、网络、数据卷）。

# Docker结构

![Docker结构](image-20231108212141416.png)

* Docker客户端（Docker Client）：
  * 是用户与Docker进行交互的最主要方式。当在终端输入docker命令时，对应的就会在服务端产生。
    对应的作用，并把结果返回给客户端。Docker Client除了连接本地服务端，通过更改或指定DOCKER_ HOST连接远程服务端。
* Docker服务端（Docker Server）：
  * Docker Daemon其实就是Docker 的服务端。它负责监听Docker APl请求(如Docker Client)并管理Docker对象。
* Docker Registries：
  * 俗称Docker仓库，专门用于存储镜像的云服务环境。Docker Hub就是一个公有的存放镜像的地方，类似Github存储代码文件。同样的也可以类似Github那样搭建私有的仓库。
* Docker对象（Docker Objects）：
  * 镜像：一个Docker的可执行文件，其中包括运行应用程序所需的所有代码内容、依赖库、环境变量和配置文件等。
  * 容器：镜像被运行起来后的实例。
  * 网络：外部或者容器间如何互相访问的网络方式，如host模式、bridge模式。
  * 数据卷：容器与宿主机之间、容器与容器之间共享存储方式，类似虚拟机与主机之间的共享文件目录。

# 底层技术

Docker使用Go语言实现，利用Linux内核的几个特性来实现：

* 命名空间（Name Space）：为Docker容器提供操作系统层面的隔离。
* 控制组（Control Groups）：为Docker容器提供硬件层面的隔离。
* 联合文件系统（Union File Systems）：利用分层思想管理镜像和容器。

而Windows和MacOS则借助了虚拟化技术，使其在这些操作系统上得以运行Docker。

# 容器格式

Docker Engine将Linux的三个特性进行组合成一个package，这就是一个容器格式（Container Format）。

Docker通过package对这三个特性进行管理控制实现容器的创建和生命周期的管理。

容器格式有很多种，其中Docker目前使用的容器格式称为libcontrainer。