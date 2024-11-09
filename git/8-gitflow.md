---
title: 8.Git Flow
date: 2023-12-17 11:02:15
categories: Git
tags: 
  - 工作流程
  - AVN
  - Git Flow
---

[git-flow ](https://jeffkreeftmeijer.com/git-flow/)是Git进行代码管理的行为规范。

工作流通过为功能开发、发布准备和维护分配独立的分支，让发布迭代过程更流畅。

严格的分支模型也为大型项目提供了一些非常必要的结构。

# 分支介绍

* master：主干分支，开发完成的上线的项目版本。
* hotfixes：热部署分支，进行主干分支的补丁操作。
* release：预部署分支，测试工程师的调用的分支。
* develop：开发分支，开发源代码分支。
* feature：功能分支，开发者使用的分支。

## Master分支

最终上线的主分支，不能轻易去改动，每次向该分支merge时，都应该打上tag，且该分支不应该存在commit。

## Develop分支

由Master分支创建，用于开发的专用分支，所有开发者的代码汇聚地，当开发完成一个阶段后，需要merge回Release分支等待上线。

## Feature分支

由Develop分支创建，用于某功能模块的开发，该分支开发完成后需要merge回Develop分支，merge后的分支可删除可保留。

## Hotfixes分支

由Master分支创建，用于热修复Master分支上BUG及问题，开发完后需要merge回Master分支及Develop分支，并在Master分支打上tag。

## Release分支

由Develop分支创建，用于预部署，供测试人员调用，若发现BUG或问题则直接进行修复并merge回Develop分支及Master分支。

# AVN

[gitflow-avh](https://github.com/petervanderdoes/gitflow-avh/wiki)：Git的一个高效拓展工具。

使用该工具可以更快速的实现Git Flow规范。
