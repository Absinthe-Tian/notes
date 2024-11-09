---
title: 14.消息订阅与发布
category: Vue框架
tags:
  - framework
  - vue
  - subscribe
  - communication
date: 2022-11-14 00:00:00
---



消息订阅有许多第三方库，这里我们使用`pubsub-js`。



## 订阅

消息的接收者

```js component1.vue
import pubsub from 'pubsub-js'

// 订阅消息
mounted() {
  this.msg_id = pubsub.subscribe("msgName", (msgName, data)=>{
    ...
  })
},
// 取消订阅
beforeDestroy() {
    pubsub.unsubscribe(this.msg_id)
}
```

> 回调函数也可以是`methods`属性的方法。



## 发布

消息的发送者

```js component2.vue
import pubsub from 'pubsub-js'

methods:{
    funcName(){
      pubsub.publish("msgName", data)
}
```

> 需要触发事件。

