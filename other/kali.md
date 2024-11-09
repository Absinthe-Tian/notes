---
title: 解决Kali Linux无法ssh连接
date: 2023-11-04 19:31:54
categories: 其他
tags:
  - Kali
  - ssh
---

1. 修改sshd_config文件

```bash
sudo vim /etc/ssh/sshd_config
```

* 将`PasswordAuthentication yes`前的注释删掉
* 将`PermitRootLogin without-password`修改为`PermitRootLogin yes`

2. 启动ssh服务

* 启动

```bash
service ssh start
```

* 查看

```bash
service ssh status
```

3. 设置自启动

* 开启自启动

```bash
sudo update-rc.d ssh enable
```

* 关闭自启动

```bash
sudo update-rc.d ssh disabled
```



