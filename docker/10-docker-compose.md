---
title: 10.Docker Compose
date: 2023-11-11 18:33:56
categories: Docker技术
tags: 
  - Docker Compose
  - 安装
  - 指令
  - 部署
---

# 简介

Docker Compose是用于定义和运行多容器 Docker 应用程序的工具。 借助 Compose，我们可以使用 YAML 文件来配置应用程序的服务。 只需一个命令，即可创建并启动所有服务。一般与Dockerfile搭配使用。

# 安装

Mac和Windows安装Docker后会携带Docker Compose，而Linux则需要额外安装，由于我们在[安装docker](https://blog.absinthe.love/docker/1547294c/#%E5%90%84%E5%B9%B3%E5%8F%B0%E5%AE%89%E8%A3%85)时已经一起安装了，如未安装可按以下方式安装。

查看docker-compose版本：

```bash
docker compose version
```

出现版本即已安装。

## 自动安装

```bash
# Ubuntu
sudo apt-get update
sudo apt-get install docker-compose-plugin

# CentOS
sudo yum update
sudo yum install docker-compose-plugin
```

## 手动安装

1. 下载安装包

```bash
DOCKER_CONFIG=${DOCKER_CONFIG:-$HOME/.docker}

mkdir -p $DOCKER_CONFIG/cli-plugins

curl -SL https://github.com/docker/compose/releases/download/v2.23.0/docker-compose-linux-x86_64 -o $DOCKER_CONFIG/cli-plugins/docker-compose
```

> arm架构的可下载：https://github.com/docker/compose/releases/download/v2.23.0/docker-compose-linux-aarch64。

其他版本请参考：[Releases · docker/compose](https://github.com/docker/compose/releases)。

2. 执行文件

```bash
chmod +x $DOCKER_CONFIG/cli-plugins/docker-compose
```

# TOP配置参数

[Compose file官方文档](https://docs.docker.com/compose/compose-file/03-compose-file/)。

Docker Compose File顶级配置项：

* [version、name](https://docs.docker.com/compose/compose-file/04-version-and-name/)：指定Docker Compose File版本号、名称。
* [**services**](https://docs.docker.com/compose/compose-file/05-services/)：定义多个服务并配置启动参数。
* [network](https://docs.docker.com/compose/compose-file/06-networks/)：指定在多个服务中共同使用的网络对象。

* [volumes](https://docs.docker.com/compose/compose-file/07-volumes/)：声明或创建在多个服务中共同使用的数据卷。
* [config](https://docs.docker.com/compose/compose-file/08-configs/)：声明在本服务中要使用的一些配置文件。
* [secrets](https://docs.docker.com/compose/compose-file/09-secrets/)：声明在本服务中要使用的一些密钥、密码文件。
* [x-***](https://docs.docker.com/compose/compose-file/11-extension/)：自定义配置，用于复用多个服务的相同配置。

# 单机部署

使用Docker Compose File在单个机器部署一系列服务。

使用Node.js和MongoDB的`docker-compose.yaml`：

```yaml
version: '3.8'

services:
  webapp:
    image: node:14
    container_name: my_webapp
    working_dir: /app
    ports:
      - "3000:3000"
    volumes:
      - ./webapp:/app
    env_file:
      - .env  # 使用 .env 文件
    command: npm start
    depends_on:
      - database
    networks:
      - my_network

  database:
    image: mongo:latest
    container_name: my_mongo
    ports:
      - "27017:27017"
    volumes:
      - mongo_data:/data/db
    env_file:
      - .env  # 使用 .env 文件

networks:
  my_network:
    driver: bridge

volumes:
  mongo_data:
```

其中的`.env`文件内容如下：

```tex
# .env
NODE_ENV=development
DEBUG=my_webapp:*

MONGO_INITDB_ROOT_USERNAME=root
MONGO_INITDB_ROOT_PASSWORD=example
```

# 集群部署

集群部署的方案有很多，可以使用docker内置的[Swarm](https://docs.docker.com/engine/swarm/)，也可以使用[Kubernetes](https://kubernetes.io/zh-cn/)。
