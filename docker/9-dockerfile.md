---
title: 9.DockerFile
date: 2023-11-11 16:40:26
categories: Docker技术
tags:
  - Dockerfile
  - 构建
  - 指令
---

# 简介

是根据特定的语法格式撰写出来的一个普通文件。

利用docker build命令（基于commit命令）依次执行在Dockerfile中定义的一系列命令，最终生成一个新镜像（定制镜像）。

各大官方镜像[Dockerfile文档](https://github.com/docker-library/docs)。

# 使用

使用Dockerfile进行构建。

命令格式：

```bash
docker build [OPTIONS] PATH|URL|-
```

命令参数（OPTIONS）：

* -t，--tag list：指定镜像名及版本。
* -f，--file string：指定自定义Dockerfile文件。

注意：

* Dockerfile中的每一步操作都会产生一层历史记录。

* 第一次构建后，后面的构建过程中，会在更改的位置及之后的步骤重新执行，否则将会使用已有的镜像缓存。

# Dockerfile指令

[Dockerfile reference](https://docs.docker.com/engine/reference/builder/)。

常用指令如下：

* FROM：指定基础镜像。
* RUN：构建镜像过程中需要执行的命令（一般使用shell方式）。
* CMD：添加启动容器时需要执行的命令，只会执行最后一条，且会被覆盖（一般使用exec方式）。
* ENTRYPOINT：同CMD，且必须执行，不会被覆盖（一般使用exec方式）。
* LABEL：给镜像添加对应的说明数据。
* MAINTAINER：指定镜像作者（已被遗弃）。
* EXPOSE：设置对外暴露的端口，用于端口映射。
* ENV：设置执行命令时的环境变量，在构建完后依然生效。
* ARG：设置只在构建过程中使用的环境变量，构建完后失效。
* COPY：将本地文件或目录拷贝到镜像的文件系统中。
* ADD：同COPY，且解压特定格式文件，可使用URL。
* VOLUME：添加数据卷。
* USER：指定以哪个用户的权限执行RUN、CMD、ENTRYPOINT等命令。
* WORKDIR：设置工作目录。
* ONBUILD：在引用另一个Dockerfile之前，执行一些操作。
* STOPSIGNAL：设置容器退出时发出的关闭信号。
* HEALTHCHECK：设置容器状态检查。
* SHELL：更改执行shell命令的程序（Linux默认为`["/bin/sh", "-c"]`、Windows默认为`["cmd", "/S", "/C"]`。
