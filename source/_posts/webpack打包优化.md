---
title: webpack打包优化小结
date: 2017-11-17 15:32:21
tags: Webpack
categories: 前端
description: TODO:（2018-11-17 19:16:32）重新整理了下，这篇文章已经有点过时了，因此有时间需要更新下内容。
---

## 一、学习的文章列表

[《webpack 构建性能优化策略小结》](https://segmentfault.com/a/1190000007891318)
[《Webpack devtool source map》](http://cheng.logdown.com/posts/2016/03/25/679045)
[《Webpack2 升级指南和特性摘要》](https://segmentfault.com/a/1190000008181955)

## 二、优化点

### 2.1 加入 happypack 进行多线程打包

### 2.2 增强 uglifyPlugin

webpack 打包构建中，经常到 80%的时候，就开始停留一段时间，测试发现，这段时间是对 output 的 bundle 文件进行 uglfiyjs 进行压缩损耗的时间。

针对这一块，采用 [webpack-uglify-parallel](https://github.com/tradingview/webpack-uglify-parallel)， 多核并行压缩。

### 2.3 单独打包 dll 包

把一些公共的 npm 库，单独打包出来， 比如 react, react-router ,redux...， 然后在 html 中 使用 script 引入。

### 2.4 修改 devtool 提升构建的时间

参考文章 [《Webpack devtool source map》](http://cheng.logdown.com/posts/2016/03/25/679045)

For development, use `cheap-module-eval-source-map`. For production, use `cheap-module-source-map`.

## 三、测试结果

> 多次测试的结果， 测试机 mac, i5, 8G

### 3.1 采用 happypack， 和 webpack-uglify-parallel 加强 和 devtool = 'cheap-module-source-map'

| 构建内容           | 优化前(ms) | 优化后(ms) |
| ------------------ | ---------- | ---------- |
| 多个 JS 入口【1】  | 208038ms   | 106484ms   |
| 多个 CSS 入口【1】 | 38731ms    | 21575ms    |
| 多个 JS 入口【2】  | 232045ms   | 107905ms   |
| 多个 CSS 入口【2】 | 38145ms    | 25439ms    |
| 多个 JS 入口【3】  | 234041ms   | 108487ms   |
| 多个 CSS 入口【3】 | 38731ms    | 27816ms    |

### 3.2 devtool 替换成 eval

> 不推荐使用， 打包出来的 js 文件变得超级大，和没有压缩的一样大

| 构建内容           | 优化前(ms) | 优化后(ms) |
| ------------------ | ---------- | ---------- |
| 多个 JS 入口【1】  | 208038ms   | 34526ms    |
| 多个 CSS 入口【1】 | 38731ms    | 15862ms    |

#### eval:

![](https://ws1.sinaimg.cn/large/006tKfTcgy1fll5vo6r8sj31k80yqk4z.jpg)

#### cheap-module-source-map:

![](https://ws3.sinaimg.cn/large/006tKfTcgy1fll5vningpj31ii0vaamr.jpg)

## 四、配置文件

线上的 webpack 配置

```javascript
var path = require('path')
var glob = require('globby')
var webpack = require('webpack')
var nib = require('nib')
var ExtractTextPlugin = require('extract-text-webpack-plugin')
var os = require('os')
var UglifyJsParallelPlugin = require('webpack-uglify-parallel')
var HappyPack = require('happypack')
var happyThreadPool = HappyPack.ThreadPool({ size: os.cpus().length })

var JS_PATH = path.join(__dirname, 'src')

function getEntries() {
  var map = {}
  var fileList = glob.sync([
    './src/**/*.js',
    '!./src/gulp/**/*.js',
    '!./src/common/**/*.js'
  ])

  fileList.forEach(function(file) {
    var name = path.basename(file)
    var filePath = path.relative(JS_PATH, file)
    if (name.match(/^[^_](.+)\.js$/)) {
      map[filePath] = file
    }
  })

  return map
}

var CSS_PATH = {
  css: {
    pattern: './src/**/[^_]*.styl',
    src: path.join(__dirname, 'src'),
    dst: 'static/build'
  }
}

function getCSSEntries(config) {
  var fileList = glob.sync(config.pattern)
  return fileList.reduce(function(previous, current) {
    var filePath = path.parse(path.relative(config.src, current))
    var withoutSuffix = path.join(filePath.dir, filePath.name)
    previous[withoutSuffix] = current
    return previous
  }, {})
}

var uglifyJsPlugin = new webpack.optimize.UglifyJsPlugin({
  comments: false,
  compress: {
    warnings: false
  }
})

module.exports = [
  /**
   * CSS打包问题
   */
  {
    devtool: 'source-map',
    context: __dirname,
    entry: getCSSEntries(CSS_PATH.css),
    output: {
      path: CSS_PATH.css.dst,
      filename: '[name].css'
    },
    module: {
      loaders: [
        {
          test: /\.styl$/,
          loader: 'happypack/loader?id=happystyle'
        }
      ],
      resolve: {
        extensions: ['', '.styl']
      }
    },
    plugins: [
      new ExtractTextPlugin('[name].css'),
      new HappyPack({
        id: 'happystyle',
        loaders: ['style-loader', 'css-loader', 'stylus-loader'],
        threads: 2,
        verbose: true
      })
    ],
    stylus: {
      use: [nib()]
    }
  },
  /**
   * JS 文件打包
   */
  {
    context: __dirname,
    entry: getEntries(),
    output: {
      path: path.join(__dirname, 'static/build/webpack'),
      filename: '[name]'
    },
    module: {
      loaders: [
        {
          test: /\.jsx?$/,
          // loader: 'babel-loader',
          loader: 'happypack/loader?id=happybabel',
          exclude: /node_modules/
          // query: {
          //   cacheDirectory: true
          // }
        },
        /**
         * 这里构建出来的路径为什么和webpack.config.js不一样？
         * 因为打包上线的时候，组件库里面的文件引用，没有替换成打包后的路径，而还是用webpack构建时的路径
         * dev环境下，构建文件扔在 build下
         * prod环境，构建文件扔在 dist下
         */
        {
          test: /\.(eot|woff|woff2|svg|ttf|png|jpg|jpeg|gif)(\?v=[\d\.]+)?$/,
          loader: 'file?name=/dist/files/[name].[ext]',
          exclude: /node_modules\/antd-mobile/
        },
        {
          test: /\.(svg)$/i,
          loader: 'svg-sprite',
          include: [require.resolve('antd-mobile').replace(/warn\.js$/, '')]
        },
        {
          test: /\.css/,
          loader: 'style!css!postcss',
          options: {
            modules: true
          }
        },
        {
          test: /\.less$/,
          loader: 'style!css!postcss!less',
          options: {
            modules: true
          }
        },
        {
          test: /\.json$/,
          loader: 'json'
        }
      ]
    },
    resolve: {
      root: JS_PATH,
      modulesDirectories: [
        'node_modules',
        path.join(__dirname, '../node_modules')
      ],
      extensions: ['', '.web.js', '.js', '.json', '.less']
    },
    plugins: [
      new webpack.IgnorePlugin(/^\.\/locale$/, /moment$/),
      new webpack.DefinePlugin({
        'process.env': {
          NODE_ENV: JSON.stringify(process.env.NODE_ENV || 'production')
        }
      }),
      new UglifyJsParallelPlugin({
        workers: os.cpus().length,
        mangle: true,
        compressor: {
          warnings: false,
          drop_console: true,
          drop_debugger: true
        }
      }),
      new HappyPack({
        id: 'happybabel',
        loaders: ['babel-loader'],
        threadPool: happyThreadPool,
        verbose: true
      })
    ],
    devtool: 'cheap-module-source-map',
    externals: {
      react: 'var React',
      'react-dom': 'var ReactDOM',
      'react-addons-css-transition-group': 'var ReactCSSTransitionGroup'
    }
  }
]
```
