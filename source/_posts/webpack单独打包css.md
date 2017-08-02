---
title: webpack单独打包css
date: 2016-05-14 10:50:25
tags: Webpack
categories: 前端
---
webpack 把所有的资源都当成了一个模块, CSS,Image, JS 字体文件 都是资源, 都可以打包到一个 bundle.js 文件中. 
但是有时候需要把样式 单独的打包成一个文件, 然后放到 CND上, 然后缓存到浏览器客户端中
<!--more-->

---
这个操作很简单的，只需要一个插件就好了，就是`extract-text-webpack-plugin`

#### 1. 安装extract-text-webpack-plugin
```
npm install extract-text-webpack-plugin --save-dev
```

#### 2. 配置文件添加对应配置
首先require一下
```
var ExtractTextPlugin = require("extract-text-webpack-plugin");
```
plugins里面添加
```
new ExtractTextPlugin("styles.css"),
```
实例：
```
plugins:  [
      new webpack.optimize.CommonsChunkPlugin('common.js'),
      new ExtractTextPlugin("styles.css"),  
],
```
modules里面对css的处理修改为
```
{
    test: /\.css$/,
     loader:  ExtractTextPlugin.extract("style-loader","css-loader")
},
```
**千万不要重复了，不然会不起作用的**

  我这里如下：
```
module:  {
      loaders:  [
            {
            test: /\.css$/,
             loader:  ExtractTextPlugin.extract("style-loader","css-loader")
        },
            {
            test:  /\.scss$/,
             loader:  "style!css!sass"
        },
            {
            test:  /\.less$/,
             loader:  "style!css!less"
        },
    ]
},
```
#### 3. 在引入文件里面添加需要的css，【举例如下】

```
require('../less/app.less');
require('./bower_components/bootstrap-select/dist/css/bootstrap-select.min.css');
require('./bower_components/fancybox/source/jquery.fancybox.css');
```