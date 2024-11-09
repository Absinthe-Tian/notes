---
title: 11.todo案例
category: Vue框架
tags:
  - framework
  - vue
  - demo
date: 2022-11-11 00:00:00
---



本案例由前面所学知识构建，后续会有其他版本。



## 编码流程

1. 拆分静态组件：按功能点拆分组件，命名不能冲突。
2. 实现动态组件：思考数据存放位置，共用的数据存放在父组件，否则存放在自身上。
3. 提供组件交互：绑定事件等等。



## 案例图示

![ToDoList](demo.png){height="256px" width="600px"}

拆分如下：

![ToDoList](demo_thinking.png){height="256px" width="600px"}



## 示例代码

> uuid、nanoid可作为唯一标识。

https://gitee.com/happy-knight/vue_study.git

