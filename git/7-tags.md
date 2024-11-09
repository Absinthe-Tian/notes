---
title: 7.标签管理
date: 2023-12-17 10:42:22
categories: Git
tags:
  - 标签
  - 版本
---

Git可以给历史中的某一个提交打上标签，以示重要。

比较有代表性的是人们会使用这个功能来标记发布结点(v1.0、v1.2等)。

标签指的是某个分支某个特定时间点的状态。通过标签，可以很方便的切换到标记时的状态。

# 查看

查看所有标签：

```bash
git tag
```

查看标签信息

```bash
git show TagName
```

# 创建

```bash
git tag TagName
```

> 会在当前commit上创建标签。

# 删除

删除本地标签

```bash
git tag -d TagName
```

删除远程仓库标签

```bash
git push RemoteName :refs/tags/TagName

git push RemoteName --delete TagName
```

