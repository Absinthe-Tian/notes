---
title: 4.版本控制
date: 2023-12-16 18:58:17
categories: Git
tags: 
  - 恢复
  - 回退
---

# 添加

将工作区中修改的文件添加到暂存区中。

```bash
git add FileName
```

> 也可使用`.`通配符来调配所有修改的文件。

# 提交

将暂存区中修改的文件提交到版本库中。

```bash
git commit -m "提交信息"
```

> -m：需要提供本次提交的信息，说清该版本的更新情况即可。
>
> -a：添加修改到暂存区并提交到版本库，不能用于第一次提交的文件。
>
> --amend：覆盖最后一次提交信息，可用于提交信息的修改。

# 恢复

将工作区、暂存区进行恢复还原。

```bash
git restore FileName
```

> --staged、-S：取消暂存区未提交的修改。
>
> --worktree、-W（默认）：取消工作区未暂存的修改。

# 回退

将工作区、暂存区、版本库进行版本的回退。

```bash
git reset HEAD|版本号
```

> --soft：将版本库进行回退，工作区及暂存区不变。
>
> --mixed（默认）：将版本库及暂存区进行回退，工作区不变。
>
> --hard：将版本库、暂存区及工作区都进行回退。

其中HEAD指代当前版本，HEAD^指代上个版本，以此类推，HEAD~N即指代前N个版本。

