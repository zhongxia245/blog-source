---
title: React 页面顶部Progress加载进度条
date: 2016-05-05 09:32:52
tags: React
categories: React
---
在做Ajax 请求,或者请求相关资源的时候, 需要有一个进度条来让页面看起来更加的自然,简洁美观. 在 存JS 下,有很多 progress的实现方式, 这里提供一个 react 下的实现方式.
<!--more-->
---

[github 源码地址](http://github.hubspot.com/pace/docs/welcome/)

## React中 如何使用
1. 引入 pace.js 和 pace.css
```
import Pace from  './common/js/pace'
import './common/css/pace.less'
```
2. 开始显示 progress 挑
```
Pace.start()
```

3. 然后看效果即可. 就这么简单

当然,效果有那么多种,如何改变效果呢?
[github 源码地址](http://github.hubspot.com/pace/docs/welcome/) 这里面的 DOWNLOAD 里面其实是样式,复制替换到 pace.css即可

效果图:

![Paste_Image.png](http://upload-images.jianshu.io/upload_images/2004793-d349b2899c75d433.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

---
原文地址: [页面顶部Progress加载进度条](http://zhongxia.win/2016/05/05/React-%E9%A1%B5%E9%9D%A2%E9%A1%B6%E9%83%A8Progress%E5%8A%A0%E8%BD%BD%E8%BF%9B%E5%BA%A6%E6%9D%A1/)