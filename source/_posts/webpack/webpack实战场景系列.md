---
title: webpack实战场景系列
tags: webpack
description: '本系列主要记录webpack在实际项目开发中，都有哪些实际的应用，单页面开发，多页面开发，大项目构建优化，按需加载，动态加载，动态加载并且每个引用单独打包成一个文件等知识的记录'
abbrlink: 44724
folding: true # 是否使用代码折叠
date: 2018-11-20 22:22:18
---

> 货有点干，啃起来很累人，建议先看目录导航，再选择有兴趣的知识点看。

本系列主要记录自己从 16 年到 18 年，项目开发中 Webpack 的一些具体场景的应用，记录只记录核心配置代码。

## 目录导航

> webpack 版本：4.26.0 ， 项目以 React 为例。

记录一下，在项目实际开发中遇到的一些场景

1. [单页面开发](#1-单页面开发)
2. [多页面开发](#2-多页面开发)
3. [如何单独打包出 CSS 文件？](#3-单独打包出-CSS-文件)
4. [项目比较大之后，如何优化构建速度](#4-项目比较大之后，如何优化构建速度)
5. [引用的模块名是动态变化的](#5-引用的模块名是动态变化的)
6. [动态引用，把所有文件打包成一个文件如何解决](#6-动态引用，把所有文件打包成一个文件如何解决)

## 1. 单页面开发?

单页面开发是 webpack 应用中，醉普遍的一种场景，基本所有的后台系统，web 需要比较好交互的，对 SEO 要求不太非常高的都可以做成单页面。

**Q：有什么好处？**

- 交互体验比多页面好
- 一次加载完资源（项目大可以按需加载）
- 页面间跳转交互方便实现,方便实现更大型的 Web 应用

**Q：有什么不足？**

- 对 SEO 不友好，需要加载完 JS 才能渲染出页面，对爬虫（蜘蛛）抓取比较难。 可以做 SSR 实现首屏支出解决。

**Q：比较好的实践？**

- 快速构建出中小型后台系统？
  推荐蚂蚁金服的 [antd-design-pro](https://pro.ant.design/index-cn) , [ice](https://alibaba.github.io/ice/)

- 有自己公司的组件库?
  可以用 `create-react-app` 命令直接创建一个脚手架,直接快速开干。

- 从零搭建针对公司项目的脚手架?
  需要考虑的东西比较多，webpack 的基本配置，解析图片，解析 css，支持 less 或者 sass，支持 es6、7、8 语法，支持开发环境热更新，线上构建出来的文件+hash 值， 上传到 CDN，开发时如何解决跨域问题，项目大了如果做优化，是否需要按需加载等。**_[公司的脚手架是从零开始搭建出来的，上面的这些都经历过]_**

## 2. 多页面开发?

如果公司的产品需要做很多活动，一大堆促销活动，邀请活动，周年活动，每个活动之间没有关联，每个活动是独立的。 那么你们就需要做成多页面应用。
那么多页面还用 react，用 webpack 吗？ 当然的，毕竟模块化，组件化，不管是开发还是维护上都是舒服太多了。(用 vue 也可以，看公司技术栈)

**Q：webpack 如何配置？**
核心思路：用 nodejs 遍历 src 目录下的文件，找出所有的入口文件, 然后其他和单页面是一样的。

代码有点多，想了想还是方上来一部分代码，不然后面查看起来比较麻烦, 这里讲一下基本实现。

1. 遍历出多页面的入口 `Js` 文件，以及对应的 `HTML` 模板(生成的文件，最终要注入到 `html` 模板才有意义, 因为不同的活动，模板可能不同，因此做成每个活动一个 `html` 模板)
2. 匹配入口 `JS` 文件对应的 `html` 模板，然后用 `HtmlWebpackPlugin`插件 生成 `html`,并且注入对应的 `js` 文件
3. 把生成的 `html plugins` 列表，放到 `webpack` 的插件配置中

> 注意，如果 `HtmlWebpackPlugin3.x` 是 3.x 版本，当 html 的文件数量上传到一定程度（大概 100 多个的时候），构建速度超级超级慢(20 几分钟)， 换成 `HtmlWebpackPlugin4.x` 就好了。 这个是 `HtmlWebpackPlugin3.x` 的 bug

```js
// 只放了核心代码
const path = require('path')
const HtmlWebpackPlugin = require('html-webpack-plugin')

// 根据匹配规则输出正确的文件路径
/*
返回结果 eg:
最终生成的文件，会是key的名称，也就是会放在 fe/demo/ 目录下
{ 
  'fe/demo/index': '/Users/zhongxia/Code/webpack/webpack4-mul-demo/sample/fe/demo/index.jsx' ,
  'fe/demo/test': '/Users/zhongxia/Code/webpack/webpack4-mul-demo/sample/fe/demo/test.jsx' ,
}
*/
const getEntries = pattern => {
  var fileList = glob.sync(pattern)
  return fileList.reduce((previous, current) => {
    var filePath = path.parse(path.relative(path.resolve(__dirname, './src'), current))
    var withoutSuffix = path.join(filePath.dir, filePath.name)
    previous[withoutSuffix] = path.resolve(__dirname, current)
    return previous
  }, {})
}

// 推荐用 pug模板，支持模板集成，不需要每个页面都去写一些公共的东西[如果没用过，理解成html就行了]
const jsRegx = `src/**/*.jsx`
const htmlRegx = `src/**/*.pug`

const jsEntries = getEntries(jsRegx)
const htmlEntries = getEntries(htmlRegx)

let htmlPlugins = []
for (htmlEntry in htmlEntries) {
  const config = {
    filename: htmlEntry + '.html',
    template: htmlEntries[htmlEntry],
    chunks: ['lodash'],
    inject: true,
    hash: CONFIG.isLocal,
    cache: true,
    chunksSortMode: 'manual',
    minify: {
      removeComments: true,
      collapseWhitespace: false
    }
  }
  // 遍历判断注入每个页面对应的JS文件
  for (jsEntry in jsEntries) {
    // eg 去掉后缀后： src/demo/index === src/demo/index ，则把生成的JS文件注入到HTML中
    if (htmlEntry === jsEntry) {
      config.chunks.push(jsEntry)
    }
  }
  htmlPlugins.push(new HtmlWebpackPlugin(config))
}

module.exports = {
  mode: 'production', // 这里写成线上环境，开发环境则切换成 development
  entry: jsEntries,
  output: {
    // 静态资源文件的本机输出目录
    path: path.resolve(__dirname, 'build'),
    // 静态资源服务器发布目录
    publicPath: '/',
    // 入口文件名称配置
    filename: '[name]-[chunkhash].js',
    chunkFilename: '[name]-[chunkhash].js'
  },
  module: {
    rules: [
      {
        test: /\.pug$/,
        use: 'pug-loader'
      },
      {
        test: /\.jsx?$/,
        use: 'babel-loader?cacheDirectory',
        exclude: /node_modules/
      }
    ]
  },
  // 把生成HTML的插件放到 webpack插件中(每个HtmlWebpackPlugin插件实例，生成一个HTML文件)
  plugins: [...htmlPlugins]
}
```

## 3. 单独打包出 CSS 文件?

把每个入口 JS 文件的 CSS 抽离出来打包成单个文件。

| Webpack 版本 | 插件                          |
| ------------ | ----------------------------- |
| < 4          | `extract-text-webpack-plugin` |
| = 4          | `mini-css-extract-plugin`     |

官方建议用 `mini-css-extract-plugin`, 打包速度更快。

> 以前有人问：如何打包出多个 CSS 文件，要多个 CSS 的话，把 JS 入口变成多个，或者模块异步加载就可以了。
> 模块异步加载可以看 react-loadable [应该没拼错]

```js
const MiniCssExtractPlugin = require('mini-css-extract-plugin')

module.exports = {
  mode: 'production',
  entry: {
    /*省略*/
  },
  output: {
    /*省略*/
  },
  module: {
    rules: [
      /*省略*/
      {
        test: /\.css$/,
        use: [MiniCssExtractPlugin.loader, 'css-loader?minimize', 'postcss-loader']
      },
      {
        test: /\.less$/,
        use: [MiniCssExtractPlugin.loader, 'css-loader?minimize', 'postcss-loader', 'less-loader']
      }
    ]
  },
  plugins: [
    /*省略*/
    new MiniCssExtractPlugin({
      filename: '[name]-[contenthash].css'
    })
    /*省略*/
  ]
}
```

## 4. 项目比较大之后，如何优化构建速度

项目比较大之后，每次构建的时间就随着增长。 目前项目从代码提交，再到 ci 触发 docker，自动去构建，需要 4~7 分钟。 这个时间有点久了，有什么办法可以加快吗？

经过调研和实践发现，主要有一下几种方式可以加快构建速度

### 1. 多线程构建 happypack

nodejs 是单线程运行的，因此可以采用多线程的方式，来加快构建速度。`速度有一定的提升，但是提升不是非常明显`

```js
const os = require('os')
const HappyPack = require('happypack')

// 系统几核，就用几个线程
const happyThreadPool = HappyPack.ThreadPool({ size: os.cpus().length })

const plugins = [
  new HappyPack({
    id: 'jsx',
    threadPool: happyThreadPool,
    loaders: ['babel-loader?cacheDirectory']
  }),
  new HappyPack({
    id: 'css',
    threadPool: happyThreadPool,
    loaders: ['css-loader?minimize', 'postcss-loader']
  }),
  new HappyPack({
    id: 'less',
    threadPool: happyThreadPool,
    loaders: ['css-loader?minimize', 'postcss-loader', 'less-loader']
  })
]

module.exports = {
  mode: 'production',
  entry: {
    /*省略*/
  },
  output: {
    /*省略*/
  },
  module: {
    rules: [
      /*省略*/
      {
        test: /\.jsx?$/,
        use: 'happypack/loader?id=jsx',
        exclude: /node_modules/
      },
      {
        test: /\.css$/,
        use: [MiniCssExtractPlugin.loader, 'happypack/loader?id=css']
      },
      {
        test: /\.less$/,
        use: [MiniCssExtractPlugin.loader, 'css-loader?minimize', 'postcss-loader', 'less-loader']
      }
      /*省略*/
    ]
  },
  plugins: plugins
}
```

### 2. 单页面推荐把公共文件构建 dll 包

原理是，先把第三方公共包构建成 dll， 然后 webpack 在解析到公共包，便直接从 dll 文件里面引用，而不再构建一遍。

可以直接使用 `autodll-webpack-plugin`插件，方便开发环境构建 `React,React-Dom` 开发包,带有错误提示，线上环境则构建不带提示的包。

> 具体配置等后续单独出一篇补上

### 3. 多页面推荐使用 externals

`externals` 的作用：当 `webpack`解析到 `externals`里面配置的对象时，不打包到文件中，直接引用全局变量里面的。

如何使用？

```html
<!-- 简单的写一下 -->
<!DOCTYPE html>
<html>
  <head>
    <meta charset="utf-8" />
    <meta http-equiv="X-UA-Compatible" content="IE=edge" />
    <title>External Demo</title>
    <meta name="viewport" content="width=device-width, initial-scale=1" />
  </head>
  <body>
    <div id="app"></div>
    <!-- 引入第三方依赖包 -->
    <script src="//fe-public.licaigc.com/libs/core-js/core.min.js"></script>
    <script src="//fe-public.licaigc.com/libs/react/16.4.0/react.development.js"></script>
    <script src="//fe-public.licaigc.com/libs/react-dom/16.4.0/react-dom.development.js"></script>
    <!-- 注入的其他文件，会在这下面 -->
  </body>
</html>
```

```js
module.exports = {
  mode: 'production',
  entry: {
    /*省略*/
  },
  output: {
    /*省略*/
  },
  module: {
    rules: [
      /*省略*/
    ]
  },
  // 不打包 react 和 react-dom
  externals: {
    react: 'var React',
    'react-dom': 'var ReactDOM'
  }
}
```

```js
// 实际开发中,正常写就好了
import React, { Component } from 'react'

class Demo extends Component {
  render() {
    return <div>This is a Demo</div>
  }
}

export default Demo
```

需要注意的点：项目中如果还引用了 `react-addons-css-transition-group`库， webpack 又会把 react 引进来。 如果去查看源码，你会发现 `react-addons-css-transition-group` 子模块又依赖了 `React`。

```js
// react模块
// react/lib/ReactCSSTransitionGroup.js
var React = require('./React')
var ReactTransitionGroup = require('./ReactTransitionGroup')
var ReactCSSTransitionGroupChild = require('./ReactCSSTransitionGroupChild')
//....剩余代码忽略
```

更详细，请看着 [《彻底解决 Webpack 打包性能问题》](/posts/6476.html)

> 多页面为什么不用 dll,有兴趣可以看下面折叠起来的内容。

```js
/*
多页面，就需要考虑，把 dll 文件注入到每一个 html 中, 
HtmlWebpackPlugin 可以解决注入问题，
但是这里要注意一下， 页面多了，千万千万不要用 HtmlWebpackPlugin3.x版本， 
页面越多，构建速度越慢
（100 多个页面，不优化大概 7 分钟，用了 dll+ HtmlWebpackPlugin3.x版本注入 js 引用后，也就 26 分钟+吧。）。

多页面，插件`autodll-webpack-plugin`可以解决构建 dll，并且注入到 html 中，
但是 `autodll-webpack-plugin`这个插件不支持`HtmlWebpackPlugin4.x`，
并且看了下 `github，` 已经半年多没更新了。
*/
```

## 5. 引用的模块名是动态变化的

有一个需求，再实现一个运动活动平台时，每个活动都是由多个模块组成， 每个活动依赖的模块是动态的( 具体依赖什么模块，由运营同学在可视化后台配置)，那么如何动态加载模块？

以简单版的模块解析器为例，来看下如何使用动态的模块名。

```js
/**
 * 配置解析器
 * 该配置解析器是开发环境使用的，会把组件包一层，添加一个选中的效果
 */
import React, { Component } from 'react'

class ModuleParse extends Component {
  render() {
    const { current, modules = [] } = this.props
    return (
      <div className="module-parse">
        {modules &&
          modules.map((item, index) => {
            try {
              // 这里的模块名是变化的
              const Component = require(`../${item['component']}`).default
              if (!Component) return <p>null</p>
              return <Component key={index} {...item['data']} />
            } catch (error) {
              console.log('parse modules error:', error)
              return ''
            }
          })}
      </div>
    )
  }
}

export default ModuleParse
```

注意事项：

```js
// 这样会报错
// 没有一个大概的范围，webpack不知道打包哪里的模块
const Component = require(moduleName)

// ok
// webpack 会把 ./page/ 的目录文件都打包进来。
const Component = require('./page/' + moduleName)
```

这种引用方式有一个问题，会把所有模块打包成一个 JS 文件。 如果是 SPA 页面，这样使用问题不大。
但是对于多页面来说， 这个问题就很无语了， 命名活动只依赖的 头图和固定按钮模块, 你给我加载一个 1m 多的 js 包。

## 6. 动态引用，把所有文件打包成一个文件如何解决

把所有模块都打成一个包，是不合理的，因为一个简单的活动，根本就不需要引用那么多不需要的模块代码， 因此是否可以把`每个模块都单独打一个 js 文件`，活动则去引用它需要的模块即可，

eg: 活动 A：需要引入 module-a , module-b , module-c 的 js 文件

**解决方案：**
`bundle-loader` 可以解决这个问题。

> 但是目前还没有找到单独打包出来的 js 文件，如何设置文件名的问题？ 默认是 `1.js, 2.js`

可以参考这里：[《bundle-loader - webpack 指南》](https://webpack.toobug.net/zh-cn/chapter4/bundle-loader.html)
