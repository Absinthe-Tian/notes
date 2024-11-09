---
title: 6.样式绑定
category: Vue框架
tags:
  - framework
  - vue
  - style
date: 2022-11-06 00:00:00
---



## 绑定class样式

使用v-bind绑定class时，会追加类名。

### 字符串写法

> 适用于类名不确定时使用

定义样式

```css
.className {
	...
}
```

定义属性

```js
data: {
  styleName: className
}
```

动态引用属性

```html
<div class="basic" :class="styleName"></div>
```

### 对象写法

> 适用于样式类名及个数确定，但是需要动态选择时使用。

定义样式

```css
.className1 {
  ...
}

.className2 {
  ...
}
```

定义属性

```js
data: {
  styleObj: {
    className1: true,
    className2: false
  }
}
```

动态引用属性

```html
<div class="basic" :class="styleObj"></div>
```

### 数组写法

> 适用于样式类名及个数不确定时使用。

定义样式

```css
.className1 {
  ...
}

.className2 {
  ...
}
```

定义属性

```js
data: {
  styleArr: ['className1', 'className2']
}
```

动态引用属性

```html
<div class="basic" :class="styleArr"></div>
```



## 绑定style样式

### 对象写法

定义属性

```js
data: {
  styleObj: {
    ...
  }
}
```

**注意**：此处样式对象的key必须是小驼峰命名法，如`backgroundColor: "red"`。

动态引用

```html
<div class="basic" :style="styleObj"></div>
```

### 数组写法

定义属性

```js
data: {
  styleArr: [styleObj1, styleObj2]
}
```

**注意**：此处的styleObj是样式对象。

动态引用

```html
<div class="basic" :style="styleArr"></div>
```



## Less使用

### 安装

```bash
npm install -D less less-loader
```

### 使用

```html
<style lang="less">
@width: 400px;
@height: 200px;

.content {
    width: @width;
    height: @height;
    background-color: yellowgreen;
}
</style>
```

