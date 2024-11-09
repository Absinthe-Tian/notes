---
title: 6.分支管理
date: 2023-12-16 23:55:52
categories: Git
tags: 
  - branch
  - merge
---

![分支](image-20231216235839358.png)

# 查看

```bash
git branch
```

> -r：列出所有远程分支。
>
> -a：列出所有本地和远程分支。

## 创建

在当前分支创建一个子分支。

```bash
git branch BranchName [CommitID|BranchName|TagName]
```

> -m：修改分支名。

## 切换

```bash
git checkout BranchName
```

> -b：在当前分支下创建并切换到该分支。

## 合并

将指定分支合并到当前分支。

```bash
git merge BranchName
```

## 删除

删除本地分支：

```bash
git branch -d BranchName
```

> -d：删除已合并的分支。
>
> -D：强制删除，不管该分支是否已合并。未合并删除会给出CommitID，可再次使用该ID创建分支。

删除远程分支：

```bash
git push origin -d BranchName
```

