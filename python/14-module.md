---
title: 14.模块和包
date: 2022-10-14 00:00:00
category: Python语言
tags:
  - module
  - import
  - from
---

# 模块

Python 模块(Module)，是一个 Python 文件，以 .py 结尾，包含了 Python 对象定义和Python语句，模块能定义函数，类和变量量，模块⾥也能包含可执行的代码。

## 导入模块

```python
# 直接导入模块
import 模块名

# 导入模块的某个功能
from 模块名 import 功能名

# 导入模块的所有功能
from 模块名 import *

# 导入模块并取别名
import 模块名 as 别名

# 导入模块的某个功能并取别名
from 模块名 import 功能名 as 别名
```

## 模块的定位顺序

当导⼊⼀个模块，Python解析器对模块位置的搜索顺序是:
1. 当前⽬录
2. 如果不在当前⽬录，Python则搜索在shell变量PYTHONPATH下的每个目录。
3. 如果都找不到，Python会搜索默认路径。UNIX下，默认路径一般为/usr/local/lib/python/

模块搜索路径存储在system模块的sys.path变量中。变量⾥包含当前目录，PYTHONPATH和由安装过程决定的默认目录。

> 自⼰的⽂件名不要和已有模块名重复，否则导致模块功能无法使用。
>
> 使⽤from 模块名 import功能的时候，如果功能名字重复，调用到的是最后定义或导⼊的功能。

如果模块文件中含`__all__`变量，使用`*`导入全部功能时，只会导入该变量列表的功能。

```python
# 定义模块，文件名为 my_module.py
__all__ = ["func1", ]

def func1():
  print("功能1")

def func2():
  print("功能2")
  
# 导入模块（需要在第二个文件中执行）
from my_module import *

# 可用
func1()
# 不可用
func2()
```

# 包

将有关联的模块都组织的放在同一个文件夹下，并且该文件夹下包含一个`__init__.py`文件，那么这个文件夹就称为包。

```python
# 导入某个包中的某个模块的某个功能
import 包名.模块名.功能

# 导入包的所有模块
from 包名 import *

# 导入包中某个模块的所有功能
from 包名.模块名 import *
```

> 也可以在`__init__.py`文件中定义`__all__ = []`变量来控制导入的模块及功能。

