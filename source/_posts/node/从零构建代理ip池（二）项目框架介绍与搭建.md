---
title: NodeJs从零构建代理ip池（二）项目框架介绍与搭建
tags: 'nodejs , 爬虫'
description: >-
  本系列主要介绍如何从零构建一个简单的代理IP池。涉及到的知识点：NodeJs, MySql, Eggjs, Sequelize, Async/Await
  异步处理, PM2等知识点的入门与应用。
abbrlink: 57516
date: 2018-11-28 09:06:35
---

此小节项目源码：[simple-proxy-pool](https://github.com/zhongxia245/simple-proxy-pool/releases/tag/v0.1)

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

爬虫，简单的理解，就是发出一个 http 请求，拿到页面的 html 内容，然后解析 html 内容，得到自己想要的数据。

这里需要使用三个 npm 库

1. `request` 用来发出 http 请求,
2. `request-promise` promise 形式的 request 库， promise 化主要是为了方便 配置 async 和 await 使用
3. `iconv-lite` html 页面的编码如果是 gbk 的时候，会乱码，因此需要用这个库解码一下。

```bash
# 安装需要的库
cnpm i --save request request-promise iconv-lite
# 重新启动项目
cnpm run dev
```

```js
// simple-proxy-pool/app/controller/home.js
'use strict'

const Controller = require('egg').Controller
const iconv = require('iconv-lite')
const rp = require('request-promise')

class HomeController extends Controller {
  /**
   * 抓取代理IP
   * @param {number} [ipCount=100] 代理数量
   * @return {array} 抓取回来的代理列表
   */
  async get66ipData(ipCount = 100) {
    try {
      // 抓取的页面地址
      const apiURL = `http://www.66ip.cn/nmtq.php?getnum=${ipCount}&isp=0&anonymoustype=0&start=&ports=&export=&ipaddress=&area=0&proxytype=2&api=66ip`

      // 设置HTTP请求参数
      const options = {
        method: 'GET',
        url: apiURL,
        gzip: true,
        headers: {
          Accept: 'text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,*/*;q=0.8',
          'Accept-Encoding': 'gzip, deflate',
          'Accept-Language': 'zh-CN,zh;q=0.8,en;q=0.6,zh-TW;q=0.4',
          'User-Agent':
            'Mozilla/5.0 (Macintosh; Intel Mac OS X 10_14_0) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/70.0.3538.110 Safari/537.36',
          referer: 'http://www.66ip.cn/',
          Cookie:
            'yd_cookie=35c72298-25ad-4321acee5c54def5342d0a3b37ebf11f72c2; _ydclearance=9b6308ea1deb3b3b72c14f64-142a-4adf-9107-694c8922918b-1543373100; Hm_lvt_1761fabf3c988e7f04bec51acd4073f4=1541736200,1542009395,1542297692,1543365902; Hm_lpvt_1761fabf3c988e7f04bec51acd4073f4=1543365915',
          Host: 'www.66ip.cn'
        }
      }

      // 发出http请求
      let data = await rp(options)

      // 如果是 gbk ，则使用icnov 来转
      // node环境不支持 gbk,因此会乱码
      if (/meta.+charset=gbk/i.test(data)) {
        data = iconv.decode(data, 'gbk')
      }

      // 清洗出代理ip
      const proxyList = data.match(/\d{1,3}\.\d{1,3}\.\d{1,3}\.\d{1,3}:\d{1,4}/g)

      // 把数据解析成约定的格式（数据库的格式）
      const newList = []
      proxyList.forEach(item => {
        newList.push({
          ip: item.split(':')[0],
          port: item.split(':')[1],
          anonymous: '高匿',
          source: 'http://www.66ip.cn/'
        })
      })

      return newList
    } catch (error) {
      console.warn('get proxy list error ...')
      return error
    }
  }

  async index() {
    // 因为获取代理ip是异步的，因此配合promise化的request， 可以直接使用 await来处理异步
    // 不用像早起写成功回调
    this.ctx.body = await this.get66ipData()
  }
}

module.exports = HomeController
```

> 注意点：抓取数据的时候，可能因此没有代理 ip 网站的有反爬虫机制，因此需要做一些特殊的处理。 比如这个网站 需要携带 cookie ,否则会报 521 错误。

运行结果
![](https://i.loli.net/2018/11/28/5bfde6ce26458.png)

此小节项目源码：[simple-proxy-pool](https://github.com/zhongxia245/simple-proxy-pool/releases/tag/v0.1)
