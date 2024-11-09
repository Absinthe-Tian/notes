---
title: Typora for Mac破解
date: 2023-11-02 00:00:00
category: 其他
tags:
  - Typora
  - for Mac
  - 破解教程
---

说明：本次破解Mac的1.5.6版本。

1. 前往[Typora 官方](https://typoraio.cn/)下载并安装Mac版本。
2. 打开后右下角显示未激活，完全关闭Typora。
3. 快捷键[command]{.kbd} + [shift]{.kbd} + [g]{.kbd}打开资源搜索框，输入`/Applications/Typora.app/Contents/Resources/TypeMark/page-dist/static/js`并回车，找到`LicenseIndex.xxx.xxx.chunk.js`。
4. 右键->打开方式->文本编辑，[command]{.kbd} + [f]{.kbd} 搜索框输入`hasActivated="true"==e.hasActivated`替换为`hasActivated="true"=="true"`，[command]{.kbd} + [s]{.kbd}进行保存。
5. 重新打开Typora，显示激活成功。

> 这里提供[Typora 1.7.6 for Mac破解版](https://pan.baidu.com/s/14WNCwqYbR5NjxRwimGbN0g?pwd=mgsq)供学习下载。