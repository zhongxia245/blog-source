---
title: IOS11 webview 动画影响 系统自带复制功能
date: 2018-03-16 12:05:56
tags: css
categories: 前端
description: 这个是一个坑，只有在 IOS webview下才出现的深坑， 在做动画(animation)的时候， 复制功能居然失效了。 一顿操作后，发现使用 transform 来实现动画，则没有这个问题。
---

在 IOS webview 里面， 使用 JS 定时器修改 margin 或者 top，left 来实现动画， 或者 animation 实现动画，会影响 IOS 本机自带的复制功能。

解决方案： 用 CSS3 的 transform 则没有这个问题。

这个问题的原因不太清楚，猜测是 webview 的问题， 微信 webview 里面打开，则没有这个问题。

```css
@keyframes jump {
  0% {
    margin-top: 0;
  }
  100% {
    margin-top: -30px;
  }
}

.view-money {
  position: absolute;
  top: 170px;
  left: 50%;
  width: 33px;
  height: 33px;
  transform: translateX(-105px);
  animation: jump 1s ease 1s infinite alternate;
}
.view-chart {
  position: absolute;
  top: 210px;
  left: 50%;
  width: 31px;
  height: 50px;
  transform: translateX(110px);
  animation: jump 1s ease infinite alternate;
}
```

在 IOS 中 使用 animation 的情况下， jump 使用 margin-top , 或者 top 来实现动画， 会导致 IOS11 webview 中， 无法复制文字。 不太清除为什么。

解决方案：

```css
@keyframes jump {
  0% {
    transform: translateX(-100px);
  }
  100% {
    transform: translateX(-140px);
  }
}
```

动画采用 translate 实现，复制功能就不受影响。
