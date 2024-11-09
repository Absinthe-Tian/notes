---
title: 12.浏览器存储
category: Vue框架
tags:
  - framework
  - vue
  - webStorage
date: 2022-11-12 00:00:00
---



webStorage大小一般在5M左右（具体值依赖浏览器）。



## localStorage

数据保存在本地磁盘，关闭浏览器仍然存在。

1. 保存/修改数据

```js
window.localStorage.setItem("saveObject", JSON.stringify(Obj))
```

> 只能保存string。

2. 读取数据

```js
window.localStorage.getItem("saveObject")
```

3. 删除数据

```js
window.localStorage.removeItem("saveObject")
```

4. 清空数据

```js
window.localStorage.clear()
```



## sessionStorage

数据保存在内存，关闭浏览器即清空。

> API同上