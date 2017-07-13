---
title: React Native Network Request Failed 解决方案
date: 2016-12-09 10:53:10
tags: ReactNative
categories: ReactNative
---
在ReactNative使用fetch去异步加载数据。 这里的 fetch 和浏览器的 fetch 用法是一样的。
但是， ***这里的 异步请求，都不存在跨域问题。***
<!--more-->

## React Native Network Request Failed
>时间：2016-12-09 08:12:29
>在ReactNative使用fetch去异步加载数据。 这里的 fetch 和浏览器的 fetch 用法是一样的。
但是， ***这里的 异步请求，都不存在跨域问题。***

## 一、问题描述

```javascript
const url = 'http://bbs.reactnative.cn/api/category/3'
fetch(url)
  .then(res => res.json())
  .then(result => {
    console.log('result', result);
  })
  .catch(error => {
    console.log('error', error);
  })
```

>Network Request Failed

![](http://ww2.sinaimg.cn/large/006tKfTcgw1fak80695twj31kw0zk182.jpg)


## 二、原因
>IOS9以上，默认需要请求https协议的接口导致该问题。

IOS9引入了新特性App Transport Security (ATS)。新特性要求App内访问的网络必须使用HTTPS协议，意思是Api接口以后必须是HTTPS。但是我的项目使用的是HTTP协议，现在也不能马上改成HTTPS协议传输。

## 三、解决方案
1. 在Info.plist中添加NS App Transport Security类型Dictionary。 
2. 在NS App Transport Security下添加`NSAllowsArbitraryLoads`类型Boolean,值设为`YES`

设置完如图：
>添加的时候，输入 NSAllowsArbitraryLoads 名称，显示的时候就如图一样。 NS 不显示了
![](http://ww3.sinaimg.cn/large/006tKfTcgw1fak84t94w0j30uu06qabs.jpg)

## 四、请求到数据的效果图
![](http://ww4.sinaimg.cn/large/006tKfTcgw1fak879x9oxj31kw0neagm.jpg)
