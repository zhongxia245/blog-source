---
title: 移动端开发中JavaScript遇到问题汇总
date: 2017-07-17 18:44:00
tags: JavaScript
---

## 一、前言
记录日常的移动端页面开发中，遇到的一些 JS问题。


## 二、遇到的问题
 
### 2.1 移动端下对象数组排序问题
```javascript
let arr = [
  { seq: 2, },
  { seq: 1, },
  { seq: 3, }
]

// 正确
console.log(arr.sort((obj, obj1) => {
  return obj.seq > obj1.seq ? 1 : -1
}))

//移动端下，这样排序不起作用
console.log(arr.sort((obj, obj1) => {
  return obj.seq > obj1.seq ? true : false
}))

```

>看了下 数组排序用的 sort 函数， 内部是跟0来比较，返回 true false 就会 有问题。【奇怪的是，PC 端为什么可以，莫名其妙的】
![](https://ww1.sinaimg.cn/large/006tKfTcly1fhn2rzt4azj30jb06ywg0.jpg)