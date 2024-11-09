---
title: 10.脚手架
category: Vue框架
tags:
  - framework
  - vue
  - cli
date: 2022-11-10 00:00:00
---

## 结构

* node_modules：脚手架的依赖包。
* public：公用文件文件夹，静态文件夹（图片、音频、视频，css），webpack会将其原封不动的打包到dist文件夹里。
  * favicon.ico：页签图标。
  * index.html：应用主页面。
* src：源文件文件夹。
  * assets：静态资源文件夹，webpack会将其打包成js模块。
    * logo.png：应用logo。
  * component：组件文件夹。
    * HelloWorld.vue：组件文件。
  * App.vue：应用总组件。
  * main.js：入口文件，vm所在文件。
* .gitignore：git忽略配置文件。
* babel.config.js：babel配置文件。
* jsconfig.json：TypeScript 的配置文件。
* package.json：依赖包配置文件。
* package-lock.json：依赖包版本管理文件。
* README.md：应用描述文件。
* vue.config.js：脚手架配置，[配置参考 | Vue CLI (vuejs.org)](https://cli.vuejs.org/zh/config/#vue-config-js)。

## 组成

==Vue = Vue核心 + 模板解析器。==

脚手架默认使用运行版（runtime）Vue，剔除了模板解析器，因此在使用`template`属性时会报错，使用`render`替代即可。

## 属性

### ref

给标签打标识，以获取该标签。

1. 打标识。

```js
<School ref="sc"/>
```

2. 获取标识

```js
this.$refs.sc
```

> 如果是已有的HTML标签打标识，获取的则是DOM元素。
> 
> 被用来给元素或子组件注册引用信息（id的替代者）。

### props

数据传递，父子组件传递数据。

1. 传递

```js
// 传递数据到子组件
<School key1="value" :key2="value" .../>
```

> 传递其他类型时，可使用v-bind，引号内为js表达式，不再是字符串。

2. 接收

```js
// 接收数据到该组件
export default {
    ...
    // 简单接收
    props: ['key1', ...]

    // 类型限制接收
    props: {
        key1: String,
        ...
    }

    // 详细限制接收
    props: {
        key1: {
            type: String,
            required: false,
            default: ''
        },
        ...
    }
}
```

> 接收的props数据不可修改。
> 
> 优先级：props > data
> 
> 父组件传递回调函数给子组件，可实现子组件传递数据给父组件。

### mixin

相同属性混入，所有配置项都可以混入。

1. 定义mixin

```js
export const mixinName = {
    methods:{
        showName(){
            alert(this.name)
        }
    },
    mounted() {
        console.log("组件挂载完毕！")
    },
      ...
}
```

2. 引入mixin

局部引入

```js
import {mixinName} from "@/mixin"

export default {
    ...
    mixins: [mixin]
}
```

全局引入

```js
import {mixinName} from "@/mixin"

Vue.mixin(mixinName)
```

> 引入mixin的组件可直接使用。
> 
> 优先级：data > mixin
> 
> 生命周期同级，但后调用。
