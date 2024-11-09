---
title: 4.Vue中的属性
category: Vue框架
tags:
  - framework
  - vue
  - property
date: 2022-11-04 00:00:00
---



## 属性预览

1. `el`：用于挂载元素，也可用`mount`方法挂载。
1. `data`：用于保存数据，可以是对象，也可以是函数，函数需要返回值。
1. `methods`：用于保存实例方法，默认参数为event。
1. `computed`：用于保存计算属性，至少需要实现getter。
1. `watch`：用于监视属性，data和computed的属性都可以。
1. `filters`：用于过滤数据，可以在插值语法中使用。
1. `directives`：用于自定义指令，不由Vue管理。
1. `template`：用于替换模版，仅能有一个根元素，脚手架环境默认使用运行版Vue，无模板解析器不可用。
1. `render`：用于创建渲染元素。

>由Vue管理的函数，this指向vm，箭头函数则向外寻找指向window。
>
>组件的this指向组件实例对象（VueComponent、vc）。



## 挂载

1. el属性进行挂载

```html
<body>
  <div id="root"></div>

  <script type="text/javascript">
      const vm = new Vue({
          el:"#root"
      })
  </script>  
</body>
```

2. mount方法挂载

```html
<body>
  <div id="root"></div>

  <script type="text/javascript">
      const vm = new Vue()
      vm.$mount("#root")
  </script>  
</body>
```



## 数据

1. data属性数据

```html
<body>
  <div id="root"></div>

  <script type="text/javascript">
      const vm = new Vue({
        data: {
          key: value
        }
      })
      vm.$mount("#root")
  </script>  
</body>
```

> 该方式会有对象引入问题，导致数据混乱。
>
> 定义组件时，会报错。

2. data函数形式保存数据

```html
<body>
  <div id="root"></div>

  <script type="text/javascript">
      const vm = new Vue({
        data(){
          return {
            key: value
          }
        }
      })
      vm.$mount("#root")
  </script>  
</body>
```



## 方法

1. 无参方法

```html
<body>
  <div id="root"></div>

  <script type="text/javascript">
      const vm = new Vue({
        methods: {
          // 默认第一个参数为event表示当前元素，无需传参
          funcName(event){
            ...
          }
        }
      })
      vm.$mount("#root")
  </script>  
</body>
```

2. 含参方法

```html
<body>
  <div id="root"></div>

  <script type="text/javascript">
      const vm = new Vue({
        methods: {
          // 可使用$event实参占位，及代表event参数
          funcName(event, param1, ...){
            ...
          }
        }
      })
      vm.$mount("#root")
  </script>  
</body>
```



## 计算属性

1. computed属性完整用法

```html
<body>
  <div id="root"></div>

  <script type="text/javascript">
      const vm = new Vue({
        computed: {
          // 属性名
          propName: {
            // 读取该属性时调用，属性值=返回值
            get(){
              ...
              return
            },
            // 更新该属性时调用，实参=修改值
            set(value){
              ...
            }
          }
        }
      })
      vm.$mount("#root")
  </script>  
</body>
```

>计算属性在读取时，会采用缓存机制，只在第一次读取时调用getter，同时会将用到的数据进行实时更新。

2. 简写形式

```html
<body>
  <div id="root"></div>

  <script type="text/javascript">
      const vm = new Vue({
        computed: {
          // 属性名=函数名、属性值=返回值
          propName(){
            ...
            return
          }
      })
      vm.$mount("#root")
  </script>  
</body>
```

>简写形式只实现了getter。



## 属性监视

1. watch属性完整用法

```html
<body>
  <div id="root"></div>

  <script type="text/javascript">
      const vm = new Vue({
        watch: {
          propName: {
            // 是否初始化时调用，默认false
            immediate: true,
            // 是否开启深度监视，默认false，用于多层数据
            deep: true,
            // 监听代理，默认包含两个参数
            handler(newValue, oldValue){
              ...
            }
          }
      })
      vm.$mount("#root")
  </script>  
</body>
```

