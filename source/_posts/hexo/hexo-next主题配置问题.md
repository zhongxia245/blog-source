---
title: 【Hexo】next主题配置问题汇总
tags: hexo
description: next主题是一个功能完善丰富的主题，但是使用过程中可能会遇到一系列的错误，这里记录汇总下。
abbrlink: 22240
date: 2018-11-29 00:52:37
---


所有按照配置，配置完没有报错，也没有生效，请 `hexo clean ` 然后在 `hexo s` 重试一下。

## 1. algolia 免费搜索功能： Not enough rights to add an object

[《algolia 基本使用文档》](https://github.com/theme-next/hexo-theme-next/blob/master/docs/ALGOLIA-SEARCH.md)

这里需要使用 admin 的 apikey， 除了 window 命令行使用 search-only apikey。

```bash
Not enough rights to add an object

$ export HEXO_ALGOLIA_INDEXING_KEY=Search-Only API key # Use Git Bash
# set HEXO_ALGOLIA_INDEXING_KEY=Search-Only API key # Use Windows command line
$ hexo clean
$ hexo algolia

```

## 2. 百度，谷歌站点地图收录

[《hexo 教程:搜索 SEO+阅读量统计+访问量统计+评论系统(3)》](http://fangzh.top/2018/2018090918/)

## 3. 留言功能

这个留言信息存放在 `github` 仓库的 `issue` 里面
[【Hexo】三步搞定留言板功能（Gitalk）](http://www.izhongxia.com/posts/41249.html)

---

如果想使用免登录的留言功能， 看这里。

效果图大概这样，
![](http://static.izhongxia.com/pictures/479553aa-62b9-411b-94fd-bbcbda204c42.jpeg)

具体如何使用，参考[《valine官网》](https://valine.js.org/quickstart.html)

hexo next 主题里面，默认支持这个留言功能，但是需要自己申请一个 LearnCloud 的帐号， 具体看 [《valine官网》](https://valine.js.org/quickstart.html)

## 4. 代码折叠

[《Hexo next 博客添加折叠块功能添加折叠代码块》](https://blog.rmiao.top/hexo-fold-block/)

{% fold 点击显/隐内容 %}
something you want to fold, include code block.
{% endfold %}

## 5. 在右上角或者左上角实现 fork me on github

[《GitHub Ribbons》](https://blog.github.com/2008-12-19-github-ribbons/)

打开 `themes/next/layout/_layout.swig` 文件，把代码复制到`<div class="headband"></div>`下面。

## 6. 本地搜索的问题

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

## 7. 加入心形鼠标

```js
// 新建一个文件： themes/next/source/js/src/love.js
!function(e,t,a){function n(){c(".heart{width: 10px;height: 10px;position: fixed;background: #f00;transform: rotate(45deg);-webkit-transform: rotate(45deg);-moz-transform: rotate(45deg);}.heart:after,.heart:before{content: '';width: inherit;height: inherit;background: inherit;border-radius: 50%;-webkit-border-radius: 50%;-moz-border-radius: 50%;position: fixed;}.heart:after{top: -5px;}.heart:before{left: -5px;}"),o(),r()}function r(){for(var e=0;e<d.length;e++)d[e].alpha<=0?(t.body.removeChild(d[e].el),d.splice(e,1)):(d[e].y--,d[e].scale+=.004,d[e].alpha-=.013,d[e].el.style.cssText="left:"+d[e].x+"px;top:"+d[e].y+"px;opacity:"+d[e].alpha+";transform:scale("+d[e].scale+","+d[e].scale+") rotate(45deg);background:"+d[e].color+";z-index:99999");requestAnimationFrame(r)}function o(){var t="function"==typeof e.onclick&&e.onclick;e.onclick=function(e){t&&t(),i(e)}}function i(e){var a=t.createElement("div");a.className="heart",d.push({el:a,x:e.clientX-5,y:e.clientY-5,scale:1,alpha:1,color:s()}),t.body.appendChild(a)}function c(e){var a=t.createElement("style");a.type="text/css";try{a.appendChild(t.createTextNode(e))}catch(t){a.styleSheet.cssText=e}t.getElementsByTagName("head")[0].appendChild(a)}function s(){return"rgb("+~~(255*Math.random())+","+~~(255*Math.random())+","+~~(255*Math.random())+")"}var d=[];e.requestAnimationFrame=function(){return e.requestAnimationFrame||e.webkitRequestAnimationFrame||e.mozRequestAnimationFrame||e.oRequestAnimationFrame||e.msRequestAnimationFrame||function(e){setTimeout(e,1e3/60)}}(),n()}(window,document);

```

```html
<!-- 把这段代码加入 themes/next/layout/_layout.swig 的 </body> 前 -->
<script type="text/javascript" src="/js/src/love.js"></script>
```

## 8. 文章置顶

```bash
# 安装依赖
# hexo-generator-index 这个模块有bug，无法置顶
cnpm install hexo-generator-index-pin-top --save

---
title: 【Hexo】next主题配置问题汇总
tags: hexo
description: next主题是一个功能完善丰富的主题，但是使用过程中可能会遇到一系列的错误，这里记录汇总下。
abbrlink: 22240
date: 2018-11-29 00:52:37
top: true   # 设置置顶，多个置顶按照时间排序
---

```


## 9. 新增近期文章

前几天看到别人的博客有个近期文章版块，感觉挺好，于是就想给自己的博客也加这么个功能。由于使用的是next主题，而next默认是没有这个版块的，那就自己搞一个吧。废话不多说，直接上代码，挺简单的。

```html
{% if theme.recent_posts %}
    <div class="links-of-blogroll motion-element {{ "links-of-blogroll-" + theme.recent_posts_layout  }}">
      <div class="links-of-blogroll-title">
        <!-- modify icon to fire by szw -->
        <i class="fa fa-history fa-{{ theme.recent_posts_icon | lower }}" aria-hidden="true"></i>
        {{ theme.recent_posts_title }}
      </div>
      <ul class="links-of-blogroll-list">
        {% set posts = site.posts.sort('-date') %}
        {% for post in posts.slice('0', '5') %}
          <li>
            <a href="{{ url_for(post.path) }}" title="{{ post.title }}" target="_blank">{{ post.title }}</a>
          </li>
        {% endfor %}
      </ul>
    </div>
{% endif %}
```

将此代码贴在`next/layout/_macro/sidebar.swig`中的`if theme.links`对应的endif后面，就ok了，是不是很简单。。。。
为了配置方便，在主题的`_config.yml`中添加了几个变量，如下：
```bash
recent_posts_title: 近期文章
recent_posts_layout: block
recent_posts: true
```