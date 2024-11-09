---
title: 2.Docker安装和配置
date: 2023-11-08 14:31:54
categories: Docker技术
tags:
  - 安装
  - 配置
  - install
  - settings
---

# 版本

* Docker-CE：Docker社区版，由社区维护和提供技术支持，是免费版本，适合个人开发和小团队使用。

* Docker-EE：Docker企业版，是收费版本，有售后团队和技术团队提供技术支持，专为企业开发和IT团队设计，在社区版的基础上增加一些额外功能，提供了更重要的安全保障。

> 其中，Stable版按季度发布，更加稳定；Edge版按月发布，供测试使用。

# 各平台安装

## CentOS中安装

[Install Docker Engine on CentOS | Docker Docs](https://docs.docker.com/engine/install/centos/)

1. 卸载

```bash
sudo yum remove docker \
                  docker-client \
                  docker-client-latest \
                  docker-common \
                  docker-latest \
                  docker-latest-logrotate \
                  docker-logrotate \
                  docker-engine
```

2. 更新系统工具

```bash
sudo yum install -y yum-utils
```

3. 添加软件源

```bash
sudo yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
```

> 可替换阿里云源：https://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo
>
> 或在*/etc/yum.repos.d/docker-ce.repo*中将`download.docker.com`替换为`mirrors.aliyun.com/docker-ce`

4. 安装Docker

```bash
sudo yum install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```

5. 启动Docker

```bash
sudo systemctl start docker
```

6. 查看安装

```bash
sudo docker run hello-world
```

## Ubuntu中安装

[Install Docker Engine on Ubuntu | Docker Docs](https://docs.docker.com/engine/install/ubuntu/)

1. 卸载

```bash
for pkg in docker.io docker-doc docker-compose docker-compose-v2 podman-docker containerd runc; do sudo apt-get remove $pkg; done
```

2. 更新apt及安装证书

```bash
sudo apt-get update

sudo apt-get install ca-certificates curl gnupg

sudo install -m 0755 -d /etc/apt/keyrings

curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg

sudo chmod a+r /etc/apt/keyrings/docker.gpg

echo \
  "deb [arch="$(dpkg --print-architecture)" signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu \
  "$(. /etc/os-release && echo "$VERSION_CODENAME")" stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
  
 sudo apt-get update
```

> 可替换阿里云源：https://mirrors.aliyun.com/docker-ce/linux/ubuntu/gpg

3. 安装并启动Docker

```bash
sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```

4. 查看安装

```bash
sudo docker run hello-world
```

## MacOS中安装

[Install Docker Desktop on Mac | Docker Docs](https://docs.docker.com/desktop/install/mac-install/)

## Windows中安装

[Install Docker Desktop on Windows | Docker Docs](https://docs.docker.com/desktop/install/windows-install/)

# Docker镜像加速

 [容器镜像服务 (aliyun.com)](https://cr.console.aliyun.com/cn-hangzhou/instances/mirrors)

## Ubuntu/CentOS镜像加速

1. 创建配置文件夹

```bash
sudo mkdir -p /etc/docker
```

2. 写入配置

```bash
sudo tee /etc/docker/daemon.json <<-'EOF'
{
  "registry-mirrors": ["https://xxx.mirror.aliyuncs.com"]
}
EOF
```

> 加速器地址请自行替换。

3. 重载进程

```bash
sudo systemctl daemon-reload
```

4. 重启Docker

```bash
sudo systemctl restart docker
```

# 帮助指令

作用：查看某指令的具体用法。

命令格式：

```bash
docker [COMMAND] --help
```

> 由于安装Docker时使用了`sudo`权限，因此在使用Docker指令时，应`sudo su`提权后执行。

# 服务端配置

为了让其他第三方客户端连接到Docker Server，我们需要进行一些配置。

1. 编辑配置文件

```bash
vim /usr/lib/systemd/system/docker.service
```

2. 在`ExecStart`行尾添加`-H tcp://0.0.0.0:2375`

```json
ExecStart=/usr/bin/dockerd -H fd:// --containerd=/run/containerd/containerd.sock -H tcp://0.0.0.0:2375
```

3. 重启服务

```bash
sudo systemctl daemon-reload

sudo systemctl restart docker
```

4. CentOS开启2375端口

```bash
# 开放端口
sudo firewall-cmd --zone=public --add-port=2375/tcp --permanent
# 重载防火墙
sudo firewall-cmd --reload
# 查看开放端口
sudo firewall-cmd --list-ports
```

