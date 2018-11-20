---
title: webpack实战场景系列
tags: webpack
description: '本系列主要记录webpack在实际项目开发中，都有哪些实际的应用，单页面开发，多页面开发，大项目构建优化，按需加载，动态加载，动态加载并且每个引用单独打包成一个文件等知识的记录'
abbrlink: 44724
date: 2018-11-20 22:22:18
---

本系列主要记录自己从 16 年到 18 年，项目开发中 Webpack 的一些具体应用。记录只记录核心配置代码，并不会记录完成的代码（简单的配置没有意义）。

本系列主要记录 webpack 在实际项目开发中，都有哪些实际的应用，单页面开发，多页面开发，大项目构建优化，按需加载，动态加载，动态加载并且每个引用单独打包成一个文件等知识的记录

## 场景目录

> webpack 版本：4.26.0 ， 项目以 React 为例。

记录一下，在项目实际开发中遇到的一些场景

1. 单页面开发如何用?
2. 多页面开发?
3. 单独打包出 CSS 文件?
4. webpack 热更新?
5. 项目很大后，如何优化构建速度？
6. 项目很大后，如何按需异步加载?
7. 如何打包动态文件路径的文件？
8. 如果实现一个活动平台，如何动态引用模块，并且分开打包？

## 1. 单页面开发如何用?

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

```javascript
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

```javascript
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

## 4. webpack 热更新?

TODO

## 5. 项目很大后，如何优化构建速度？

TODO

## 6. 项目很大后，如何按需异步加载?

TODO

## 7. 如何打包动态文件路径的文件？

TODO

## 8. 如果实现一个活动平台，如何动态引用模块，并且分开打包？
