---
title: Immutable入门
tags: js
categories: 前端
description: >-
  Immutable 是和 React 同一个时期出现的库，用于解决 JavaScript 没有不可变数据结构的问题。
  （到目前既然在项目中应用过这个库，尴尬.png）
abbrlink: 51986
date: 2017-08-09 00:28:28
---

## 一、背景

刚开始接触 React 的时候，就知道有 Immutable 这个东西，是用来保证 state 不可变的。

Immutable 会创建一个不可变的对象，保证 State 的一致性。

这个是最基本的理解，也是以前的理解。 由于业务开发中没有使用到，因此没有具体学习到如何使用。

今天看到一个面试者的简历，上面有写到这个技术，因此了解下。

## 二、Immutable 介绍

1.  和 React 同期出现的，但是光芒被 React 给掩盖了
2.  是一个独立的库，什么框架都可以使用
3.  意义在于它弥补了 Javascript 没有不可变数据结构的问题

JavaScript 对象是引用类型

```
var obj = {a:10}
var obj1 = obj
obj1.a = 20

console.log(obj.a)                  // 20

//====================================================
// Jquery 里面深复制，合并对象的用法
var defaultConfig = { /* 默认值 */};
var config = $.extend({}, defaultConfig, initConfig);       // jQuery用法。initConfig是自定义值
var config = $.extend(true, {}, defaultConfig, initConfig); // 如果对象是多层的，就用到deep-copy了


//====================================================
// ES6
Object.assign 相当于浅复制（只是复制了第一层，如果第二层的对象变化了。 还是有问题的）

var obj2 = {a:{name:'zhongxia'}}
var obj3 = Object.assign(obj2)
obj3.a.name = 'hehe'

console.log(obj2.a.name)            // hehe

// 深复制则应该输出  obj2.a.name 还是 zhongxia
```

## 三、Deep-Copy 可以用，为什么还用 Immutable?

差别在性能上面

### 3.1 deep-copy

每次把整个对象复制一次

### 3.2 与 Object.freeze、const 区别

Object.freeze 和 ES6 中新加入的 const 都可以达到防止对象被篡改的功能，但它们是 shallowCopy 的。对象层级一深就要特殊处理了。

### 3.3 Immutable

Immutable 类似链表，添加一个新的节点，把旧节点的父子关系移动到新节点上。

```
var defaultConfig = Immutable.fromJS({ /* 默认值 */});
var config = defaultConfig.merge(initConfig);               // defaultConfig不会改变，返回新值给config
var config = defaultConfig.mergeDeep(initConfig);           // 深层merge
```

![image](http://img.alicdn.com/tps/i2/TB1zzi_KXXXXXctXFXXbrb8OVXX-613-575.gif)

## 五、参考文章

1.  [《搞定 immutable.js》](http://boke.io/immutable-js/)
2.  [《知乎：facebook immutable.js 意义何在，使用场景？》](https://www.zhihu.com/question/28016223)
3.  [笔记, immutable-js 基础操作](https://segmentfault.com/a/1190000002909224)
4.  [Immutable 详解及 React 中实践](https://zhuanlan.zhihu.com/p/20295971)

5.  [《Immutable.js 初识》](http://www.w3cplus.com/javascript/immutable-js.html)
    > 详细的 API 可以看这个
