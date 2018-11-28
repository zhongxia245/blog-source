---
title: NodeJs从零构建代理ip池（一）介绍
tags: 'nodejs , 爬虫'
description: 本系列主要介绍如何从零构建一个简单的代理IP池。涉及到的知识点：NodeJs, MySql, Eggjs, Sequelize, Async/Await 异步处理, PM2等知识点的入门与应用。
abbrlink: 8230
date: 2018-11-26 23:48:20
---

本系列主要讲解如何从零实现一个简单的代理 IP 池，教你从 Node 爬虫入门到融会贯通。

跟着本系列教程，将会学到一个完整 NodeJs 项目的开发到部署的一整套流程。

## 零、项目介绍

**目标：维护一个相对稳定，长期可用的免费代理 IP。**

采用定时爬虫，不停的去各大免费代理 IP 网站，爬取代理 IP 数据，并定时清洗数据存入数据库。
因为免费的代理 IP 非常不稳定，可能现在可以用，一个小时后就无法使用。因此还需要每隔一段时间，去校验代理 IP 是否可用，清理不可用的代理 IP,保证数据库中，存在一堆相对稳定可用的代理 IP。

项目预览地址: http://ip.izhongxia.com
项目源码地址: [simple-proxy-pool](https://github.com/zhongxia245/simple-proxy-pool)

![](https://i.loli.net/2018/11/28/5bfddeabae49a.gif)

## 一、文章目录

1. [项目框架介绍与搭建](http://www.izhongxia.com/posts/57516.html)
2. 爬虫抓取数据 [TODO]
3. 清洗数据，并保存到 MySql 数据库 [TODO]
4. 定时抓取数据和清洗数据 [TODO]
5. 定时校验代理 IP 的可用性 [TODO]
6. 使用 BootStrap 实现数据展示页面 [TODO]
7. 使用 PM2 进行项目部署 [TODO]

## 二、为什么写这个系列

在一次爬虫数据抓取的过程中，IP 被封了。 虽然以前知道有代理 IP 这个东西，但是由于爬虫的量很小，并且没有高频次的抓取，因此没有用到代理 IP。刚好这次碰到了这个问题，那么就自己维护一个相对稳定的免费代理 IP 池。

然后采用 Eggjs 为基础框架，用来两个周末的时间，完成了这个代理 IP 池。

乘着还清楚的记得，开发的各大过程，思路，以及开发中遇到的坑， 就准备编写一下这个系列《NodeJs 从零实现代理 IP 池》的文章。
