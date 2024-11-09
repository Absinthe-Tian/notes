---
title: 3.Docker镜像
date: 2023-11-08 21:25:19
categories: Docker技术
tags: 
  - 镜像
  - 指令
  - 管理
  - 下载
---

# 简介

镜像（Image）是一个Docker的==可执行文件==，其中包含运行应用程序所需的所有**代码**、**依赖**、**环境变量**和**配置文件**等。

通过镜像可以创建一个或多个容器。 

# 镜像管理

## 搜索

作用：搜索[Docker Hub 镜像仓库](https://hub.docker.com/)的镜像。

命令格式：

```bash
docker search [OPTIONS] TERM
```

命令参数（OPTIONS）：

* -f，--filter filter：根据提供的格式筛选结果（如`-f is-official=true`、`-f is-automated=true`）。
* --format string：使用Go语言format格式化输出结果。
* --limit int：展示最大结果数，默认25个。
* --no-trunc：完全内容展示。

## 查看

作用：列出本地镜像。

命令格式：

```bash
docker images [OPTIONS] [REPOSITORY[:TAG]]
或
docker image ls [OPTIONS] [REPOSITORY[:TAG]]
```

命令参数（OPTIONS）：

* -a，--all：展示所有镜像（默认隐藏底层镜像）。
* --no-trunc：完全内容展示。
* -q，--quiet：只展示镜像ID。

## 下载

作用：拉取远程镜像仓库（如：[Docker Hub 镜像仓库](https://hub.docker.com/)）到本地。

命令格式：

```bash
docker pull [OPTIONS] NAME[:TAG|@DIGEST]
```

命令参数（OPTIONS）：

* -a，--all-tags：下载所有符合tag的镜像。
* --platform：指定镜像运行的平台（arm64、amd64）。

## 删除

作用：删除一个或多个本地镜像。

命令格式：

```bash
docker rmi [OPTIONS] IMAGE [IMAGE...]
或
docker image rm [OPTIONS] IMAGE [IMAGE...]
```

命令参数（OPTIONS）：

* -f，--force：强制删除。

> 删除所有镜像：
>
> ```bash
> docker rmi -f `docker images -aq`
> ```

## 保存

作用：将一个或多个本地镜像保存为本地tar文件（输出到STDOUT）。

命令格式：

```bash
docker save [OPTIONS] IMAGE [IMAGE...]
```

命令参数：

* -o，--output string：指定导出的文件名和路径（使用`>`重定向也可）。

## 加载

作用：将保存的tar文件加载到本地镜像库。

命令格式：

```bash
docker load [OPTIONS]
```

命令参数（OPTIONS）：

* -i，--input string：指定导入文件路径（加载到STDIN）。
* -q，--quiet：不打印加载信息。

## 改名

作用：将本地镜像的NAME、TAG进行重命名，并产生一个新镜像引用。

命令格式：

```bash
docker tag SOURCE_IMAGE[:TAG] TARGET_IMAGE[:TAG]
```

## 详情

作用：查看一个或多个本地镜像详情信息。

命令格式：

```bash
docker image inspect [OPSTION] IMAGE [IMAGE...]
或
docker inspect [POSTION] IMAGE [IMAGE...]
```

> 可搭配` | grep keyword`输出指定字段信息。

命令参数（OPSTIONS）：

* -f，--format string：使用Go语言format格式化输出结果。

## 历史

作用：查看本地镜像的历史（分层）信息。

命令格式：

```bash
docker history [OPTIONS] IMAGE
```

命令参数（OPTIONS）：

* -H，--human：优化打印创建时间、大小（默认为true）。
* -q，--quiet：只显示镜像ID。
* --no-trunc：完全内容展示。
