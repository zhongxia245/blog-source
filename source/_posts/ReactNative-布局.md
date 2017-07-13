---
title: ReactNative 布局
date: 2016-05-01 00:07:33
tags: ReactNative
categories: ReactNative
---
不管是在 App ,Web 设置是 任何可见的页面, 布局都是很重要的. 一个好的布局,可以让你的页面更加优雅.
ReactNative 中, 可以使用 CSS3 的 Flex布局, 使用方法基本一样, 并且RN中,都是支持的.
<!--more-->
---

## 1. 宽度单位, 像素密度

**ReactNative 不支持百分比的宽度, 因此 设置宽度的时候,不需要写 单位**
```
<view style={{width:100}}></view>
```

Q: 既然不需要写单位, 那么 100 指的是啥呢?  100px  还是 100pt?
	单位是 pt

Q: 如何获取实际像素?(图片高清化很重要,100*100的图片,如果宽高设置为100*100 , 图片就会模糊,因为屏幕 高清屏)
```
宽高设置为100 * 100 ,图片要求是 100 * pixelRatio
//react 提供了PixelRatio 的获取方式https://facebook.github.io/react-native/docs/pixelratio.html
 var image = getImage({
   width: 200 * PixelRatio.get(),
   height: 100 * PixelRatio.get()
 }); 
<Image source={image} style={{width: 200, height: 100}} />
```

## 2. Flex 布局 

Q: DIV不设置宽度, 会占 100% ,那么 RN中的 Text, View 等之类的呢?
	不设置宽度, 默认100%占满容器

Q: 水平 垂直 居中
	alignItems: 'center',   	//水平居中    
	justifyContent: 'center'    //垂直居中
```
<Text style={[styles.text, styles.header]}>
        水平垂直居中
</Text>
<View style={{height: 100, backgroundColor: '#333333', alignItems: 'center', justifyContent: 'center'}}>
  <View style={{backgroundColor: '#fefefe', width: 30, height: 30, borderRadius: 15}}/>
</View>
```




---
## 总结
总结
1. react 宽度基于pt为单位， 可以通过Dimensions 来获取宽高，PixelRatio 获取密度，如果想使用百分比，可以通过获取屏幕宽度手动计算。
2. 基于flex的布局
	1. view默认宽度为100%
	2. 水平居中用alignItems, 垂直居中用justifyContent
	3. 基于flex能够实现现有的网格系统需求，且网格能够各种嵌套无bug
3. 图片布局
	1. 通过Image.resizeMode来适配图片布局，包括contain, cover, stretch
	2. 默认不设置模式等于cover模式
	3. contain模式自适应宽高，给出高度值即可
	4. cover铺满容器，但是会做截取
	5. stretch铺满容器，拉伸
4. 定位
	1. 定位相对于父元素，父元素不用设置position也行
	2. padding 设置在Text元素上的时候会存在bug。所有padding变成了marginBottom
5. 文本元素
	1. 文字必须放在Text元素里边
	2. Text元素可以相互嵌套，且存在样式继承关系
	3. numberOfLines 需要放在最外层的Text元素上，且虽然截取了文字但是还是会占用空间


## 参考文章
1. [react-native 之布局篇 2015-07-22](http://www.wtoutiao.com/p/pb5atN.html)