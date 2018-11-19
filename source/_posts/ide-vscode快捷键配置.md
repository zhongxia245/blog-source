---
title: vscode快捷键配置
tags: ide
description: 一个好用的编辑器可以提升你很大的开发效率，vscode是一个一直在用的编辑器，这集记录一下常用的一些配置。
abbrlink: 41631
date: 2017-08-02 10:51:50
---

开发人员都应该熟练掌握一个 IDE，能够进行快速的开发，调试。 熟练掌握 IDE，就必须要了解一些常用的快捷键（删除一行，复制一行，全选光标选中的单词，全局查找文件，全局查找字符串等）。

### 一、Vscode 快捷键设置

习惯使用 vscode，这里放一下，自己 vscode 的配置。

```json
// 将键绑定放入此文件中以覆盖默认值
[
  {
    "key": "cmd+d",
    "command": "editor.action.deleteLines",
    "when": "editorTextFocus"
  },
  {
    "key": "shift+cmd+k",
    "command": "editor.action.addSelectionToNextFindMatch",
    "when": "editorFocus"
  },
  {
    "key": "shift+cmd+l",
    "command": "editor.action.selectHighlights",
    "when": "editorFocus"
  },
  {
    "key": "ctrl+u",
    "command": "editor.action.transformToUppercase"
  },
  {
    "key": "ctrl+shift+u",
    "command": "editor.action.transformToLowercase"
  },
  //cmd+shift+/多行注释
  {
    "key": "cmd+shift+/",
    "command": "editor.action.blockComment",
    "when": "editorTextFocus"
  },
  {
    "key": "cmd+1",
    "command": "workbench.action.openEditorAtIndex1"
  },
  {
    "key": "cmd+2",
    "command": "workbench.action.openEditorAtIndex2"
  },
  {
    "key": "cmd+3",
    "command": "workbench.action.openEditorAtIndex3"
  },
  {
    "key": "cmd+4",
    "command": "workbench.action.openEditorAtIndex4"
  },
  {
    "key": "cmd+5",
    "command": "workbench.action.openEditorAtIndex5"
  },
  {
    "key": "cmd+6",
    "command": "workbench.action.openEditorAtIndex6"
  },
  {
    "key": "cmd+7",
    "command": "workbench.action.openEditorAtIndex7"
  },
  {
    "key": "cmd+8",
    "command": "workbench.action.openEditorAtIndex8"
  },
  {
    "key": "cmd+9",
    "command": "workbench.action.openEditorAtIndex9"
  },
  {
    "key": "ctrl+1",
    "command": "workbench.action.focusFirstEditorGroup"
  },
  {
    "key": "ctrl+2",
    "command": "workbench.action.focusSecondEditorGroup"
  },
  {
    "key": "ctrl+3",
    "command": "workbench.action.focusThirdEditorGroup"
  }
]
```
