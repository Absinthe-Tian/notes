---
title: 6.Docker网络
date: 2023-11-10 11:18:25
categories: Docker技术
tags: 
  - 网络
  - 管理
  - 驱动模式
---

# 作用

容器的网络默认与宿主机、与其他容器都是相互隔离的。

在开发和使用过程中，容器与宿主机之间、容器与容器之间通常都是有网络需求的，此时我们就可以使用Docker中的网络管理对其进行操作。

# 驱动模式

Docker有5种内置网络驱动模式

* Bridge  network模式（网桥）：默认的网络模式，类似虚拟机的nat模式（应用较多）。
* Host network模式（主机）：容器直接使用宿主机的网络（性能最好）。
* None network模式（null）：容器禁用所有网络。
* Overlay network模式（覆盖）：利用VXLAN实现的bridge模式。
* Macvlan network模式（Mac）：容器具备Mac地址，使其显示为网络上的物理设备。

特殊的驱动模式：Container network模式（应用较多）。

## bridge网络模式

![bridge网络模式](image-20231110133659640.png "bridge网络模式")

* 宿主机上需要单独的bridge网卡，如docker默认创建的docker0。
* 容器与容器、容器与主机之间的网络通信，是借助每一个容器生成的一对veth pair虚拟网络设备对进行通信的。
* 每创建一个bridge网络的容器，都会在宿主机上创建一个vethxxx的虚拟网络设备。
* 外部无法直接访问容器，需要**端口映射**才能访问。
* 容器借助veth虚拟设备通过docker0这种bridge网络设备进行通信。
* 每一个容器具备独立IP。

## host网络模式

![host网络模式](image-20231110140548333.png "host网络模式")

* 容器完全共享宿主机的网络，无网络隔离。
* 容器和宿主机上的应用所使用的端口不能重复。
* 外部可以直接访问容器，不需要端口映射。
* 容器IP就是宿主机的IP。

## none网络模式

* 容器上无网络，也没有任何网络设备。
* 如果需要使用网络，需要用户自行安装与配置。
* 适合高度定制网络的情景使用。

## overlay网络模式

![overlay网络模式](image-20231110143655679.png "overlay网络模式")

* 网络的实现方式和方案有很多，Docker中是基于VXLAN隧道技术（会在TCP/IP的网络层封装一个自己的IP和Mac地址传输到各个容器）实现。
* 主要用于实现跨主机容器之间的通信，适合涉及跨主机的容器集群管理的情景使用。

## macvlan网络模式

![macvlan网络模式](image-20231110145004309.png "macvlan网络模式")

* 网络通信直接基于Mac地址进行转发，让容器像一个真实物理设备。
* 宿主机充当二层交换机，Docker会维护一个Mac地址表，根据地址表找到对应的容器并进行传输。
* 容器之间可以直接通过IP互通，通过宿主机上内建的虚拟网络设备，但是与主机无法直接使用IP互通。
* 对于每个外来数据报都是与容器Mac地址进行通信的场景使用。

## container网络模式：

![container网络模式](image-20231110150314824.png "container网络模式")

会将指定的容器作为“主机”并共享其网络，与指定的容器无网络隔离。

```bash
docker run/create --network container:CONTAINER ...
```

# 网络管理

## 查看

作用：查看已经建立的网络对象。

命令格式：

```bash
docker network ls [OPTIONS]
```

命令参数（OPTIONS）：

* -f，--filter filter：根据提供的格式筛选结果。
* --format string：使用Go语言format格式化输出结果。
* --no-trunc：完全内容展示。
* -q，--quiet：只输出网络对象的ID。

## 创建

作用：创建新的网络对象。

命令格式：

```bash
docker network create [OPTIONS] NETWORK
```

命令参数（OPTIONS）：

* -d，--driver string：指定网络的驱动（默认bridge）。
* --subnet string：指定子网网段（如192.168.0.0/16）。
* --ip-range string：指定容器IP范围，subnet子集。
* --gateway string：指定子网IPv4或IPv6网关（如192.168.0.1）。

注意：

* host、none模式网络只能存在一个。
* Docker自带的overlay模式创建依赖Docker swarm（集群负载均衡）服务。
* 192.168.0.0/16 == 192.168.0.0 ~ 192.168.255.255
* 172.88.0.0/24 == 172.88.0.0 ~ 172.88.0.255

## 删除

作用：删除一个或多个网络。

命令格式：

```bash
docker network rm NETWORK [NETWORK]
```

## 详情

作用：查看一个或多个网络的详情信息。

命令格式：

```bash
docker network inspect [OPTIONS] NETWORK [NETWORK...]
或
docker inspect [OPTIONS] NETWORK [NETWORK...]
```

命令参数（OPTIONS）：

* -f，--format string：使用Go语言format格式化输出结果。

## 使用

作用：为启动的容器指定网络。

命令格式：

```bash
docker run/create --network NETWORK ...
```

> 默认使用名为bridge的网络。

## 连接/断开

作用：连接/断开指定容器与网络。

命令格式：

```bash
docker network connect/disconnect [OPTIONS] NETWORK CONTAINER
```

命令参数（OPTIONS）：

* -f，--force：强制断开连接。

注意：

* 不能用于host网络、overlay网络的连接与断开。
* none网络、overlay网络和其他网络不能同时连接。
* bridge网络和macvlan网络可以同时连接多个。

## 端口映射

作用：用于bridge网络模式下，外部访问容器内部服务。

命令格式：

```bash
docker run/create -P/-p ...
```

命令参数：

* -P，--publish-all：将容器内所有暴露的端口进行随机映射。
* -p，--publish list：手动指定端口映射。

注意：

-p [HOST_IP]:[HOST_PORT]:CONTAINER_PORT

* `-p ::80`：将容器的80端口随机映射到主机任意IP随机端口。
* `-p :16379:6379`：将容器的6379端口映射到主机任意IP的16379端口。
* `-p 192.168.0.0::3306`：将容器的3306端口映射到主机该IP的随机端口。
* `-p 192.168.0.1:13306:3306`：将容器的3306端口映射到主机该IP的13306端口。
