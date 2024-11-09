---
title: 8.Docker仓库
date: 2023-11-11 13:17:22
categories: Docker技术
tags: 
  - 仓库
  - 搭建
  - 管理
---

# 简介

Docker仓库上存放docker镜像，可以通过pull命令下载的云环境。

分为公有仓库和私有仓库。

# 仓库搭建

## 无认证私有仓库的搭建

1. 在服务器上安装并配置docker。
2. 启动仓库。

```bash
docker run -dti --restart always \
--name my-registry -p 5000:5000 \
-v /my-registry/registry:/var/lib/registry \
registry
```

> registry镜像默认开放5000端口，默认镜像存放路径是*/var/lib/registry*。

## 带认证私有仓库的搭建

1. 在服务器上安装并配置docker。
2. 创建存放认证用户名和密码。

```bash
mkdir /my-registry/auth -p
```

3. 创建验证文件。

```bash
docker run --entrypoint htpasswd registry \
-Bbn USER_NAME PASSWORD > /my-registry/auth/htpasswd
```

**"htpasswd": executable file not found in $PATH**解决思路：

安装签证工具：

```bash
# CentOS
yum -y install httpd-tools

# Ubuntu
apt-get install apache2-utils
```

创建验证文件：

```bash
htpasswd -Bbn  USER_NAME PASSWORD > /my-registry/auth/htpasswd
```

4. 启动仓库。

```bash
docker run -d -p 5000:5000 --restart always \
--name my-registry \
-v /my-registry/registry:/var/lib/registry \
-v /my-registry/auth:/auth \
-e "REGISTRY_AUTH=htpasswd" \
-e "REGISTRY_AUTH_HTPASSWD_REALM=Registry Realm" \
-e "REGISTRY_AUTH_HTPASSWD_PATH=/auth/htpasswd" \
registry
```

# 仓库管理

可使用`HOST_IP:PORT/v2/_catalog`查看仓库的镜像。

## 登录

在带认证私有仓库进行上传和下载镜像时，需要先进行登录。

```bash
docker login -u USER_NAME -p PASSWORD HOTS_IP:PORT
```

## 注销

执行完毕后，可退出该认证。

```bash
docker logout HOST_IP:PORT
```

## 上传

1. 重命名：想要对上传的镜像进行重命名，命名格式如下。

```bash
docker tag IMAGE HOST_IP:PORT/IMAGE_NAME
```

2. 上传镜像：将重命名后镜像上传到服务器的私有仓库。

```bash
docker push HOST_IP:PORT/IMAGE_NAME
```

出现https错误，是因为创建的仓库默认使用http协议，而push默认使用https协议。

解决思路：使用http协议，修改配置文件*/etc/docker/daemon.json*，添加`"insecure-registries":["HOST_IP:PORT"]`，重启Docker。

## 下载

从指定的无认证私有仓库下载镜像。

```bash
docker pull HOST_IP:PORT/IMAGE_NAME
```



