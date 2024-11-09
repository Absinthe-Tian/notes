---
title: 2.安装与配置
date: 2023-12-16 14:37:04
categories: Git
tags: 
  - 安装
  - 配置
---

# 安装

在[Git - Downloads](https://git-scm.com/downloads)下载对应操作系统的安装包，按照提示安装即可。

# 配置

安装的Git需要配置一下用户的信息。

在终端输入：

```bash
git config --global user.name "用户名"

git config --global user.email "邮箱"
```

查看配置信息：

```bash
git config --list
```

> 也可以在`~/.gitconfig`文件进行查看和修改。
