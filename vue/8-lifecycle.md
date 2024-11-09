---
title: 8.生命周期
category: Vue框架
tags:
  - framework
  - vue
  - lifecycle
date: 2022-11-08 00:00:00
---



## 生命周期概念

生命周期，也称生命周期回调函数、生命周期钩子，是Vue在运行过程中的关键时刻，此时Vue会调用的一系列函数，函数名是固定的，且this指向vm或组件实例对象。

> 官方详情：
>
> [生命周期 — Vue2.js(vuejs.org)](https://v2.cn.vuejs.org/v2/guide/instance.html#实例生命周期钩子)。
>
> [生命周期 — Vue3.js(vuejs.org)](https://cn.vuejs.org/guide/essentials/lifecycle.html#registering-lifecycle-hooks)。



## Vue2生命周期

![lifecycle](lifecycle.png){height="256px" width="600px"}

### 初始化周期

创建阶段

```js
// 数据监视、数据代理创建之前
beforeCreate(){}

// 数据监视、数据代理创建完毕
created(){}
```

挂载阶段

```js
// 挂载之前
beforeMount(){}

// 挂载完毕
*mounted(){}
```

### 更新周期

更新阶段

```js
// 更新之前
beforeUpdate(){}

// 更新完毕
updated(){}
```

### 销毁周期

销毁阶段

```js
// 销毁之前
*beforeDestroy(){}

// 销毁完毕
destroyed(){}
```

> 销毁后Vue开发者工具无信息、自定义事件失效。
>
> beforeDestory周期数据可用，但操作数据也不会触发更新流程。

### 其他生命周期

下一次更新完DOM调用

```js
this.$nextTick(function () {
  ...
})
```

> 一般在methods里使用。

路由组件激活时调用

```js
activated() {
	...
}
```

路由组件失活时调用

```js
deactivated() {
	...
}
```



## Vue3生命周期

![lifecycle_vue3](lifecycle_vue3.png){height="256px" width="600px"}

- Vue2.x中的生命周期钩子，但有有两个被更名：
  - ```beforeDestroy```改名为 ```beforeUnmount```
  - ```destroyed```改名为 ```unmounted```
- Vue3.0也提供了 Composition API 形式的生命周期钩子，与Vue2.x中钩子对应关系如下：
  - `beforeCreate`：`setup()`
  - `created`：`setup()`
  - `beforeMount`：`onBeforeMount`
  - `mounted`：`onMounted`
  - `beforeUpdate`：`onBeforeUpdate`
  - `updated` ：`onUpdated`
  - `beforeUnmount`：`onBeforeUnmount`
  - `unmounted`：`onUnmounted`

> 配置项优先级 > 组合式优先级
>
> 参数可以是箭头函数。



