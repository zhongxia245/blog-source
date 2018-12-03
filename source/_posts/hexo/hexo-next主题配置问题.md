---
title: 【Hexo】next主题配置问题汇总
tags: hexo
description: next主题是一个功能完善丰富的主题，但是使用过程中可能会遇到一系列的错误，这里记录汇总下。
abbrlink: 22240
date: 2018-11-29 00:52:37
---

## 一、错误汇总

### 1. algolia 免费搜索功能： Not enough rights to add an object

[《algolia 基本使用文档》](https://github.com/theme-next/hexo-theme-next/blob/master/docs/ALGOLIA-SEARCH.md)

这里需要使用 admin 的 apikey， 除了 window 命令行使用 search-only apikey。

```bash
Not enough rights to add an object

$ export HEXO_ALGOLIA_INDEXING_KEY=Search-Only API key # Use Git Bash
# set HEXO_ALGOLIA_INDEXING_KEY=Search-Only API key # Use Windows command line
$ hexo clean
$ hexo algolia

```

### 2. 百度，谷歌站点地图收录

[《hexo 教程:搜索 SEO+阅读量统计+访问量统计+评论系统(3)》](http://fangzh.top/2018/2018090918/)

### 3. 留言功能

[【Hexo】三步搞定留言板功能（Gitalk）](http://www.izhongxia.com/posts/41249.html)

### 4. 代码折叠

[《Hexo next 博客添加折叠块功能添加折叠代码块》](https://blog.rmiao.top/hexo-fold-block/)

{% fold 点击显/隐内容 %}
something you want to fold, include code block.
{% endfold %}

### 5. 在右上角或者左上角实现 fork me on github

[《GitHub Ribbons》](https://blog.github.com/2008-12-19-github-ribbons/)

打开 `themes/next/layout/_layout.swig` 文件，把代码复制到`<div class="headband"></div>`下面。

### 6. 本地搜索的问题

使用 `hexo-generator-searchdb` 插件生成的文件，必须为 json 格式，如何 ajax 请求的时候会报 `parseerror。`

就是点击搜索的时候，页面会一直在 loading，不会出现弹窗。

只需要改 hexo 根目录下的 \_config.yml 的配置， 把 `path` 改成 `search.json`即可

```yaml
# local search
# 生成的文件如果是xml，则ajax请求解析则会报错
search:
  path: search.json
  field: post
  format: html
  limit: 10000
```
