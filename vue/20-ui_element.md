---
title: 20.UI组件库
category: Vue框架
tags:
  - framework
  - vue
  - UI
  - library
date: 2022-11-20 00:00:00
---



## 移动端常用

1. Vant https://youzan.github.io/vant
2. Cube UI https://didi.github.io/cube-ui
3. Mint UI http://mint-ui.github.io



## PC端常用

1. Element UI https://element.eleme.cn
2. IView UI https://www.iviewui.com



## Element UI使用

### 安装

```bash
npm i element-ui
```

### 配置

```js babel.config.js
module.exports = {
  presets: [
    '@vue/cli-plugin-babel/preset',
    ["@babel/preset-env", {"modules": false}]
  ],
  "plugins": [
    [
      "component",
      {
        "libraryName": "element-ui",
        "styleLibraryName": "theme-chalk"
      }
    ]
  ]
}
```

### 按需引入

```js main.js
import {Button, ...} from 'element-ui';

Vue.use(Button)
...
```

> 组件名 = 组件标签名 减去 `el-`，然后使用大驼峰。

### 使用

```html component.vue
<el-button type="danger" icon="el-icon-delete" circle></el-button>
```



## Element-Plus

[一个 Vue 3 UI 框架 | Element Plus (element-plus.org)](https://element-plus.org/zh-CN/)。

使用与Element UI一样，按需引入不同。

### 安装

```bash
npm install element-plus
```

### 按需引入

1. 安装插件

```bash
npm install -D unplugin-vue-components unplugin-auto-import
```

2. 配置

webpack配置

```js vue.config.js
// webpack.config.js
const AutoImport = require('unplugin-auto-import/webpack')
const Components = require('unplugin-vue-components/webpack')
const { ElementPlusResolver } = require('unplugin-vue-components/resolvers')

module.exports = {
  // ...
  configureWebpack: {
    plugins: [
      AutoImport({
        resolvers: [ElementPlusResolver()],
      }),
      Components({
        resolvers: [ElementPlusResolver()],
      }),
    ],
  }
}
```

vite配置

```js vite.config.js
// vite.config.ts
import { defineConfig } from 'vite'
import AutoImport from 'unplugin-auto-import/vite'
import Components from 'unplugin-vue-components/vite'
import { ElementPlusResolver } from 'unplugin-vue-components/resolvers'

export default defineConfig({
  // ...
  plugins: [
    // ...
    AutoImport({
      resolvers: [ElementPlusResolver()],
    }),
    Components({
      resolvers: [ElementPlusResolver()],
    }),
  ],
})
```

> 两者可根据自己使用的框架进行选择。

