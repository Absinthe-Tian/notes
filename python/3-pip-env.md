---
title: 3.Pip和虚拟环境
date: 2022-10-03 00:00:00
category: Python语言
tags:
  - pip换源
  - 虚拟环境
  - env
---

# pip换源

pip是Python的包管理工具，用于安装和管理Python包。

## 临时换源

```bash
pip3 install 安装包 -i 换源地址
```

## 永久换源

```bash
# 升级到最新pip
pip3 install -U pip

# 全局换源
pip3 config set global.index-url 换源地址
```

常用的国内PyPi源有：

* 清华大学：https://pypi.tuna.tsinghua.edu.cn/simple
* 阿里云：https://mirrors.aliyun.com/pypi/simple
* 豆瓣：https://pypi.douban.com/simple
* 中国科大 https://pypi.mirrors.ustc.edu.cn/simple/

> 永久换源后，就可以直接安装依赖，不需要使用`-i`参数指定安装源啦。

# 创建虚拟环境

通过pycharm创建需要注意的是，可能会因为依赖源下载慢导致报错，提前[换源](https://www.knight-blog.cn/python/71240fa/#pip换源)即可。

1. 创建并进入项目文件夹

```bash
mkdir projectName && cd projectName
```

2. 创建并激活虚拟环境

```bash
python3 -m venv ./venv && source ./venv/bin/activate
```

退出虚拟环境

```bash
deactivate
```

或使用`mkvirtualenv`创建

```bash
# 安装virtualenv
sudo apt install virtualenv

# 安装virtualenvwrapper，安装完后重启终端
sudo apt install virtualenvwrapper

# 创建并激活虚拟环境
mkvirtualenv -p /usr/bin/python3 envName

# 切换虚拟环境
workon envName

# 删除虚拟环境
rmvirtualenv envName
```

> 成功激活虚拟环境后，会在终端行首展示`(venv)`。
> 后续操作将在虚拟环境中进行。

# 依赖管理

requirements.txt可以帮助我们将项目的所有依赖进行导出，在移植到其他环境时可以快速安装依赖。

## 导出依赖

会将当前环境的所有依赖打包到requirements.txt文件。

```bash
pip3 freeze > requirements.txt
```

## 安装依赖

会将requirements.txt文件的所有依赖都安装到此工作环境。

```bash
pip3 install -r requirements.txt
```

# 在线编辑器

如果有小伙伴不想安装Python环境，又希望能够快速入门，那么可以使用在线编辑工具进行学习，在线编辑器有很多，但是大部分都存在一些问题，如不能输入、不能导入第三库、不能保存等。

这里小编推荐[Lightly在线编辑器](https://lightly.teamcode.com/dashboard)，不仅可以编写Python程序，还支持C、Java、PHP、React、Vue等其他热门语言，同时还有强大的设置功能，免费版账户拥有500M存储空间，完全足够学习使用，并且可以从[GitHub](https://github.com/)、[Gitee](https://gitee.com/)或其他版本库中导入项目，可以随时随地的进行代码编写。