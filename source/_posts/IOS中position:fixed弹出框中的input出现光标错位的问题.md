---
title: IOS中position:fixed弹出框中的input出现光标错位的问题
date: 2017-12-17 09:51:06
tags: React
categories: 前端
description: IOS的浏览器下，固定布局 `position:fixed` 在 input 获取焦点的时候，就会失效，导致页面样式出问题， 本文主要讲解： 为什么出现？ 如何解决？ 
---

## 一、参考文章

[IOS 中 position:fixed 弹出框中的 input 出现光标错位的问题](https://www.cnblogs.com/NatChen/p/7941133.html)

## 二、出现原因

![](https://ws1.sinaimg.cn/large/006tNc79gy1fmjj2rqq4yj30ku112mym.jpg)
IOS 下，input 获取焦点时，position:fixed; 属性则会失效， 这个是一个移动端的兼容性问题。

## 三、解决光标定位不准的问题

### 3.1 方案一

> 原理：弹窗时，设置了 fixed 布局，fixed 会失效，然后页面就会滚动到最顶端，然后这个的解决弊端就是，会滚动到页面顶端

```javascript
// 弹窗时
$('body').css({ position: 'fixed' })

// 隐藏时
$('body').css({ position: 'relative' })
```

### 3.2 方案二

> 原理和上面的是一样的， 让页面会弹到最顶端

```javascript
// 弹窗时
var h = $(window).height()
$('body,html').css({ overflow: 'hidden', height: h + 'px' })

// 隐藏时
$('body,html').css({ overflow: 'auto', height: 'auto' })
```
