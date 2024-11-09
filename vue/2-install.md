---
title: 2.Vue的引用及安装
category: Vue框架
tags:
  - framework
  - install
  - vue
date: 2022-11-02 00:00:00
---



本节将会讲解两种Vue的安装方式，一种用于入门学习使用，一种用于后期提升开发效率使用。



## Vue.js的引入

在HTML文件中引入Vue.js是最快捷最简单的方法，将以下代码直接粘贴到HTML文件中即可。

Vue.js包含两个版本，学习的小伙伴请务必选择开发版本。

* [开发环境版本](https://v2.cn.vuejs.org/js/vue.js)，包含错误提示及警告，具备调试。

```html
<script src="https://cdn.jsdelivr.net/npm/vue@2/dist/vue.js"></script>
```

* [生产环境版本](https://v2.cn.vuejs.org/js/vue.min.js)，优化尺寸和速度，但不会提供错误提示及调试。

```html
<script src="https://cdn.jsdelivr.net/npm/vue@2"></script>
```

以上的引入方式为网络资源，小伙伴们也可以点击链接另存到本地。



## Vue脚手架

在实际项目中，一般不会使用js引入点方式进行开发，而是使用搭建更快速的脚手架。在使用脚手架之前，需要小伙伴们提前安装[Node.js (nodejs.org)](https://nodejs.org/en/)，具体安装方法很简单，这里就不做过多介绍。

1. npm换源

```bash
npm config set registry https://registry.npm.taobao.org
```

2. 安装脚手架

```bash
npm install -g @vue/cli
```

3. 创建项目

```bash
vue create projectName
```

4. 启动项目

```bash
npm run serve
```

5. 打包项目

```bash
npm run build
```



## Vue Devtools插件

[Vue Devtools插件](https://devtools.vuejs.org/guide/installation.html)是Vue的开发工具，用于Vue的调试和测试，仅支持chrome内核的浏览器，如Chrome浏览器、Firefox浏览器、Edge浏览器（这里需要外网下载，也可以[点击这里](https://pan.baidu.com/s/1ZJYbb6XrYsdCntYK6ddwBA?pwd=1234)网盘下载，提取码: 1234）。

解压安装方法：打开Chrome浏览器 -> 找到扩展程序 -> 开启开发者模式 -> 点击加载已解压的扩展程序 -> 选择下载好的文件夹（里面带数字版本的目录那一层） -> 添加即可

crx文件安装方法：打开Chrome浏览器 -> 找到扩展程序 -> 将crx文件拖拽至浏览器 -> 添加即可



## 第一个程序

```html
<head>
  <script src="https://cdn.jsdelivr.net/npm/vue@2/dist/vue.js"></script>
</head>

<body>
  <div id="root">
    <h1>Hello {% raw %}{{ name }}{% endraw %}</h1>
    <p>欢迎{% raw %}{{ nickname }}{% endraw %}前来学习！</p>
  </div>

   <script type="text/javascript">
      // Vue.config是全局配置对象，productionTip属性即是否阻止vue在启动时生成提示，默认为true
      Vue.config.productionTip = false

      new Vue({
        // el：用于指定Vue实例挂载的容器，且实例与容器一一对应，值通常为css选择器字符串，也可以是DOM对象
        el: '#root',
        // data：用于给挂载容器提供数据
        data: {
          "name": "Vue",
          "nickname": "小伙伴们"
        }
      })
    </script>
</body>
```
