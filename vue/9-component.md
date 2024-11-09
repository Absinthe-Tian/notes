---
title: 9.组件化编程
category: Vue框架
tags:
  - framework
  - vue
  - component
date: 2022-11-09 00:00:00
---



## 相关概念

传统编码缺陷：

1. 依赖关系混乱，不好维护。
2. 代码复用率低。

### 模块

模块：向外提供特定功能的js程序，一般指js文件。

作用：复用js，简化js的编写，提高js运行效率。

模块化：应用中的js都以模块的方式来编写，则该应用就称是一个模块化的应用。

### 组件

组件：实现**局部**功能**代码**和**资源**的集合，包含该功能的所有代码文件和资源文件。

作用：复用编码，简化项目编码，提高运行效率。

组件化：应用中的功能都以组件的方式来编写，则称该应用是一个组件化的应用。

非单文件组件：一个文件包含多个组件。

单文件组件：一个文件只包含一个组件。



## 非单文件组件

### 定义组件

```js
const school = Vue.extend({
  			// 指定Vue开发者工具中的组件名，不影响使用
  			name: "my-shcool",
        data(){
            return {
                schoolName: "荆州学院",
                address:"湖北省荆州市"
            }
        },
        template: `
            <div>
                <h2>{% raw %}{{schoolName}}{% endraw %}</h2>
                <h2>{% raw %}{{address}}{% endraw %}</h2>
            </div>
        `
})
```

> 使用`Vue.extend`API进行组件的定义，也可以直接简写成配置项，Vue也会自己调用该API。	
>
> 组件的属性与vm属性几乎一样。
>
> 不能有`el`属性，因为组件不固定于某个节点，统一由vm管理。
>
> `data`属性必须写成函数式，因为组件复用时，数据存在引用关系。
>
> 组件本质就是一个名为VueComponent的构造函数，由extend生成。

### 注册组件

```js
vm = new Vue({
        el: "#root",
        data: {
            demo: "非单文件组件"
        },
  			// 局部组件，组件名: 拓展组件
        components: {
            School: school
        }
})
```

```js
// 全局组件
Vue.component(组件名, 拓展组件)
```

> 使用`components`属性注册局部组件，局部组件只能在该vm内部使用。
>
> 组件名：kebab-case（小写字母，多个单词之间使用`-`)、CamelCase（所有单词首字母大写）。后者需要脚手架环境。避免与已有标签名同名。

### 引用组件

```html
<div id="root">
    {% raw %}{{ demo }}{% endraw %}
    <hr>
    <School></School>
</div>
```

> 引用组件时，直接使用带有组件名的标签即可。
>
> 使用自闭和标签组件时，需要脚手架环境。
>
> 本质：组件的实例对象，由Vue执行的`new VueComponent(options)`，简称vc。
>
> `VueComponent.prototype.__proto__ === Vue.prototype`，所以vc可以使用Vue的原型对象上的属性。



## 单文件组件

### 定义组件

```js SchoolComponent.vue
<template>
<!-- 组件的结构  -->
  <div class="demo">
    <h2>学校: {% raw %}{{schoolName}}{% endraw %}</h2>
    <h2>地址: {% raw %}{{address}}{% endraw %}</h2>
    <button @click="strike">点击暴击</button>
  </div>
</template>

<style lang="css" scoped>
/* 组件的样式 */
  .demo{
    background-color: skyblue;
  }
</style>

<script>
// 组件的交互
  export default {
    name:"School",
    data(){
      return {
        schoolName: "荆州学院",
        address:"湖北省荆州市"
      }
    },
    methods:{
      strike(){
        alert(`您已成功暴击[${this.schoolName}]`)
      }
    }
  }
</script>
```

> 一个vue文件一般就是一个组件。
>
> vue文件不执行创建vm，且浏览器不识别，需要脚手架环境。
>
> style中scoped会随机生成一个属性，并使用属性选择器进行局部美化样式，解决命名冲突。

### 定义App

```js App.vue
<template>
  <div id="app">
    <School></School>
    ...
  </div>
</template>

<script>
  import School from "./School";

  export default {
    name:"App",
    components: {
      School
    }
  }
</script>
```

> 一般一个应用都应该有一个App组件，向上与vm交互，向下管理其他组件。

### 创建vm

```js main.js
import App from "./App";

new Vue({
    el:"#root",
    template:`<App></App>`,
    components:{App},
})
```

### 构建容器

```html index.html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>单文件组件</title>
</head>
<body>
  <div id="root"></div>
  
  <script src="https://cdn.jsdelivr.net/npm/vue@2/dist/vue.js"></script>
  <script src="main.js"></script>
</body>
</html>
```

> 因浏览器无法识别vue文件，该案例需要脚手架环境，搭建脚手架环境请参考[2.Vue的引用及安装](https://blog.absinthe.love/vue/3333d32c/#vue%E8%84%9A%E6%89%8B%E6%9E%B6)。

