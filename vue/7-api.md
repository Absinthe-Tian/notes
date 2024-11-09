---
title: 7.Vue中的API
category: Vue框架
tags:
  - framework
  - vue
  - API
date: 2022-11-07 00:00:00
---



## 其他API

> 官方API：
>
> [API — Vue2.js (vuejs.org)](https://v2.cn.vuejs.org/v2/api/)
>
> [API — Vue3.js (vuejs.org)](https://cn.vuejs.org/api/)



### set(target, key, val)

追加一个响应式属性

```js
// Vue静态方法
Vue.set(obj, key|index, val)
// vue实例方法
vm.$set(obj, key|index, val)
```

**注意**：obj不能是Vue实例或Vue实例的根数据。

### delete(targey, key)

删除一个响应式属性

```js
// Vue静态方法
Vue.delete(obj, key|index)
// vue实例方法
vm.$delete(obj, key|index)
```

### use(vue, options)

启用插件，增强Vue。

1. 定义插件

```js plugins.js
export default {
    install(vue, options...) {

        // 全局过滤器
        vue.filter("myFilter", function (value) {
					...
        })

        //全局指令
        vue.directive('myDir', {
            // 指令与元素绑定时
            bind(element, binding) {
							...
            },
            // 指令所在元素被插入页面时
            inserted(element, binding) {
							...
            },
            // 指令所在模板被重新解析时
            update(element, binding) {
							...
            }
        })

        // 全局混入
        vue.mixin({
            methods: {
            	...
            }
        })

        // 添加Vue原型方法
        vue.prototype.methodName = () => {
					...
        }
    }
}
```

> 第一个参数vue，是Vue的构造函数，后面的参数为可选参数。

2. 使用插件

```js main.js
import plugins from "@/plugins";

Vue.use(plugins, options...)
```

### emit(eventName,  ...params)

触发自定义事件。

1. 绑定自定义事件

```js App.vue
// 方式一
<componentName @eventName="回调函数"/>

// 方式二
<componentName ref="cn"/>
...
this.$refs.cn.$on('eventName', 回调函数|箭头函数)
```

> 方式二可搭配`mounted`钩子，使用更加灵活。

2. 触发自定义事件

```js componentName.vue
this.$emit('eventName', ...params)
```

> 自定义事件在谁身上，谁来触发。
>
> 通过父组件提供回调函数，可实现子组件传递数据给父组件。

### off(eventName)

关闭自定义事件。

```js componentName.vue
this.$off(['eventName', ...])
```

> 字符串：关闭一个自定义事件。
>
> 数组：关闭多个自定义事件。
>
> 空：关闭所有自定义事件。

### nextTick(function)

下一次更新DOM结束后调用。

```js
this.$nextTick(function(){
  ...
})
```

> 可配合`ref`属性，达到自动获取焦点效果。
>
> 当改变数据后，基于更新后的新DOM进行某些操作时，需要在该函数指定的回调函数中执行。



