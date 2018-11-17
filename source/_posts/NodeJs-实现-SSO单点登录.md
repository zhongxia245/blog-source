---
title: NodeJs 实现 SSO单点登录
date: 2017-12-20 17:38:40
tags: SSO
description: 本文主要记录实现单点登录的流程，并且自己实现的一个 DEMO 案例,附带源码地址。
---

> 有兴趣看代码，可以了解下这个 [《Github Demo》](https://github.com/zhongxia245/nodejs-sso-example)

单点登录系统的流程图
![](https://ws2.sinaimg.cn/large/006tKfTcgy1fmndfxuhxqj30m80irgno.jpg)

## 一、原文地址

[《基于 NODEJS 的 SSO 登录方案》](https://segmentfault.com/a/1190000006103655)

## 二、SSO 的流程

1.  用户登录 A 站系统，返回一个 ticket

2.  服务端把 SessionId 写到 Redis

3.  A 站登录后，马上发一个 B 站的接口请求，携带 ticket 过去

    > 采用 `<img src="api_url"/> 或 iframe` 解决跨域问题 [iframe 比较重，而 img 比较轻]

4.  服务端用 ticket 去 redis 检测是否存在 sessionid, 存在则把 sessionId，写到 B 站的 cookie 里面

5.  因为 A 站和 B 站使用同一个 redis 来存放 sessionid，，所以只要有一个地方退出，则两个网站都退出了。

## 三、中途遇到的坑

1. 关于 SSO 单点注销， SSO 如何通知子系统清除凭证？
   之前在 CNODE 社区问的，链接在这 [《这里这里》](https://cnodejs.org/topic/5b3e2e8c35342ab069061298#5beb9999a05b0e0ae443aee9)。
