---
title: 5.远程仓库
date: 2023-12-16 21:10:35
categories: Git
tags: 
  - github
  - gitee
  - gitlab
---

# 常见远程仓库

* [GitHub](https://github.com/)：是一个面向开源及私有软件项目的托管平台，因为只支持Git作为唯的版本库格式进行托管。
* [Gitee](https://gitee.com/)：是国内的一个代码托管平台，由于服务器在国内，所以相比于GitHub，码云速度会更快。
* [GitLab](https://about.gitlab.com/)：是一个用于仓库管理系统的开源项目，使用Git作为代码管理工具，并在此基础上搭建起来的web服务。

# SSH协议

## 生成密钥

```bash
ssh-keygen -t rsa -C "xxxxx@xxxxx.com"
```

> 会在用户目录下生成`.ssh`文件夹，包含了公钥和私钥，作为用户的凭据。

然后在远程仓库托管平台配置ssh公钥。

## 测试

```bash
ssh -T git@gitee.com
```

> 出现successfully即成功。

最后可以通过ssh克隆。

# 仓库管理

## 添加

```bash
git remote add RemoteName Url
```

## 移除

```bash
git remote rm RemoteName
```

## 查看

```bash
git remote -v
```

# 代码管理

## 克隆

```bash
git clone Url
```

## 推送

```bash
git push [RemoteName BranchName|TagName]
```

> -f：强制推送

## 拉取

```bash
git pull [RemoteName RemoteBranch:LocalBranch]
```

> 拉取远程仓库的代码，且合并到本地仓库。

## 抓取

```bash
git retch [RemoteName BranchName]
```

> 抓取远程仓库的代码，但不会合并到本地仓库。

# 冲突

产生原因：

* 不同分支下的merge。
* 同一个分支下的pull或push。

解决：

* push之前，先pull。
* 手动删除冲突内容的代码。

# 关于GitHub访问慢

[获取最新DNS](https://raw.hellogithub.com/hosts)，修改本地hosts文件，并刷新DNS。

* Windows刷新DNS

```bash
ipconfig/flushdns
```

* MacOS刷新DNS

```bash
sudo killall -HUP mDNSResponder
```

