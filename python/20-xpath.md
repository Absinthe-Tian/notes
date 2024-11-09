---
title: 20.XPath与Bs4
date: 2022-10-20 00:00:00
categories: Python语言
tags: 
  - HTML数据清洗
  - XPath
  - BeautifulSoup
---

# XPath

XPath 是一门在 XML 文档中查找信息的语言，通常用来匹配一个HTML节点。

## 节点

XML和HTML都是有标签构成，具有很强的层级关系，基于此关系XPath语法可以选择出我们想要的数据。

* 父：直接包含该节点的节点，称为该节点的父节点。
* 子：该节点直接包含的节点，称为该节点的子节点。
* 同胞：与该节点在同一个父节点下的节点，都称为该节点的同胞节点/兄弟节点。
* 先辈：直接或间接的包含该节点的节点，都称为该节点的先辈节点。
* 后代：该节点直接或间接的包含的节点，都称为该节点的后代节点。

## 语法

### 节点选取

XPath 使用路径表达式在 XML/HTML 文档中选取节点。

| 表达式   | 描述                            | 使用                | 说明                            |
| :------: | :-----------------------------: | :-----------------: | :-----------------------------: |
| nodeName | 选取此节点的所有节点          | div                 | 选取div的所有节点           |
| //       | 选取全局的节点（相对路径）    | //body              | 选取所有的body节点              |
| /        | 选取某个节点下的子节点（绝对路径）    | //head/title      | 选取head下的title节点           |
| .        | 选取当前节点 | ./div               | 选取当前节点下的div节点         |
| .. | 选取当前节点的父节点 | //div/.. | 选取div的父节点 |
| @        | 选取当前节点的指定属性         | //div/@prop | 选取div节点的prop属性值 |
| text() | 选取当前节点的textContent值 | //a/text() | 选取a节点的文本内容 |

### 谓语

谓语用来查找某个特定的节点或者包含某个指定的值的节点。

|          表达式          |              描述              |
| :----------------------: | :----------------------------: |
|      //div/span[n]       |     选取div下第n个span节点     |
|    //div/span[last()]    |   选取div下倒数第1个span节点   |
|   //div/span[last()-n]   |  选取div下倒数第n+1个span节点  |
| //div/span[position()<n] |     选取div下前n个span节点     |
|       //div[@prop]       |    选取带prop属性的div节点     |
|    //div[@prop=value]    | 选取prop属性值为value的div节点 |

### 通配符

| 通配符 |              描述              |
| :----: | :----------------------------: |
|   *    |        匹配任何元素节点        |
|   @*   | 匹配任何属性节点（无属性取空） |
| node() |       匹配任何类型的节点       |

### 多路径

通过在路径表达式中使用`|`运算符，可以选取若干个路径。

## 使用

Python中的XPath使用lxml库中的etree模块。

先安装依赖：

```bash
pip3 install lxml
```

基本使用如下：

```python
from lxml import etree

html = etree.HTML(HTML文本)
# 是一个节点还是多个节点，取决于路径表达式的书写。
nodes = html.xpath(路径表达式)
```

# Beautifulsoup4

[Beautiful Soup4](http://www.crummy.com/software/BeautifulSoup/)是一个可以从HTML或XML文件中提取数据的Python库。

它能够通过你喜欢的转换器实现惯用的文档导航，查找，修改文档的方式。Beautiful Soup会帮你节省数小时甚至数天的工作时间。

默认使用Python解析器，但是我们可以使用更加强大的lxml解析器。

安装：

```bash
pip3 install beautifulsoup4

pip3 install lxml
```

* 常用属性

```python
from bs4 import BeautifulSoup

soup = BeautifulSoup(open('demo.html', encoding='utf-8'), 'lxml')

# 获取第一条div标签
soup.div

# 获取第一条div的所有属性
soup.div.attrs

# 获取第一条div的指定属性
soup.div.attrs["class"]
soup.div["class"]

# 获取第一条div的文本内容
# 获取第一条文本内容
soup.div.string # 返回第一条文本内容，可以为空。
soup.div.strings # 返回一个生成器，包含所有文本内容。
soup.div.stripped_strings # 返回一个生成器，不包含空白符。
# 获取所有文本内容
soup.div.text # 返回一个字符串
soup.div.contains # 返回一个列表
```

* find方法：返回第一条匹配的节点。

```python
soup.find('div')
# 属性筛选
soup.find('div', attrs={"Key": "Value"})
soup.find('div', id="idValue")
soup.find('div', class_=["Value1", "Value2"])
```

* find_all方法：返回所有匹配的节点列表。

```python
soup.find_all('div')
# 多标签匹配
soup.find_all(['div', 'p'])
# 指定范围
soup.find_all('div')[0:5]
soup.find_all('div', limit=5)
# 正则匹配
soup.find_all(re.compile(pattern))
```

* select方法：返回所有匹配选择器的节点列表。

```python
soup.select('div[class="name"]>p>span')
```

