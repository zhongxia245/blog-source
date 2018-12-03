---
title: NodeJs从零构建代理ip池（二）项目框架介绍与搭建
tags:
  - nodejs
  - 爬虫
description: >-
  本系列主要介绍如何从零构建一个简单的代理IP池。涉及到的知识点：NodeJs, MySql, Eggjs, Sequelize, Async/Await
  异步处理, PM2等知识点的入门与应用。
abbrlink: 57516
date: 2018-11-28 09:06:35
---

项目源码：[simple-proxy-pool](https://github.com/zhongxia245/simple-proxy-pool/releases/tag/v0.1)

Eggjs 是阿里开源的一个企业级的 Nodejs web 基础框架。使用 Eggjs 再结合官网文档过一遍，就可以很容易上手了。

采用 Eggjs 搭建项目的框架, 《[EggJs 官网文档](https://eggjs.org/zh-cn/intro/quickstart.html)》。

## 零、总结

1. [初始化项目框架，并运行起来](#一、初始化项目)
2. [实现一个简单的爬虫，抓取代理 IP](#三、-写一个简单的爬虫)

## 一、初始化项目

```bash
# 初始化项目
$ npx egg-init simple-proxy-pool --type=simple
$ cd simple-proxy-pool
$ npm i

# npm安装慢的话，可以使用cnpm
$ npm i -g cnpm
$ cnpm i

# 启动项目
$ npm run dev
$ open localhost:7001
```

![](https://i.loli.net/2018/11/28/5bfde19cecffd.png)

## 二、目录结构

这里可以去看下[《Eggjs 目录结构说明》](https://eggjs.org/zh-cn/basics/structure.html), 这里有详细的解释。

> 项目开发中，会用到的目录有主要是以下这些

`app/router.js` 用于配置 URL 路由规则
`app/controller/**` 用于解析用户的输入，处理后返回相应的结果
`app/service/**` 用于编写业务逻辑层，可选，建议使用
`config/config.{env}.js` 用于编写配置文件
`config/plugin.js` 用于配置需要加载的插件 【数据库插件，模板渲染插件等】
`app/schedule/**` 用于定时任务 【定时抓取数据，定时清洗无用数据】

## 三、 写一个简单的爬虫

> 2018-11-30 00:01:08：修改：本来抓取的站点 ip，突然做了反爬虫处理，因此先换一个代理 ip 列表

爬虫，简单的理解，就是发出一个 http 请求，拿到页面的 html 内容，然后解析 html 内容，得到自己想要的数据。

这里都可以说不算是爬虫的，可以说是接口， 不过没事先把数据抓回去，清理入库，然后定时抓取，整套流程起来后，在增加爬虫的数量。

这里需要使用三个 npm 库

1. `request` 用来发出 http 请求,
2. `request-promise` promise 形式的 request 库， promise 化主要是为了方便 配置 async 和 await 使用

```bash
# 安装需要的库
cnpm i --save request request-promise iconv-lite
# 重新启动项目
cnpm run dev
```

{% fold 显示/隐藏代码 %}

```js
// simple-proxy-pool/app/controller/home.js
'use strict'

const Controller = require('egg').Controller
const rp = require('request-promise')

class HomeController extends Controller {
  /**
   * 抓取代理IP
   */
  async getZhongxiaIp() {
    try {
      // 设置HTTP请求参数
      const options = {
        method: 'GET',
        url: 'http://ip.izhongxia.com/get_ips',
        headers: {
          'User-Agent':
            'Mozilla/5.0 (Macintosh; Intel Mac OS X 10_14_0) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/70.0.3538.110 Safari/537.36'
        }
      }
      // 发出http请求
      let data = await rp(options)
      return data
    } catch (error) {
      console.warn('get proxy list error ...')
      return error
    }
  }

  async index() {
    // 因为获取代理ip是异步的，因此配合promise化的request， 可以直接使用 await来处理异步
    // 不用像早起写成功回调
    this.ctx.body = await this.getZhongxiaIp()
  }
}

module.exports = HomeController
```

{% endfold %}

> 注意点：抓取数据的时候，可能因此没有代理 ip 网站的有反爬虫机制，因此需要做一些特殊的处理。 比如这个网站 需要携带 cookie ,否则会报 521 错误。

运行结果
![](https://i.loli.net/2018/11/30/5c000dac50a14.png)
