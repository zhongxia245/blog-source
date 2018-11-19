---
title: Docker实战一【部署 NodeJs Hello World】
tags: docker
description: 学习了一下 Docker 的基本知识，要应用起来，才能把知识变成自己的， 纸上得来总觉浅呀。这里开始记录下实战的内容， 部署 NodeJs 服务。
abbrlink: 29823
date: 2017-11-22 11:57:26
---

学习了一下 Docker 的基本知识，要应用起来，才能把知识变成自己的， 纸上得来总觉浅呀。这里开始记录下实战的内容， 部署 NodeJs 服务。

## 一、构建步骤

### 1. 编写一个简单的 nodejs 静态服务器

```json
// package.json
{
  "name": "study_nodejs",
  "version": "0.0.1",
  "description": "Node.js on Docker",
  "author": "zhongxia",
  "main": "server.js",
  "scripts": {
    "start": "node app.js"
  },
  "dependencies": {
    "express": "^4.13.3"
  }
}
```

```javascript
// app.js
'use strict'
var express = require('express')
var PORT = 8888
var app = express()
app.get('/', function(req, res) {
  res.send('Hello world\n')
})
app.listen(PORT)
console.log('Running on http://localhost:' + PORT)
```

### 2. 选择镜像

刚开始实战，所以就不考虑搞一个最小的 nodejs 环境， 而是直接找一个可以用的 环境即可。

```bash
# 查找镜像
docker search nodejs
```

### 3. 编写 Dockerfile

Dockerfile 是 创建镜像所必须的文件。

```bash
# FORM 是构建镜像的基础镜像源，如果本地没有则会 docker pull 下来
# 坑：如果下载不了，就用国内镜像: hub.c.163.com/nce2/nodejs:0.12.2 ，原因你懂的
FROM heroku/nodejs-hello-world
# 创建目录，保存源码
RUN mkdir -p /home/service
# 设置工作目录
WORKDIR /home/service
# 把本机当前目录的所有文件，拷贝到 镜像中的 /home/service 目录下
COPY . /home/service
# 坑： 如果使用 npm，则要设置国内镜像，否则可能下载不下来
RUN npm config set registry https://registry.npm.taobao.org
RUN npm install
# 镜像对外暴露除8888宽口，默认是不对外开放接口的
EXPOSE 8888
# 这里用来启动项目
CMD [ "node", "app.js" ]
```

### 4. 构建 docker 镜像

```
docker build -t study_nodejs .
```

![](https://ws3.sinaimg.cn/large/006tNc79gy1flqqo6ponoj30wc0osq4c.jpg)

![](https://ws1.sinaimg.cn/large/006tNc79gy1flqqon4inyj318y04wglr.jpg)

### 5. 运行容器

82e776de5186 是镜像的 IMAGE ID

```
docker run -d -p 8888:8888 82e776de5186
```

### 6. 测试

```
curl -i localhost:8888
```

### 7. 额外补充

```bash
# 看日志
docker logs CONTAINER_ID
# 进入容器，执行 linux 命令
docker exec -i -t CONTAINER_ID /bin/bash
```

## 二、后续可以优化的问题

1.  线上部署，代码可以存放在 git
2.  开发环境，代码经常会变，可以把代码放在 volume
3.  优化当前基础镜像，使镜像最小化
4.  其他还未想到，一步一步来

## 三、学习文章

1.  [Docker 实践--部署 Nodejs 应用](https://www.cnblogs.com/li-peng/p/5827104.html)
2.  [淘宝 NPM 镜像](http://npm.taobao.org/)
3.  [NodeJS 使用淘宝 npm 镜像站的各种姿势](http://www.jianshu.com/p/253cb9003411)
