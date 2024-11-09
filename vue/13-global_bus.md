---
title: 13.全局事件总线
category: Vue框架
tags:
  - framework
  - vue
  - bus
  - communication
date: 2022-11-13 00:00:00
---



全局事件总线：各个组件通过一个第三方组件，绑定和触发自定义事件，达到数据传递的效果。



## 安装全局事件总线

```js main.js
new Vue({
  render: h => h(App),
  beforeCreate() {
    // 安装全局事件总线，$bus=vm
    Vue.prototype.$bus = this
  }
}).$mount('#app')
```

> 原理：`VueComponent.prototype.__proto__ === Vue.prototype`，给Vue构造函数的原型对象上添加一个第三方组件vm。



## 绑定/解除自定义事件

```js component1.vue
// 绑定自定义事件
mounted() {
  this.$bus.$on("eventName",(data)=>{
    ...
  })
},
// 解除自定义事件
beforeDestroy() {
    this.$bus.$off("eventName")
}
```

> 用于接收数据。
>
> 箭头函数也可以是`methods`里的方法，其中的`data`就是传递过来的数据。



## 触发自定义事件

```js component2.vue
methods:{
    funcName(){
      this.$bus.$emit("eventName", data)
}
```

> 用于传递数据。
>
> 需要通过事件触发该方法，其中的`data`就是需要传递的数据。