2. 简写形式

```html
<body>
  <div id="root"></div>

  <script type="text/javascript">
      const vm = new Vue({
        watch: {
          // 属性名=函数名
          propName(newValue, oldValue){
            ...
          }
      })
      vm.$mount("#root")
  </script>  
</body>
```

>简写形式仅实现handler。

3. watch方法完整用法

```html
<body>
  <div id="root"></div>

  <script type="text/javascript">
      const vm = new Vue()
      vm.$watch("proName", {
        immediate: true,
        deep: true,
				handler(newValue, oldValue){
        	...
      	}
      })
      vm.$mount("#root")
  </script>  
</body>
```

4. 简写形式

```html
<body>
  <div id="root"></div>

  <script type="text/javascript">
      const vm = new Vue()
      vm.$watch("proName", function(newValue, oldValue){
        	...
      	})
      vm.$mount("#root")
  </script>  
</body>
```

> 当computed和watch都能实现一个功能时，watch可以使用异步任务，而computed只能通过返回值修改数据。



## 过滤器

1. filters属性局部过滤器

```html
<body>
  <div id="root" :x="value | filterName1 | filterName2(p1, ...)">
    {% raw %}{{ value | filterName1 | filterName2(p1, ...) }}{% endraw %}
  </div>

  <script type="text/javascript">
      const vm = new Vue({
        filters: {
          filterName1(value){
            ...
          },
          filterName2(value, param1, ...){
          	...
          },
      })
      vm.$mount("#root")
  </script>  
</body>
```

**注意**：局部过滤器只能在该Vue实例对象上使用，不能被其他Vue实例对象使用。

> 1. 过滤器可以在插值语法中使用，也可以在`v-bind`动态属性绑定中使用。
> 2. 第一个参数默认为`|`（管道符）前的值。
> 3. 可以多个过滤器依次串联使用。

2. filter方法全局过滤器

```js
Vue.filter("filterName", function(value, param1, ...){
  ...
})
```

**注意**：全局过滤器需要在Vue实例化之前就注册好。



## 自定义指令

1. directives属性局部指令

```html
<body>
  <div id="root"></div>

  <script type="text/javascript">
      const vm = new Vue({
        directives: {
          direName: {
            // 指令与元素绑定时
            bind(element, binding){
              ...
            },
            // 指令所在元素被插入页面时
            inserted(element, binding){
              ...
            },
           	// 指令所在模板被重新解析时
            update(element, binding){
              ...
            }
          }
      })
      vm.$mount("#root")
  </script>  
</body>
```

> 1. element：使用该指令的元素。
> 2. binding：指令的绑定属性。
>
> 指令名多个单词可以使用kebab-case，如 v-else-if，定义时需要使用引号包裹。

2. 简写形式（局部）

```html
<body>
  <div id="root"></div>

  <script type="text/javascript">
      const vm = new Vue({
        directives: {
          // 指令名=v-函数名
          direName(element, binding){
            ...
          }
      })
      vm.$mount("#root")
  </script>  
</body>
```

> 简写形式相当于只实现了bind和update。
>

3. directive方法全局指令

```js
// 完整形式
Vue.directive(direName, {
  // 指令与元素绑定时
  bind(element, binding){
    ...
  },
  // 指令所在元素被插入页面时
  inserted(element, binding){
    ...
  },
  // 指令所在模板被重新解析时
  update(element, binding){
    ...
  }
})

// 简写形式
Vue.directive(direName, function(element, binding){
  ...
})
```



## 渲染元素

```js main.js
new Vue({
  // 完整形式
  render(createElement) {
    return createElement(标签名, 标签内容)
  }
  
  // 箭头函数的简写形式
  render: createElement => createElement(标签名, 标签内容)
})
```

> `createElement`也可以使用其他简写，如`h`。



