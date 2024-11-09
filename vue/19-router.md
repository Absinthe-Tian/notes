---
title: 19.Vue-Router
category: Vue框架
tags:
  - framework
  - vue
  - route
  - router
date: 2022-11-19 00:00:00
---



> 官网：[Vue Router | Vue.js 的官方路由 (vuejs.org)](https://router.vuejs.org/zh/)。



## 理解

### vue-router

vue的一个插件库，用于实现SPA（single page web application）应用。

### SPA

单页面web应用，整个应用只有一个完整页面，页面中的导航链接不会进行页面刷新及跳转，只会做局部更新，数据通过Ajax发送请求获取。

### 路由

一个路由即一组对应关系，key是路径，value可能是function（后端）或component（前端）。



## 使用

> vue2使用vue-router3，vue3使用vue-router4。

### 安装

```bash
npm install vue-router@3
```

### 配置

路由配置

```js router3.0/index.js
import VueRouter from "vue-router";
import Vue from "vue";
import ComponentName from "@/components/ComponentName";
import ChildComponent from "@/components/ChildComponent";

Vue.use(VueRouter)

 export default new VueRouter({
    routes:[
        {		
          	// 路由地址
            path: "/comPath",
          	// 路由组件
            component: ComponentName,
          	// 子路由
          	children:[
              {
                // 子路由地址
                path: "secPath",
                // 子路由组件
                component: ChildComponent
              }
            ]
        },
    ]
})
```

**注意**：子路由地址不要携带`/`。

vue3使用的vue-router4.0版本使用如下

```js router4.0/index.js
import {createRouter, createWebHashHistory} from "vue-router"


export default createRouter({
    history: createWebHashHistory(),
    routes: []
})
```

路由器配置

```js main.js
import Vue from 'vue'
import App from './App.vue'
import router from './router'

Vue.config.productionTip = false
new Vue({
    render: h => h(App),
  	// vue3不需要
    router: router
}).$mount('#app')
```

### 路由使用

导航路由

```html component.vue
<router-link class="" active-class="" to="/comPath" replace>content</router-link>
```

> 多级路由需要完整路由。
>
> active-class：激活时的样式类。
>
> to：切换的路由地址。
>
> replace：使用历史记录替换模式，默认push（追加）模式。

组件切换（占位）

```html component.vue
<router-view></router-view>
```

### 组件目录分类

一般组件：component/。

路由组件：pages/。

### 路由切换内部

1. 路由组件在切换过程中，默认是**销毁**和**挂载**，并不是隐藏。

2. 每个组件都有属于自己的`$route`属性，保存着该组件的路由信息。

3. 整个应用只有一个router，可以通过组件的`$router`获取。

### query参数

1. 传递参数

```html component.vue
<router-link :to="{
      path: '/comPath',
      query: {
      	key: value,
      	...
      }
  }">
  content
</router-link>
```

> 也可以使用模板字符串和`?`、`&`拼接路由，如`/comPath?key1=${value1}&key2=${value2}`。

2. 获取参数

```js ChildComponent.vue
$route.query.key
```

> 传递的query属性，会保存在组件实例对象中的`$route`里。

### name参数

可以简化过长的路由跳转。

1. 配置路由

```js router/index.js
routes:[
        {		
          	// 配置路由名
          	name: "routeName",
            path: "/comPath",
            component: ComponentName,
        },
]
```

> 使用name属性配置。

2. 使用

```html component.vue
<router-link :to="{
			name: "routeName"
      query: {
      	key: value,
      	...
      }
  }">
  content
</router-link>
```

> 使用name配置时，必须使用对象的形式，否者会被认为是字符串。

### param参数

1. 配置路由

```js router/index.js
routes:[
        {		
          	name: "routeName",
          	// `:`后携带的是param占位符
            path: "/comPath/:paramName",
            component: ComponentName,
        },
]
```

> 需要使用`:paramName`进行占位。

2. 使用

传递参数

```html component.vue
<router-link :to="{
			name: "routeName"
      params: {
      	paramName: value,
      	...
      }
  }">
  content
</router-link>
```

> 此处必须使用name属性指定路由，不允许使用path。
>
> 传递的时候，要对应配置中的`paramName`属性名。
>
> 也可以使用模板字符串和`/`拼接路由，如`/comPath/${paramValue}`。

接收参数

```js ChildComponent.vue
this.$route.params.paramName
```

> 传递的params属性，会保存在组件实例对象中的`$route`里。

### props参数

让路由组件更加方便的接收参数。

1. 配置路由

```js router/index.js
routes:[
        {		
          	name: "routeName",
            path: "/comPath/:paramName",
            component: ComponentName,
          	// 方式一：对象，将对象作为props传入该组件
          	props: {
              key:"value",
              ...
            },
            // 方式二：布尔值，将params参数作为props传入该组件
            props: true,
            // 方式三：函数，将函数返回值作为props传入该组件
            props($route){
              return {
                key: $route.query.key,
                ...
              }
            }
        },
]
```

2. 使用

传递参数：方式二，使用params参数传递，方式三，可使用query参数传递。

接收参数：该组件使用`props`属性声明接收的key即可。

### meta参数

用于存储自定义属性。

1. 定义

```js router/index.js
routes:[
        {		
          name: "routeName",
          path: "/comPath/:paramName",
          component: ComponentName,
          // 自定义属性
          meta:{
            // 一般用于权限校验
            isAuth: true,
            // 一般用于切换路由页签
            title: "titleName"
          },
        },
]
```

2. 使用

```js
// 前置守卫函数中
to.meta.isAuth
```



## 编程式路由

```js component.vue
// push模式切换路由
this.$router.push({
  name: 'routeName',
  query: {},
})
// replace模式切换路由
this.$router.replace({
  name: 'routeName',
  query: {},
})

// 后退
this.$router.back()
// 前进
this.$router.forward()
// 前往，正数前进几步，负数后退几步。
this.$router.go(N)
```



## 缓存路由组件

```html component.vue
<keep-alive include="componentName" :exclude="['componentName', ...]">
  <router-view></router-view>
</keep-alive>
```

> include：缓存的组件，一般缓存包含输入类的组件。
>
> exclude：不缓存的组件。
>
> 若不添加任何属性，则缓存所有组件。
>
> 缓存的组件在切换时不会被销毁。



## 特有生命周期

```js component.vue
// 激活
activated() {

},
// 失活
deactivated() {

}
```

> 路由切到该组件时，activated调用。
>
> 路由切走时，deactivated调用。



## 路由守卫

### 全局守卫

1. 前置路由守卫

路由切换之前调用。

一般用于权限校验。

```js router/index.js
const router = new VueRouter({...})
router.beforeEach((to, from, next) => {
    next()
})
```

> to：前往路由对象。
>
> from：来源路由对象。
>
> next：放行函数，在需要放行的位置调用即可。

2. 后置路由守卫

路由切换完成后调用。

一般用于修改应用页签。

```js router/index.js
const router = new VueRouter({...})
router.afterEach((to, from) => {
    document.title = to.meta.title || "appName"
})
```

### 独享守卫

切换到该组件之前调用。

```js router/index.js
routes:[
        {		
          name: "routeName",
          path: "/comPath/:paramName",
          component: ComponentName,
          // 独享路由守卫
          beforeEnter(to,from,next){
            next()
          }
        },
]
```

### 组件内路由守卫

1. 进入守卫

路由进入该组件时调用。

```js component.vue
beforeRouteEnter(to,from,next){
	next()
},
```

2. 离开守卫

路由离开该组件时调用。

```js component.vue
beforeRouteLeave(to, from, next){
	next()
}
```



## 路由的工作模式

1. hash模式

默认的工作模式，兼容性好。

```js router/index.js
new VueRouter({
  mode: 'hash',
  routes:[]
})
```

`/#/`称为哈希，即后面所接的路由称为哈希值，哈希值不会作为资源发送给服务器。

2. history模式

兼容性较差，刷新路由时会发送不存在的请求。

```js router/index.js
new VueRouter({
  mode: 'history',
  routes:[]
})
```

> 路由404问题需要后端解决。
