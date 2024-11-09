---
title: 15.过渡与动画
category: Vue框架
tags:
  - framework
  - vue
  - animation
  - transition
  - css
date: 2022-11-15 00:00:00
---



在插入、更新或移除DOM时，在合适的时候给元素添加样式类名。

## 定义

```css
/* 使用动画 */
@keyframes myAnimation {
  from{
    opacity: 0;
  }
  to {
    opacity: 1;
  }
}

.v-enter-active{
  animation: myAnimation 1s linear;
}

.v-leave-active{
  animation: myAnimation 1s linear reverse;
}

/* 使用过度 */
.v-enter, .v-leave-to {
  opacity: 0;
}

.v-enter-active, .v-leave-active {
  transition: 1s linear;
}

.v-enter-to, .v-leave {
  opacity: 1;
}
```

> `v-enter`：入场起点（vue3中重命名为`v-enter-from`）。
>
> `v-enter-active`：入场中。
>
> `v-enter-to`：入场终点。
>
> `v-leave`反之。
>
> 若使用动画时指定`name`属性，需要将类名选择器中的`v`替换为`name`值。如`demo-leave-active`.

## 使用

```html
<!--单元素动画-->
<transition appear>
  <h1 v-show="isSHow">Welcome to knight</h1>
</transition>

<!--多元素动画，需要指定key值-->
<transition-group appear>
  <h1 key="1" v-show="!isSHow">Welcome to knight</h1>
  <h1 key="2" v-show="isSHow">Welcome to knight</h1>
</transition-group>
```

>`appear`属性指定后，第一次也会携带入场动画。
>
>可指定`name`属性。如`demo`。



## 第三方动画库

[Animate.css | A cross-browser library of CSS animations.](https://animate.style/)

1. 安装

```bash
npm install animate.css
```

2. 导入

```js
import 'animate.css'
```

3. 使用

```html
<transition
            appear
            name="animate__animated animate__bounce"
            enter-active-class="animate__rubberBand"
            leave-active-class="animate__flash"
            >
  <h1 class="test" v-show="isSHow">Welcome to knight</h1>
</transition>
```

> `name`需要指定该值。
>
> `enter-active-class`指定进场动画class。
>
> `leave-active-class`指定退场动画class。

