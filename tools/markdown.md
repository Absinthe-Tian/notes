---
title: Markdown语言
date: 2023-10-12 15:17:24
categories: 必备工具
tags:
  - Markdown
  - 轻量级标记语言
  - 知识点
quiz: true
---

Markdown 是一种轻量级标记语言，它允许人们使用易读易写的纯文本格式编写文档。

# 简单使用

## 标题

```markdown
一级标题
====

二级标题
----

# 一级标题
## 二级标题
### 三级标题
#### 四级标题
##### 五级标题
###### 六级标题
```

## 换行

```markdown
第一行&nbsp;&nbsp;
第二行

第一行
\n
第二行
```

## 字体

```markdown
*斜体*
_斜体_

**粗体**
__粗体__

***粗斜体***
___粗斜体___

~~删除线~~

<u>下划线<u>

<kbd>输入</kbd>
```

## 分隔线

```markdown
***

---
```

## 列表

```markdown
* 第一项
* 第二项

+ 第一项
+ 第二项

- 第一项
- 第二项

1. 第一项
2. 第二项
```

## 区块

```markdown
> 外层
> > 内层
> * 列表嵌套
```

## 代码

````markdown
`print("Hello World")`

```c
	print("Hello World")
```
````

## 脚注

```markdown
欢迎来到[Absinthe's Blog]

[Absinthe's Blog]: 苦艾酒的博客
```

## 链接

```markdown
点击[苦艾酒的博客](https://www.blog.absinthe.love)跳转至博客首页

博客首页地址为: <a herf="https://www.blog.absinthe.love">

博客首页[Absinthe's Blog][home]
[home]: https://www.blog.absinthe.love
```

## 图片

```markdown
![alt属性值](图片地址 "图片标题"){height="256px" width="416px"}

<img decoding="async" src="图片地址" width="50%" height="50%">
```

> 在小站中使用本地图片时，需要保存在同级同名目录下，并使用图片名即可。

## 表格

```markdown
| 表头1 | 表头2 | 表头3 | 
| :--- | :---: | ---: |
| 左对齐 | 居中 | 右对齐 |
| 单元格 | 单元格 | 单元格 |
```

## 转义

```markdown
\`   反引号
\*   星号
\_   下划线
\{}  花括号
\[]  方括号
\()  小括号
\#   井字号
\+   加号
\-   减号
\.   英文句点
\!   感叹号
```

# 小站使用

## link链接块

```markdown
{% raw %}{% links %}
- site: #站点名称
  owner: #管理员名字
  url: #站点网址
  desc: #简短描述
  image: #一张图片
  color: #颜色代码
{% endlinks %}{% endraw %}
```

> 使用yml文件，path 位于 `<root>/source`。

```markdown
{% raw %}{% linksfile [path] %}{% endraw %}
```

## code代码块

````markdown
```language title url url-title mark:line command("tip1":line || "tip2":line)
	
	# line: 1,3-7,10
	print("Hello World")
	
```
````

## quiz练习题

```markdown
---
quiz: true
---

## 单选题
1. 下列叙述正确的是 []{.gap} 。 {.quiz .choice}
	- 干扰项
	- 干扰项
	- 干扰项
	- 正确答案 {.correct}
	{.options}

## 多选题
2. 下列叙述正确的有。 {.quiz .multi}
	- 干扰项
	- 答案一 {.correct}
	- 干扰项
	- 答案二 {.correct}
	{.options}
     > - :x: 干扰项错误原因
     > - :heavy_check_mark: 答案正确原因
     > - :x: 干扰项错误原因
     > - :heavy_check_mark: 答案正确原因
    
## 判断题
3. 这是一个正确的语句。 {.quiz .true}
4. 这是一个错误的语句。 {.quiz .false}

## 填空题
5. 1+1= [2]{.gap} 。{.quiz .fill}
	> [3]{.mistake}
```

