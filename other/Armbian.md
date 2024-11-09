---
title: 玩客云3刷Armbian-32.02-jammy
date: 2024-01-06 18:10:16
categories: 其他
tags:
  - 刷机
  - Linux
  - Armbian
---

# 工具准备

* 玩客云3主机 + 电源线 + 网线。
* 一条双头USB线 + 短接线/镊子。
* 烧录软件 + 系统镜像。
* 一台Windows电脑。

链接: https://pan.baidu.com/s/1DMq-7yqFm7rduyl1ooCsJQ?pwd=v52y 提取码: v52y 
--来自百度网盘超级会员v5的分享

# 刷机

1. 拆机

对后盖使用热吹风加热，敲开后盖，拧下螺丝，取出主板。

2. 查看版本

查看卡槽位置的版本号，一般为V1.0、V1.3。

3. 建立连接

使用双头USB线，一端连接电脑USB口，一端连接玩客云USB2口（靠近HDMI）。

4. 插上电源

将玩客云的电源接上，先不要通电（使用有开关的插座更方便），短接焊点后再通电。

5. 短接焊点

v1.0短接焊点在背面如下：

![v1.0](image-20240107000937940.png)

v1.3短接焊点在正面如下：

![v1.3](image-20240107001026938.png)

短接焊点后打开插座开关，给玩客云主机供电，在烧录前都不要松开短接。

6. 开始烧录

打开烧录软件，导入镜像文件，在玩客云供电后，点击开始烧录。

7. 重启玩客云

关闭电源再开启电源，让玩客云系统重启。

# 系统设置

根据自家路由器查看ip，使用ssh工具连接。

系统默认的用户名及密码：root@1234

1. 设置用户及密码
2. 设置时区

# 挂载磁盘

1. 查看所有磁盘

```bash
fdisk -l
```

> 查看添加的磁盘是哪个，如：*/dev/mmcblk0*。

2. 格式化磁盘

```bash
mkfs.ext4 /dev/mmcblk0
```

3. 挂载

```bash
mkdir /mnt/disk

mount /dev/mmcblk0 /mnt/disk
```

> 将磁盘挂载到*/mnt/disk*目录。

4. 自动挂载

查看UUID：

```bash
sudo blkid
```

> 仅查看需要自动挂载的磁盘UUID。

修改配置文件：

```bash
vim /etc/fstab
```

添加如下内容：

```tex
UUID=uuid /mnt/disk ext4 defaults 1 2
```

> value的6个字段分别为：UUID、挂载目录、文件系统类型、挂载选项、文件系统检查顺序（越小越早）、文件系统检查时间间隔（越小间隔越短）。

检查配置：

```bash
mount -a
```

若未报错，则可重启测试是否自动挂载。

# 换源

常用源：

* https://mirrors.tuna.tsinghua.edu.cn/armbian/
* https://mirrors.ustc.edu.cn/armbian/
* https://mirrors.aliyun.com/armbian/

1. 查看发行版本号

```bash
cat /etc/os-release
```

`VERSION_CODENAME`即版本号。

常见的发行版本号有`stretch`、`buster`、`jammy`。

2. 换源

替换source.list

```bash
sudo nano /etc/apt/sources.list
```

以`jammy`版本为例，替换如下内容：

```tex
deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu-ports/ jammy main restricted universe multiverse

deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu-ports/ jammy-updates main restricted universe multiverse

deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu-ports/ jammy-backports main restricted universe multiverse

deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu-ports/ jammy-security main restricted universe multiverse
```

> 参数：换源地址、发行版本号、组件列表。

组件列表可选如下：

- `main`：官方支持的自由软件。
- `contrib`：官方支持的非自由软件。
- `non-free`：官方不支持的非自由软件。
- `restricted`：受限制的软件，通常包含固件和其他受限组件。
- `universe`：社区维护的自由软件。
- `multiverse`：社区维护的非自由软件。

替换armbian.list任意换源地址

```bash
sudo vim /etc/apt/sources.list.d/armbian.list
```

# 安装CasaOS

```bash
curl -fsSL https://get.casaos.io | bash
```

> 浏览器访问即可。

# 安装Docker

1. 安装

```bash
curl -fsSL https://get.docker.com | bash -s docker –mirror Aliyun
```

