---
title: 3.数据代理
category: Vue框架
tags:
  - framework
  - vue
  - agency
  - MVVM
date: 2022-11-03 00:00:00
---



## MVVM模型

* M（Model）：data数据。
* V（View）：渲染模板。
* VM（ViewModel）：Vue实例。

![MVVM](MVVM.png){height="256px" width="600px"}

> 因此，一般Vue对象的变量名取vm。



## 数据代理

通过一个对象代理另一个对象中属性的操作（读/写）。



### 原生js数据代理

```html
<script type="text/javascript">
    let number = 18
    let person = {
        "name": "Jack",
        "sex": "男",
        // "age": number
    }

    // 原生js数据代理对象
    Object.defineProperty(person, "age", {
        // 代理初始值，不能与getter/setter一起使用。
        value: number,
        // 代理属性值是否可以修改，默认false，不能与getter/setter一起使用。
        writable: true,

        // 代理属性是否可以枚举，默认false
        enumerable: true,
        // 代理属性是否可以删除，默认false
        configurable: true,

        // 读取代理属性时调用，并将返回值作为代理值
        get() {
            console.log("调用了getter");
            return number
        },

        // 修改代理属性时调用，并接收修改值作为实参
        set(value) {
            console.log("调用了setter");
            number = value
        }
    })

    console.log("person:", person)
    console.log("keys:", Object.keys(person));
</script>
```



### Vue中的数据代理

1. 传入的`data`会保存在`vm._data`属性中（即`vm._data.name` = `data.name`）。
1. 通过`vm`对象代理`_data`对象中属性的操作（即`vm.name` = `vm._data.name`），方便操作数据。
1. `vm._data`做了数据劫持（给属性添加getter和setter），并实现响应式渲染。



## Vue中的数据检测

1. 监视data中所有层级的数据。
2. 对象中的数据检测通过setter实现，且需要在Vue实例化时就传入data；对象中后追加的属性Vue默认不做响应式，如需给对象追加响应式属性时，可使用`set`API。
3. 数组中的数据检测通过封装原生数组方法实现，其原理就是在原方法上增加了响应式；如需响应式修改数组元素时，可使用`set`API或者使用已封装好的7个数组方法（push、pop、shift、unshift、splice、sort、reverse）。

> `set`API请浏览 [7.vue中的API](https://blog.absinthe.love/vue/576e1c4c/#settarget-key-val)。



