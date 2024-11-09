---
title: Todo Tree的使用
date: 2024-03-21 20:04:43
category: 必备工具
tags:
  - Todo
  - VScode
  - 使用
---

在VScode中，有一款Todo Tree的插件，可以非常方便的给文件标注事项，在VScode拓展中搜索并安装即可使用。

可在settings.json中添加如下配置：

```json
{
  "todo-tree.highlights.customHighlight": {

        "BUG": {
            "icon": "bug",
            "foreground": "#ff0000",
        },
        "HACK": {
            "icon": "tools",
            "foreground": "#fffb00",
        },
        "TODO": {
            "foreground": "#00aaff",
        },
        "FIXME": {
            "icon": "flame",
            "foreground": "#ff00ee",
        },
        "XXX": {
            "icon": "x",
            "foreground": "#ffa200",
        },
        "[ ]": {
            "icon": "issue-draft",
            "foreground": "#00ff22",
        },
        "[x]": {
            "icon": "issue-closed",
            "foreground": "#00ff22",
        }
    },
}
```

在代码文件中使用关键字即可触发（一般与配合注释使用）。

* `TODO`：待开发
* `[ ]`：未完成
* `[x]`：已完成
* `FIXME`：报错,待修复
* `BUG`：BUG,待解决
* `HACK`：待调整
* `XXX`：待改进