2. 镜像加速

 [容器镜像服务 (aliyun.com)](https://cr.console.aliyun.com/cn-hangzhou/instances/mirrors)获取加速器链接。

停止docker服务

```bash
systemctl stop docker.socket & systemctl stop docker.service
```

修改配置

```bash
vim /etc/docker/daemon.json
```

添加如下内容：

```json
{
  "registry-mirrors": ["https://xxx.mirror.aliyuncs.com"]
}
```

启动docker服务

```bash
systemctl start docker.socket & systemctl start docker.service
```

# 安装Jellyfin

* 使用Docker安装（版本较低）

```bash
docker run -d -p 8096:8096 -v /mnt/SD1T/jellyfin/config:/config -v /mnt/SD1T/jellyfin/media:/media -v /mnt/sata1/jellyfin/media:/media2 --name jellyfin jellyfin/jellyfin
```

> 一个配置文件夹，两个媒体文件夹。

* 直接安装

```bash
sudo apt install curl gnupg
```

```bash
curl -fsSL https://repo.jellyfin.org/ubuntu/jellyfin_team.gpg.key | sudo gpg --dearmor -o /etc/apt/trusted.gpg.d/jellyfin.gpg
```

```bash
echo "deb [arch=$( dpkg --print-architecture )] https://repo.jellyfin.org/$( awk -F'=' '/^ID=/{ print $NF }' /etc/os-release ) $( awk -F'=' '/^VERSION_CODENAME=/{ print $NF }' /etc/os-release ) main" | sudo tee /etc/apt/sources.list.d/jellyfin.list
sudo apt update
```

```bash
sudo apt update && sudo apt install jellyfin -y
```

# 内网穿透

[Sakura Frp | 樱花内网穿透 — 免费内网穿透](https://www.natfrp.com/)

1. 确认处理器架构

```bash
uname -m
```

处理器对应表：

| 输出                | 架构        |
| ------------------- | ----------- |
| `i386`, `i686`      | i386        |
| `x86_64`            | amd64       |
| `arm`, `armel`      | arm_garbage |
| `armv7l`, `armhf`   | armv7*      |
| `aarch64`, `armv8l` | arm64       |
| `mips`              | mips*       |
| `mips64`            | mips64*     |

2. 确认处理器字节序

```bash
# 一般来说只需要使用这条命令:
echo -n I | hexdump -o | awk '{print substr($2,6,1); exit}'

# 如果上面的命令报错，请尝试这条:
echo -n I | od -to2 | awk '{print substr($2,6,1); exit}'
```

字节序对应表：

| 输出 | 架构              |
| ---- | ----------------- |
| `0`  | mips / mips64     |
| `1`  | mipsle / mips64le |

3. 下载

根据1和2获取的信息，[软件下载](https://www.natfrp.com/tunnel/download)选择对应的软件包，复制下载链接。

```bash
wget https://nya.globalslb.net/natfrp/client/launcher-unix/3.0.7/natfrp-service_linux_armv7.tar.zst
```

4. 解压

需要使用**zstd**解压，请先使用apt进行安装。

```bash
tar -I zstd -xvf natfrp-service_*.tar.zst
```

5. 设置权限

```bash
chmod +x frpc natfrp-service
```

## 启动器

使用systemd来启动服务。

1. 创建Unit文件

```bash
sudo vim /etc/systemd/system/natfrp.service
```

添加如下内容：

```ini
[Unit]
Description=SakuraFrp Launcher
After=network.target

[Service]
User=用户名
Group=用户组

Type=simple
TimeoutStopSec=20

Restart=always
RestartSec=5s

ExecStart=/软件包路径/natfrp-service --daemon

[Install]
WantedBy=multi-user.target
```

2. 初始化

```bash
systemctl start natfrp.service

systemctl stop natfrp.service
```

> 启停一次后，会在用户目录*~*生成*.config/natfrp-service*文件夹。

3. 生成远程密钥

```bash
./natfrp-service remote-kdf <远程管理 E2E 密码>
```

> 生成的密钥包含特殊字符，注意复制完整。

4. 修改配置文件

```bash
vim ~/.config/natfrp-service/config.json
```

修改如下内容：

```json
{
   "token": "访问密钥",
   "remote_management": true,
   "remote_management_key": "远程密钥",

   "log_stdout": true, // 推荐开启 log_stdout 让 Systemd 管理日志
}
```

5. 服务自启动/状态

```bash
systemctl enable --now natfrp.service

systemctl status natfrp.service
```

6. 打开[远程管理](https://www.natfrp.com/remote/v2)

选择设备及E2E密码即可

## 客户端

1. 创建工作目录

```bash
mkdir -p /usr/local/etc/natfrp
```

2. 创建Unit文件

```bash
vim /etc/systemd/system/frpc@.service
```

添加如下内容：

```ini
[Unit]
Description=SakuraFrp Service
After=network.target

[Service]
Type=idle
#DynamicUser=yes
Restart=on-failure
RestartSec=60s
ExecStart=/软件包路径/frpc -f %i
WorkingDirectory=/usr/local/etc/natfrp

[Install]
WantedBy=multi-user.target
```

3. 重载服务

```bash
systemctl daemon-reload
```

4. 启/停隧道

```bash
systemctl start Unit

systemctl stop Unit
```

> Unit：frpc@token:隧道ID

5. 自启用/禁用

```bash
systemctl enable Unit

systemctl disable Unit
```

6. 查看

```bash
# 查看某个隧道状态
systemctl status Unit

# 查看某个隧道日志
journalctl -u Unit

# 查看所有隧道
systemctl list-units --all "frpc@*"
```

