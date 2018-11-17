---
title: PM2部署--入门（一）
date: 2018-04-28 15:03:38
tags: 部署
description: PM2是node进程管理工具，可以利用它来简化很多node应用管理的繁琐任务，如性能监控、自动重启、负载均衡等，而且使用非常简单。
---

## 一、介绍

Nodejs 运行在 Chrome 的 JavaScript 运行时平台，通常称这个平台为 V8 引擎， 不管是 V8 引擎还是 Nodejs 都是单线程的方式运行的。 因此在多核心处理器中并不能发挥最大的性能。

## 二、Nodejs 的 cluster 模块

Nodejs 内置的 Cluster 模块，支持生成多个工作线程来共享同一个 TCP 连接。

### 2.1 cluster 运作流程

1.  Cluster 创建一个 master 线程
2.  根据你的需求，fork 出多个 server app （也称为工作线程）
3.  线程之间通过 IPC 进行通讯
4.  内置负载均衡来处理线程之间的压力【采用 Round-robin 算法进行负载均衡】

> 使用 Round-robin 调度策略时， master.accepts() 会传入所有的连接请求，然后将相应的 TCP 请求分配给选中的工作线程。【同样使用 IPC 进行通讯】

```javascript
// 一个最基本的例子

var cluster = require('cluster')
var http = require('http')
var os = require('os')

var numCPUs = os.cpus().length

if (cluster.isMaster) {
  // Master:
  // Let's fork as many workers as you have CPU cores

  for (var i = 0; i < numCPUs; ++i) {
    cluster.fork()
  }
} else {
  // Worker:
  // Let's spawn a HTTP server
  // (Workers can share any TCP connection.
  //  In this case its a HTTP server)

  http
    .createServer(function(req, res) {
      res.writeHead(200)
      res.end('hello world')
    })
    .listen(8080)
}
```

### 2.2 PM2 功能

pm2 做了一些封装，使得 nodejs 代码不需要变动,然后使用负载均衡部署。

pm2 还增加了很多功能，比如自动重启、后台运行、实时扩展集群、零停机更新、开机自启等等，具体可以看下官网。

## 三、常用命令行

```bash
# 安装
npm install -g pm2

# 常用命令
pm2 start ./bin/www --watch             # 启动express运用
pm2 start app.js                        #启动
pm2 start app.js --name ma-app          #启动项目并指定项目名字
pm2 list|ls                             #显示所有pm2进程
pm2 stop app_id|app_name|all            #停止指定进程
pm2 restart app_id|app_name|all         #重启指定进程
pm2 reload app_id|app_name|all          #reload指定进程
pm2 delete app_id|app_name|all          #删除进程

# 日志管理
pm2 -h                                  #显示所有的pm2 logs命令
pm2 logs app_id                         #打印对应id的日志
pm2 logs app_id --err                   #仅打印错误日志
pm2 logs --line 100                     #打印日志的行数
```

## 参考文章

- [《PM2 官方文档》](http://pm2.keymetrics.io/docs/usage/quick-start/)
- [《PM2 实用入门指南》](http://imweb.io/topic/57c8cbb27f226f687b365636)
- [《使用 PM2 将 Node.js 的集群变得更加容易》](https://www.cnblogs.com/jaxu/p/5193643.html)
