---
title: 5.模板语法
category: Vue框架
tags:
  - framework
  - grammar
  - vue
date: 2022-11-05 00:00:00
---



## 插值语法

* 功能：用于解析容器的标签体内容（innerHTML），可以直接读取data的所有属性值进行渲染。

* 语法：`{{ 表达式|Vue属性|Vue方法 }}`。
* 示例：

```html
<head>
  <script src="https://cdn.jsdelivr.net/npm/vue@2/dist/vue.js"></script>
</head>

<body>
  <div id="root">
    <h1>Hello {% raw %}{{ title }}{% endraw %}</h1>
    <ul>
      <li>I'm {% raw %}{{ user.name }}{% endraw %}</li>
      <li>I'm {% raw %}{{ user.age }}{% endraw %} years old</li>
    </ul>
  </div>

  <script type="text/javascript">

    new Vue({
      el: '#root',
      data: {
        "title": "Vue",
        "user": {
          "name": "小明",
          "age": 20
        }
      }
    })
  </script>
</body>
```

**注意**：区分表达式与语句，表达式一定会有数据结果产生，语句则是一段执行代码，不一定有数据结果产生。



## 指令语法

* 功能：用于解析标签（如：属性、内容、事件等等）。
* 语法：`v-xxx`（如：v-bind、v-if、v-for、v-on等等。更多指令请参考[vue2官方文档](https://v2.cn.vuejs.org/v2/api/#%E6%8C%87%E4%BB%A4)、[vue3官方文档](https://cn.vuejs.org/api/built-in-directives.html)）。

> 自定义指令请浏览[4.Vue 中的属性](https://blog.absinthe.love/vue/e307e75c/#自定义指令)。

### v-bind

```html
<head>
  <script src="https://cdn.jsdelivr.net/npm/vue@2/dist/vue.js"></script>
</head>

<body>
    <div id="root">
        <!-- 将href属性的值与data中baidu属性的值单向绑定 -->
        <a v-bind:href="baidu">点击前往百度</a>
        <!-- v-bind:特有的简写形式 -->
        <a :href="google">点击前往谷歌</a>
    </div>

    <script type="text/javascript">
      
        new Vue({
            el: '#root',
            data: {
                "baidu": "https://www.baidu.com",
                "google": "https://www.google.com"
            }
        })
    </script>
</body>
```

### v-model

```html
<head>
  <script src="https://cdn.jsdelivr.net/npm/vue@2/dist/vue.js"></script>
</head>

<body>
    <div id="root">
        <!-- 单向绑定：只能从data中获取数据，不能修改数据 -->
        单向绑定：<input type="text" :value="name">
        <br>
        <!-- 双向绑定：既可以从data中获取数据，也可以实时更新数据 -->
        双向绑定：<input type="text" v-model:value="name">
      	<!-- 双向绑定简写形式，默认绑定value属性 -->
        双向简写：<input type="text" v-model="name">
    </div>

    <script type="text/javascript">
        new Vue({
            el: "#root",
            data: {
                "name": "苦艾酒的博客"
            }
        })
    </script>
</body>
```

**注意**：`v-model`只能用于表单类元素（输入类元素）。

> 表单修饰符
>
> 1. number：转数字。
> 2. lazy：懒（失去焦点时）收集数据。
> 3. trim：去空格（两边）。

### v-on

```html
<head>
    <script src="https://cdn.jsdelivr.net/npm/vue@2/dist/vue.js"></script>
</head>

<body>
<div id="root">
    欢迎来到{% raw %}{{ name }}{% endraw %}
    <br>
    <!-- 事件绑定的标准写法，且不带参数-->
    无参事件：
    <button v-on:click="sayHello">点击问好-1</button>
    <br>
    <!-- 事件绑定的简写形式，且携带参数，$event占位event属性 -->
    含参事件：
    <button @click="sayHi($event, '同学')">点击问好-2</button>
    <br>
    <!-- 事件修饰符 -->
    <a href="https://www.baidu.com" @click.prevent="goUrl">前往百度</a>
  	<!-- 按键事件及别名 -->
    <input type="text" placeholder="请输入" @keyup.enter="showKey">
</div>

<script type="text/javascript">
    new Vue({
        el: "#root",
        data: {
            "name": "苦艾酒的博客"
        },
        methods: {
            sayHello(event) {
                // this指向vm或组件对象，如果定义为箭头函数，this将指向window
                console.log(this)
                alert("Hello")
            },
            sayHi(event, nickname) {
                alert("Hi, " + nickname)
            },
            goUrl(){
                alert("链接失效")
            },
            showKey(event){
                // 可通过 event.key 获取键名
                // 可通过 event.keyCode 获取键码
                console.log(event.key, event.keyCode)
            }
        }
    })
</script>
</body>
```

> 事件修饰符
>
> 1. **prevent**：阻止默认事件。
> 2. **stop**：阻止事件冒泡。
> 3. **once**：只触发一次事件。
> 4. native：绑定原生事件（组件在使用事件时，默认为自定义事件）。
> 5. capture：在元素捕获时触发。
> 6. self：event.target == 当前元素时触发。
> 7. passive：不等待回调完毕就执行默认行为。
>
> 
>
> 当多层元素触发事件时，由外向内捕获，由内向外冒泡，先捕获再冒泡。
>
> 修饰符可以连写，如 `@click.stop.prevent=`，按顺序执行。



> 按键别名
>
> 1. enter：回车。
> 2. delete：删除、退格。
> 3. esc：退出。
> 4. space：空格。
> 5. tab：换行（配合keydown使用）。
> 6. up：上。
> 7. down：下。
> 8. left：左。
> 9. right：右。
>
> 
>
> 其他别名可使用 event.key 的值，多个单词之间使用 kebab-case(短横线命名)，如 caps-lock；也可以直接使用event.keycode的值（不推荐，不在维护该特性）
>
> 
>
> 系统修饰键（ctrl、alt、shift、meta）使用规则：
>
> keyup：系统键 + 其他键（up + down）时触发，可以连写，如`@keyup.ctrl.y=`。
>
> keydown：系统键即可触发
>
> 
>
> 自定义按键别名：
>
> Vue.config.keyCodes.按键别名=键码。

### v-show

```html
<head>
    <script src="https://cdn.jsdelivr.net/npm/vue@2/dist/vue.js"></script>
</head>

<body>
<div id="root">
		<h2 v-show="isShow">Welcome to {% raw %}{{ name }}{% endraw %}</h2>
</div>

<script type="text/javascript">
    new Vue({
        el: "#root",
        data: {
            "name": "苦艾酒的博客",
          	"isShow": true
        }
    })
</script>
</body>
```

> v-show的值可以是bool值，也可以是表达式。
>
> 其底层就是`display: none`，频繁显隐操作推荐。

### v-if、v-else-if、v-else

```html
<head>
    <script src="https://cdn.jsdelivr.net/npm/vue@2/dist/vue.js"></script>
</head>

<body>
<div id="root">
		<h2 v-if="isShow">Welcome to {% raw %}{{ name }}{% endraw %}</h2>
		<h2 v-else>Thanks for your read</h2>
</div>

<script type="text/javascript">
    new Vue({
        el: "#root",
        data: {
            "name": "苦艾酒的博客",
          	"isShow": true
        }
    })
</script>
</body>
```

> v-if、v-else-if的值可以是bool值，也可以是表达式。
>
> 三个指令语法与其他语言的if-else相同，结构不能被打断，可以与`<template>`配合使用实现整体显隐。
>
> 其底层就是增删元素。

### v-for

```html
<head>
    <script src="https://cdn.jsdelivr.net/npm/vue@2/dist/vue.js"></script>
</head>

<body>
<div id="root">
    <ul>
        <h2>（数组）</h2>
        <li v-for="(item,index) in testArr" :key="item.id">
            {% raw %}name: {{item.name}} &emsp; age: {{item.age}}{% endraw %}
        </li>
    </ul>

    <ul>
        <h2>（对象）</h2>
        <li v-for="(value,key) in testObj" :key="key">
            {% raw %}{{key}}: {{value}}{% endraw %}
        </li>
    </ul>

    <ul>
        <h2>（字符串）</h2>
        <li v-for="(value,index) in testStr" :key="index">
            {% raw %}{{index}}: {{value}}{% endraw %}
        </li>
    </ul>

    <ul>
        <h2>（整数）</h2>
        <li v-for="(value,index) in testInt" :key="index">
            {% raw %}{{index}}: {{value}}{% endraw %}
        </li>
    </ul>
</div>

<script type="text/javascript">
    const vm = new Vue({
        el: "#root",
        data: {
            testArr: [
                {id: "001", name: "张三", age: 30},
                {id: "002", name: "李四", age: 20},
                {id: "003", name: "王五", age: 10},
            ],
            testObj: {
                id: "0001",
                name: "法拉利",
                price: "100W"
            },
            testStr: "absinthe",
            testInt: 5
        }
    })
</script>
</body>
```

> 使用v-for遍历的类型有数组、对象、字符串、整数，使用频率依次递减。
>
> key的问题：
>
> 1. 使用index作为key时，如果在原来的顺序上，不是顺序操作时，可能会产生信息错乱，其原因在于虚拟DOM的对比算法使用相同key进行对比，发生了部分复用。
> 2. 使用index作为key时，如果原来的真实DOM与虚拟DOM的key不同，则会重新渲染该DOM，效率低下。
> 3. 省略key时，Vue默认使用index作为key。
> 4. 最好使用唯一ID作为key。

### v-text

```html
<head>
    <script src="https://cdn.jsdelivr.net/npm/vue@2/dist/vue.js"></script>
</head>

<body>
<div id="root">
	<h2>
    Welcome {% raw %}{{name}}{% endraw %}
  </h2>
  
  <h2 v-text="name">Welcome</h2>
</div>

<script type="text/javascript">
    new Vue({
        el: "#root",
        data: {
            "name": "<h3>苦艾酒的博客</h3>"
        }
    })
</script>
</body>
```

> `v-text`指令会替换innerText属性，没有插值语法灵活。并且不能解析标签，只会展示纯文本。

### v-html

```html
<head>
    <script src="https://cdn.jsdelivr.net/npm/vue@2/dist/vue.js"></script>
</head>

<body>
<div id="root">
	<h2>
    Welcome {% raw %}{{name}}{% endraw %}
  </h2>
  
  <h2 v-html="name">Welcome</h2>
</div>

<script type="text/javascript">
    new Vue({
        el: "#root",
        data: {
            "name": "<h3>苦艾酒的博客</h3>"
        }
    })
</script>
</body>
```

**注意**：在网站上动态上任意HTML是非常危险的，可能会发生XSS攻击。

> `v-html`指令会替换innerHTML，可以解析标签并展示。

### v-clock

```html
<head>
    <style>
        [v-clock]{
            display: none;
        }
    </style>
</head>
<body>
<div id="root">
    <h2 v-cloak>Welcome {% raw %}{{demo}}{% endraw %}</h2>
</div>

<script src="https://cdn.jsdelivr.net/npm/vue@2/dist/vue.js"></script>

<script type="text/javascript">
    const vm = new Vue({
        el: "#root",
        data: {
            demo: "<h3>苦艾酒的博客</h3>"
        }
    })
</script>
</body>
```

> `v-clock`是一个特殊的属性，Vue实例化并接管容器后，会立刻删除该属性。
>
> 可以配合CSS样式，解决网速慢导致资源加载时出现非正常页面的问题。

### v-once

```html
<head>
    <script src="https://cdn.jsdelivr.net/npm/vue@2/dist/vue.js"></script>
</head>

<div id="root">
    <h2 v-once>n的初始值是:{% raw %}{{n}}{% endraw %}</h2>
    <h2>n的当前值是:{% raw %}{{n}}{% endraw %}</h2>
    <button @click="n++">点击n++</button>
</div>

<script type="text/javascript">
    const vm = new Vue({
        el: "#root",
        data: {
            n: 1
        }
    })
</script>
</body>
```

> `v-once`指令在第一次动态渲染后，就视为静态渲染，以后数据的改变不会引起该元素的更新。

### v-pre

```html
<head>
    <script src="https://cdn.jsdelivr.net/npm/vue@2/dist/vue.js"></script>
</head>

<div id="root">
    <h2 v-pre>学习使我快乐</h2>
    <h2 v-pre>n的当前值是:{% raw %}{{n}}{% endraw %}</h2>
    <button v-pre @click="n++">点击n++</button>
</div>

<script type="text/javascript">
    const vm = new Vue({
        el: "#root",
        data: {
            n: 1
        }
    })
</script>
</body>
```

> `v-pre`指令会跳过该元素的编译过程，及呈现源码；可以使用在原标签（无插值语法、指令语法的标签）上加快编译。

