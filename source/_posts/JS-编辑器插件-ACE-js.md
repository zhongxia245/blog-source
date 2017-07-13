---
title: JS 编辑器插件 ACE.js
date: 2016-05-14 10:53:10
tags: Javascript
categories: Javascript
---
ace 是 js 实现的代码编辑器
[编译打包之后的 ACE 代码](https://github.com/ajaxorg/ace-builds.git)
[ 官网,未提供编译好的文件 ](https://github.com/ajaxorg/ace)
<!--more-->
---
## ACE 拥有的特点
 1. 语法高亮超过110种语言
 2. 超过20个主题
 3. 自动缩进
 4. 减少缩进
 5. 一个可选的命令行处理庞大的文件（400万线似乎是极限！）
 6. 完全自定义的键绑定，包括vim和Emacs模式
 7. 用正则表达式的搜索和替换
 8. 突出显示匹配的括号
 9. 软标签和实际标签之间切换
 10. 显示隐藏字符
 11. 使用鼠标拖放文本
 12. 换行
 13. 代码折叠
 14. 多个光标和选择
 15. 现场语法检查器
 16. 剪切，复制和粘贴功能
 17. **代码提示**

---
    下载下来的代码, demo/autocompletion.html, demo中 智能提示是 需要按快捷键才会出来的,配置如下
```
 enableLiveAutocompletion: false
```
    把这个配置改成 true ,则 输入字母就会出现提示

![Paste_Image.png](http://upload-images.jianshu.io/upload_images/2004793-65c3b11bc07bf1ea.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

智能代码提示的效果截图:
![Paste_Image.png](http://upload-images.jianshu.io/upload_images/2004793-aaf15c74669e4564.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)