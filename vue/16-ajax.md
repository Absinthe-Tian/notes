---
title: 16.Ajax请求
category: Vue框架
tags:
  - framework
  - vue
  - ajax
  - proxy
date: 2022-11-16 00:00:00
---

## 知识

发送Ajax请求的方式大致有如下几种：

1. XMLHttpRequest（内置原生）。
2. jQuery（xhr封装，体积大）。
3. **axios**（xhr封装，轻量级）。
4. fetch（内置原生，兼容性差，两层封装）。

解决cors跨域问题大致有如下几种：

1. 后端配置请求头（cors）。
2. jsonp（前后端都需要配置，只适用get请求）。
3. **代理**（中间商，如Nginx，vue-cli）。

## axios

> 官网：[Axios 中文文档 | Axios 中文网 | Axios 是一个基于 promise 的网络请求库，可以用于浏览器和 node.js (axios-http.cn)](https://www.axios-http.cn/)。

### 安装axios

```bash
npm install axios
```

### 发送

```js
getStudent() {
  // 发送get请求
  axios.get(requestUrl).then(
    response=>{
      console.log("请求成功", response.data)
    },
    error=>{
      console.log("请求失败", error.message)
    }
  )
}
```

### 配置代理

1. 方式一

```js
// 开启代理服务器
devServer: {
  proxy: serverUrl
}
```

> serverUrl是请求的服务器地址，自动代理为当前的8080端口，因此在发送请求时，可以将requestUrl修改为当前的8080地址。
> 
> 如果代理的8080地址存在请求的资源，则会获取8080的资源，而不是代理服务器的资源。
> 
> 配置的代理只能有一个。

2. 方式二

```js
devServer: {
  proxy: {
    // 资源前缀
    '/api': {
      // 代理服务器地址
      target: serverUrl,
      // 重写路径
      pathRewrite: {
        // 资源路径匹配正则: 替换值
        '^/api': ''
      },
      // 是否支持websocket
      ws: true,
      // 是否修改请求头host值
      changeOrigin: true
    },
    ...
  }
}
```

> 可配置多个代理。
> 
> 可区分代理的资源路径。

### 封装

待完善。

## vue-resource

vue的Ajax插件库。

### 安装

```bash
npm install vue-resource
```

### 使用

```js
import vueResource from 'vue-resource'

Vue.use(vueResource)
```

> 发送请求同axios。
> 
> 使用`this.$http.get`。
> 
> 存在维护问题。
