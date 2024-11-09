---
title: 18.Vuex
category: Vue框架
tags:
  - framework
  - vue
  - vuex
date: 2022-11-18 00:00:00
---



> 官网：[What is Vuex? | Vuex (vuejs.org)](https://vuex.vuejs.org/)



## 理解

概念：专门在vue中实现集中式状态管理的一个Vue插件，对Vue应用中的多个组件的共享状态进行集中式管理（读/写），也是一种组件间通信方式，且适用任意组件间通信。



使用：

1. 多个组件依赖同一个状态（读）。
2. 来自不同组件的行为，改变同一个状态（写）。



原理：

> Vuex组成（都由store管理）：
>
> 	1. actions（可跳过）
> 	2. mutations
> 	3. state

![vuex](vuex.png){height="256px" width="600px"}

## 使用

> vue2使用vuex3，vue3使用vuex4。

### 安装

```bash
npm install vuex@3
```

### 导入

```js store/index.js
// 引入Vue，用于使用插件
import Vue from "vue";
// 引入Vuex，用于创建store
import Vuex from "vuex";

// 计算
const getters = {
  funcName(state){
    return 
	}
}

// 配置三大属性
// 数据
const state = {}
// 行为
const actions = {}
// 加工
const mutations = {}

// 使用插件
Vue.use(Vuex)

// 创建store实例并暴露
export default new Vuex.Store({
    actions, mutations, state, getters
})
```

> 使用插件必须在创建store之前执行。
>
> `getters`同`computed`，通过`$store.getters.funcName`获取。

### 配置

```js main.js
import Vue from 'vue'
import App from './App.vue'
import store from './store'

Vue.config.productionTip = false

const vm = new Vue({
  render: h => h(App),
  // 配置store
  store
}).$mount('#app')
```

> 配置store使用`store`配置项，这里使用了简写形式。

### 派遣（dispatch）

```js component.vue
this.$store.dispatch(disName, value)

// vue3
import {useStore} from 'vuex';


```

> 此步骤可跳过，直接`this.$store.commit("DISNAME", value)`。
>
> 使用`dispatch`派遣后，会在store中的`actions`调用`disName`方法。

### 行为提交（commit）

```js store/index.js
const actions = {
    disName(context, value) {
      ...
      context.commit("DISNAME", value)
    }
}
```

> context：上下文参数，携带其他需要的数据。
>
> `actions`执行`disName`后，需要使用`commit`提交到`mutations`中，并执行其中的`DISNAME`函数。
>
> 我们习惯性的将`commit`中的`disName`大写。
>
> 可继续使用`dispatch`套娃。

### 加工数据（mutations）

```js store/index.js
const mutations = {
    DISNAME(state, value){
        ...
    }
}
```

> `commit`后，程序会来到`DISNAME`函数，执行最终的数据处理。
>
> state只能在此处修改。
>
> 此处不处理任何的业务逻辑。



## map映射工具

> mapState：映射state作为computed属性。
>
> mapGetters：映射getters作为computed属性。
>
> 
>
> mapActions：映射actions作为methods方法，并调用`dispatch`联系actions。
>
> mapMutations：映射mutations方法作为methods方法，并调用`commit`联系mutations。

```js component.vue
import {mapState, mapGetters, mapActions, mapMutations} from 'vuex';

export default {
  name: "ComponentName",
  computed: {
    // 原写法
    comName(){
      return this.$store.state.stateName
    },
    // 方式一（对象）：计算属性名: 状态名
    ...mapState({comName: "stateName", ...}),
    // 方式二（数组）：计算属性名 = 状态名
  	...mapState(["stateName", ...])
  },
  methods: {
    // 原写法
    metName(value){
      this.$store.dispatch("actName", value)
    },
    // 方式一（对象）：方法名：行为名
    ...mapActions({metName: "actName", ...}),
    // 方式二（数组）：方法名 = 行为名
    ...mapActions(["actName", ...])
}
```



## 模块化

### 配置

组件模块配置

```js componentName.js
// 组件store配置
const componentOptions = {
  	// 启用命名空间
    namespaced: true,
    state: {},
    getters: {},
    actions: {},
    mutations: {},
}
```

vuex配置

```js store/index.js
// 引入Vue，用于导入插件
import Vue from "vue";
// 引入Vuex，用于创建store
import Vuex from "vuex";
// 引入组件store配置
import componentOptions from './componentName'

// 导入插件
Vue.use(Vuex)

// 创建store实例并暴露
export default new Vuex.Store({
  	// 使用模块
    modules:{
    		// 空间名（别名）：组件配置
        component1About: componentOptions,
      	...
    }
})
```

### 使用

```js component.vue
// 方式一：直接使用，除state，均由`/`隔开。
computed: {
  stateName: this.$store.state.componentAbout.stateName,
  getterName: this.$store.getters["componentAbout/getterName"]
},
methods: {
  actName(value){
    this.$store.dispatch("componentAbout/actName", value)
  },
  mutName(value){
  	this.$store.commit("componentAbout/mutName", value)
	},
}

// 方式二：map工具(命名空间, 属性)，需要开启命名空间。
computed: {
  ...mapState("componentAbout", ["stateName", ...]),
	...mapGetters("componentAbout", ["getterName", ...])
},
methods: {
  ...mapActions("componentAbout", ["actName", ...]),
  ...mapMutations("componentAbout", ["mutName", ...])
}
```

## 案例

1. 创建store

```js store/index.js
import {createStore} from "vuex";
import count from '@/store/count';

// 创建 store 实例并暴露
export default createStore({
    modules: {
        count
    }
})
```

2. 引入

```js main.js
import store from './store'

app.use(store)
```

3. 创建模块store

```js store/count.js
const state = {
    number: 0
};

const mutations = {
    increment(state) {
      state.number++;
    }
};

const actions = {

};

export default {
    namespaced: true,
    state,
    mutations,
    actions,
};
```

4. 组件使用

```html components/Count.vue
<template>
  <p>{{ number }}</p>
  <button @click="increment">点击增加</button>
</template>

<script>
  import {useStore} from 'vuex';
  import {computed} from "vue";

  setup() {
    // 获取store实例
    const store = useStore()
    // 使用commit调用mutations中的方法
    const increment = () => {
      store.commit("count/increment")
    }
    // 获取state中的属性
    const number = computed(() => store.state.count.number)
    return {
      number,
      increment
    }
  }
</script>
```

