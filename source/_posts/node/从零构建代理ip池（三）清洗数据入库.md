---
title: NodeJs从零构建代理ip池（三）清洗代理IP和存入Mysql
tags:
  - nodejs
  - 爬虫
description: >-
  本系列主要介绍如何从零构建一个简单的代理IP池。涉及到的知识点：NodeJs, MySql, Eggjs, Sequelize, Async/Await
  异步处理, PM2等知识点的入门与应用。
abbrlink: 23199
date: 2018-11-28 09:06:35
---

此小节项目源码：[simple-proxy-pool](https://github.com/zhongxia245/simple-proxy-pool/releases/tag/v0.1)

## 零、总结

这个小结主要实现过滤出可用的代理 IP，并保存到 `MySql` 。

## 一、检测代理 IP 是否可用

抓取回来的代理 IP,有很大一部分是不可用的，因此需要清洗过滤一遍，把可用的入库。

检测代理 IP 是否可用，可以用 `request-promise` 这个库（就是写爬虫 http 请求的那个库），给发出去的请求挂上一个代理，然后去请求 `www.baidu.com` 如果正常返回数据，则表示代理可用。

{% fold 显示/隐藏代码 %}

```js
/**
 *  测试代理 ip 是否可用
 */

const Service = require('egg').Service
const rp = require('request-promise')
const cheerio = require('cheerio')

/**
 * 测试是否匿名
 * 匿名分为几种情况， 一层代理和多层代理
 * 1. 一层代理：realIp !== localIp and realIp === proxyIp
 * 2. 多层代理: realIp !== localIp and realIp !== proxyIp
 *
 * @param {function} $ cheerio 对象
 * @param {object} proxyInfo 代理信息
 * @param {string} localIp 本地 ip
 * @return {object} 真实 ip 和匿名信息
 */
const testAnonymous = ($, proxyInfo, localIp) => {
  let anonymous = '透明'
  const realIp = $('#ip')
    .text()
    .trim()
  if (realIp === localIp) {
    anonymous = '透明'
  } else if (realIp === proxyInfo.ip) {
    anonymous = '匿名'
  } else {
    anonymous = '高匿'
  }
  return {
    real_ip: realIp,
    anonymous
  }
}

class ProxyListService extends Service {
  /**
   * 测试代理 ip，过滤掉一些无效的 ip
   *
   * @param {array} proxyList 代理列表
   * @param {function} cb 每个 ip 测试完的回调
   * @param {number} [maxTime=10 * 1000] 过滤掉一些时间慢的代理 ip 延迟超过10s，则过滤掉
   * @return {Array} 可用的代理 ip 列表
   */
  async getRealProxyIp(proxyList = [], maxTime = 10 * 1000) {
    let data = null
    const realList = [] // 可用代理ip列表
    const times = [] // 代理请求时间
    const promiseRealProxyTest = [] // promise数组

    const count = proxyList.length

    // 目前的测试页面，带有 真实 ip 信息，ua，cookie，页面地址等信息
    const targetOptions = {
      method: 'GET',
      url: 'http://ip.izhongxia.com/page/info',
      timeout: 8000
    }

    // 把所有的代理ip，组成成一个 promise化的http请求，然后放到数组中， 一次性 promise.all 发起请求
    for (let i = 0; i < count; i++) {
      const proxyIp = proxyList[i]
      targetOptions.proxy = `http://${proxyIp}`

      const startTime = Date.now()

      promiseRealProxyTest.push(
        rp(targetOptions)
          .then(body => {
            times[i] = Date.now() - startTime
            console.log(`==> Success Proxy Ip ${proxyIp}, Time:${times[i]}`)
            return cheerio.load(body)
          })
          .catch(() => {
            times[i] = Date.now() - startTime
            console.log(`==> Error Proxy Ip ${proxyIp}, Time:${times[i]}`)
            return null
          })
      )
    }

    console.log(`========== Test ${count} records start ==========`)

    data = await Promise.all(promiseRealProxyTest)
    data = data.filter(Boolean)

    for (let i = 0; i < data.length; i++) {
      // 从测试页面上，获取自己的 ip 信息，检查是否为高匿名代理
      const info = testAnonymous(data[i], proxyList[i], this.config.localIp)

      // 过滤出可用的，并且代理时间小于 maxTime 的
      if (data[i] && times[i] <= maxTime) {
        realList.push({
          ip: proxyList[i],
          response_time: times[i],
          ...info
        })
      }
    }

    console.log(`========== Test ${count} records , Success rate:${(realList.length / count) * 100}%  end ==========`)

    return realList
  }
}

module.exports = ProxyListService
```

{% endfold %}

增加了过滤出可用的代理 IP 的方法后，把方法应用到爬回来的代理 IP 上。

```js
// controller/home.js
// 修改 index 里面的代码
async index() {
    const { service } = this.ctx
    // 因为获取代理ip是异步的，因此配合promise化的request， 可以直接使用 await来处理异步
    // 不用像早起写成功回调
    let list = await this.getZhongxiaIp()
    let result = await service.testProxyIp.getRealProxyIp(list)
    this.ctx.body = result
  }
```

这样， 每次抓取后，都可以得到可用的代理 IP。 免费的代理 IP 不稳定，因为需要做过滤和定时检验操作。
