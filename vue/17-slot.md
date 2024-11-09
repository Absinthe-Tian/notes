---
title: 17.插槽
category: Vue框架
tags:
  - framework
  - vue
  - slot
date: 2022-11-17 00:00:00
---



让父组件向子组件的指定位置插入HTML结构。



## 默认插槽

### 定义插槽

```html component.vue
<template>
  <div>
		...
    <slot>默认插槽的默认值</slot>
    ...
  </div>
</template>
```

> 使用`<slot>`标签可定义插槽，表示该区域是这个组件的标签体内容。
>
> 当没有传递标签体内容时，会展示默认值。

### 使用插槽

```html App.vue
<ComponentName>
  标签体内容
</ComponentName>
```

> 使用插槽时，组件使用双标签包裹标签体内容，该内容会在`<slot>`位置展示。



## 具名插槽

### 定义插槽

```html component.vue
<template>
  <div>
		...
    <slot name="slotName1">具名插槽的默认值1</slot>
    ...
    <slot name="slotName2">具名插槽的默认值2</slot>
    ...
  </div>
</template>
```

> 通过name属性指定插槽名。

### 使用插槽

```html App.vue
<ComponentName>
  <h2 slot="slotName1">
    ...
  </h2>
  
  <template v-slot:slotName2>
    ...
  </template>
</ComponentName>
```

> 使用时，通过slot属性来指定使用哪个插槽。
>
> 多个结构使用同一个插槽时，会追加结构。
>
> 使用`<template>`标签时，可以使用`v-slot:slotName`来指定插槽。
>
> 在Vue3中，`v-slot:slotName`简写`#slotName`。



## 作用域插槽

### 定义插槽

```html component.vue
<template>
  <div>
		...
    <slot :pkey="pvalue" name="slotName">作用域插槽</slot>
    ...
  </div>
</template>
```

> 可以给`<slot>`传递数据，方式同props。

### 使用插槽

```html App.vue
<ComponentName>
  <template slot-scope="scopeName" v-slot:slotName>
    {{scopeName.pkey}}
    ...
  </template>
</ComponentName>
```

> 使用时，必须使用`<template>`。
>
> 定义插槽时传递的数据，可以通过`slot-scope`属性接收为一个对象。
>
> 场景：数据在组件自身，但结构由使用者决定。