1. 下列叙述正确的是 []{.gap} 。 {.quiz}

   - 干扰项
   - 干扰项
   - 干扰项
   - 正确答案 {.correct}
     {.options}

2. 下列叙述正确的有。 {.quiz .multi}

   - 干扰项

   - 答案一 {.correct}

   - 干扰项

   - 答案二 {.correct}
     {.options}

     > - :x: 干扰项错误原因
     >
     > - :heavy_check_mark: 答案正确原因
     >
     > - :x: 干扰项错误原因
     >
     > - :heavy_check_mark: 答案正确原因
     >
     >   {.options}

3. 这是一个正确的语句。 {.quiz .true}

4. 这是一个错误的语句。 {.quiz .false}

5. 1+1= [2]{.gap} 。 {.quiz .fill}

   > [3]{.mistake}

## emoji绘文字

[所有标签参考](https://github.com/markdown-it/markdown-it-emoji/blob/master/lib/data/full.json)

```markdown
:cow: :beer:
```

:cow: :beer:

## effects文字特效

```markdown
文字带线
++下划线++
++波浪线++{.wavy}
++着重点++{.dot}
++紫色下划线++{.primary}
++绿色波浪线++{.wavy .success}
++黄色着重点++{.dot .warning}
~~删除线~~
~~红色删除线~~{.danger}

文字色彩
==荧光高亮==
[赤橙黄绿青蓝紫]{.rainbow}
[红色]{.red}
[粉色]{.pink}
[橙色]{.orange}
[黄色]{.yellow}
[绿色]{.green}
[靛青]{.aqua}
[蓝色]{.blue}
[紫色]{.purple}
[灰色]{.grey}

## 其他样式
快捷键 [Ctrl]{.kbd} + [C]{.kbd .red}
H~2~0
29^th^
```

1. 文字带线
   ++下划线++
   ++波浪线++{.wavy}
   ++着重点++{.dot}
   ++紫色下划线++{.primary}
   ++绿色波浪线++{.wavy .success}
   ++黄色着重点++{.dot .warning}
   ~~删除线~~
   ~~红色删除线~~{.danger}

2. 文字色彩
   ==荧光高亮==
   [赤橙黄绿青蓝紫]{.rainbow}
   [红色]{.red}
   [粉色]{.pink}
   [橙色]{.orange}
   [黄色]{.yellow}
   [绿色]{.green}
   [靛青]{.aqua}
   [蓝色]{.blue}
   [紫色]{.purple}
   [灰色]{.grey}

3. 其他样式
   快捷键 [Ctrl]{.kbd} + [C]{.kbd .red}
   H~2~0
   29^th^

## spoiler隐藏文字

```markdown
## 完全遮挡
!!被遮挡的内容!! : 鼠标滑过显示内容

## 模糊遮挡
!!被模糊的内容!!{.bulr}: 选中文字显示内容
```

!!被遮挡的内容!!： 鼠标滑过显示内容

!!被模糊的内容!!{.bulr} : 选中文字显示内容

## tab标签卡

```markdown
;;;id1 卡片1
	这里是卡片1的内容
;;;

;;;id1 卡片2
	这里是卡片2的内容
;;;

;;;id2 卡片3
	这里是卡片3的内容
;;;

;;;id2 卡片4
	这里是卡片4的内容
;;;
```

## collapse折叠块

```markdown
+++primary|info|success|warning|danger 标题文字
详细内容
+++
```

+++primary 标题文字
	详细内容

+++

## taskList代办事项

```markdown
- [ ] 这是一个小叉叉
- [x] 这是一个红色勾勾
```

- [ ] 这是一个小叉叉
- [x] 这是一个红色勾勾

## media多媒体

```markdown
## 音频
{% raw %}
{% media audio %}
- title: 列表1
  list:
    - 
    - 
- title: 列表2
  list:
    - 
    - 
{% endmedia %}
{% endraw %}

## 视频
{% raw %}{% media video %}
- name: 
  url: 
- name: 
  url: 
{% endmedia %}{% endraw %}
```

