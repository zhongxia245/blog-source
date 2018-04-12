---
title: CSS外边距合并解决方案
date: 2017-10-17 10:57:56
tags: CSS
categories: 前端
---
### 零、总结
```
1. 水平margin不会被合并，垂直margin会被合并
2. 外边距合并分为两种
    - 相邻的兄弟元素
    - 块级父元素与其第一个/最后一个子元素
    - 空块元素
3. 解决方案
    - 父子元素不合并： 父元素添加 overflow:auto/hidden;
    - 兄弟元素不合并：设置浮动 / 设置绝对布局 / display:inline-block;
```

### 一、外边距合并出现的原因
>具体原因，看链接
[《外边距合并 MDN》](https://developer.mozilla.org/zh-CN/docs/Web/CSS/CSS_Box_Model/Mastering_margin_collapsing)

1. 相邻的兄弟元素
>合并，以两个兄弟元素，两个 margin 值大的为主，并不是两个 margin 相加

```
<p style="margin-bottom: 30px;">这个段落的下外边距被合并...</p>
<p style="margin-top: 20px;">...这个段落的上外边距被合并。</p>
```

2. 块级父元素与其第一个/最后一个子元素

3. 空块元素

```
<p style="margin-bottom: 0px;">这个段落的和下面段落的距离将为20px</p>

<div style="margin-top: 20px; margin-bottom: 20px;"></div>

<p style="margin-top: 0px;">这个段落的和上面段落的距离将为20px</p>
```

### 二、解决方案

1. 设置了的overflow属性的盒模型，则其与子元素之间的垂直margin不会合并，但其与父元素之间、与相邻元素之间的margin会合并。（overflow取值为visible除外）

2. 设置了float属性的盒模型，则其与相邻元素之间、其与父元素之间、其与子元素之间的垂直margin都不会被合并。

3. 设置了绝对定位position:absolute的盒模型，则其与相邻元素之间、其与父元素之间、其与子元素之间的垂直margin都不会被合并。（但应注意position:absolute对其后元素的position的影响）

4. 设置了display:inline-block的盒模型，则其与相邻元素之间、其与父元素之间、其与子元素之间的垂直margin都不会被合并。
相邻元素的margin是否被折叠会影响元素的位置

防止外边距合并解决方案：
1. **防止元素与子元素margin重叠：**
```
1. 用内层元素的margin通过外层元素的padding代替；
2. 内层元素透明边框 border:20px solid transparent;;
3. 外层元素 overflow:hidden;/overflow:auto;
```

2. **防止元素与子元素、与父元素。与相邻元素的重合：**
```
设置元素绝对定位 postion:absolute;或float:left;或display:inline-block;
```