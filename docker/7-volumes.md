---
title: 7.Docker数据卷
date: 2023-11-11 11:10:34
categories: Docker技术
tags: 
  - 数据卷
  - 持久化
  - 文件共享
  - 挂载
  - 管理
---

# 简介

问题：

* 宿主机无法直接访问容器中的文件。
* 容器中的文件无法持久化，导致容器删除后，文件数据也随之丢失。
* 容器之间也无法直接访问互相的文件

数据卷解决：

* 容器与主机之间、容器与容器之间共享文件。
* 容器中的数据持久化。
* 容器中的数据备份、迁移、恢复。

# 特点

* 数据卷存在于宿主机的文件系统中，独立于容器，和容器的生命周期是分离的。
* 数据卷可以目录也可以是文件，容器可以利用数据卷与宿主机进行数据共享，实现了容器间的数据共享和交换。
* 容器启动初始化时，如果容器使用的镜像包含了数据，这些数据会拷贝到数据卷中。
* 容器对数据卷的修改是实时进行的。
* 数据卷的变化不会影响镜像的更新。数据卷是独立于联合文件系统，镜像是基于联合文件系统。镜像与数据卷之间不会有相互影响。

# 挂载方式

![挂载方式](image-20231111112334982.png "挂载方式")

* bind mounts：将宿主机上的文件或目录挂载到容器上。
* volume mounts：由Docker创建和管理的。
* tmps mounts：基于内存的临时文件系统，不会存储在磁盘上。

# 挂载

## bind挂载

命令格式：

```bash
docker run/create -v，--volume HOST_DIR:CONTAINER_DIR ...
或
docker run/create --mount type=bind,src=HOST_DIR,dst=CONTAINER_DIR ...
```

注意：

* 路径必须是绝对路径。
* src指定的路径必须存在。

## volume挂载

命令格式：

```bash
docker run/create -v，--volume VOLUME_NAME:CONTAINER_DIR ...
或
docker run/create --mount type=volume,src=VOLUME_NAME,dst=CONTAINER_DIR ...
```

注意：

* VOLUME_NAME是数据的名称，会将容器的目录挂载到宿主机的*/var/lib/docker/volumes/VOLUME_NAME/_data*上。
* 如果不指定VOLUME_NAME，则会使用随机名称。
* VOLUME_NAME不存在则会创建，存在则直接使用。

## temps挂载

命令格式：

```bash
docker run/create --tmpfs CONTAINER_DIR [CONTAINER_DIR...] ...
或
docker run/create --mount type=tmpfs,dst=CONTAINER_DIR ...
```

## 数据卷容器挂载

作用：使用另一个容器的数据卷达到数据卷共享。

命令格式：

```bash
docker run/create --volumes-from CONTAINER ...
```

# 数据卷管理

## 查看

作用：查看所有volume对象。

命令格式：

```bash
docker volume ls [OPTIONS]
```

命令参数（OPTIONS）：

* -f，--filter filter：根据提供的格式筛选结果（如`dangling=true`）。
* --format string：使用Go语言format格式化输出结果。
* -q，--quiet：只展示数据卷ID。

## 详情

作用：查看一个或多个数据卷的详情信息。

命令格式：

```bash
docker volume inspect [OPTIONS] VOLUME [VOLUME...]
或
docker inspect [OPTIONS] VOLUME [VOLUME...]
```

命令参数（OPTIONS）：

* -f，--format string：使用Go语言format格式化输出结果。

## 创建

作用：创建一个volume对象。

命令格式：

```bash
docker volume create [OPTIONS] [VOLUME]
```

命令参数（OPTIONS）：

* -d，-- driver string：指定数据卷driver（默认local）。
* --label list：设置元信息。
* -o，--opt map：设置driver指定选项（默认map[]）。

## 删除

作用：删除一个或多个数据卷对象。

命令格式：

```bash
docker volume rm [OPTIONS]
```

命令参数（OPTIONS）：

* -f，--force：强制删除。

## 清理

作用：删除未被使用的数据卷对象。

命令格式：

```bash
docker volume prune [OPTIONS]
```

命令参数（OPTIONS）：

* -a，--all：删除所有未被使用的数据卷。
* --filter filter：根据提供的格式删除数据卷（如`label=LABEL`）。
* -f，--force：强制删除。

# 注意事项

* 如果挂载一个空的数据卷到容器中的一个非空目录中，那么容器目录下的文件会被复制到宿主机的数据卷中。
* 如果挂载一个非空的数据卷到容器中的一个目录中，那么容器中的目录中会显示数据卷中的数据。如果原来容器中的目录中有数据，那么这些原始数据会被隐藏掉。

这两个规则都非常重要，灵活利用第一个规则可以帮助我们初始化数据卷中的内容。掌握第二个规则可以保证挂载数据卷后的数据总是你期望的结果。
