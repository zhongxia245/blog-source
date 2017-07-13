---
title: hexo部署主题使用
date: 2016-04-30 08:03:31
tags: hexo
---
大概一年前吧,就玩过 hexo, 但是那个时候没有去具体的优化,并使用起来, 只是简单的扔到github pages 上就完事了. 然后觉得自带的主题不是很喜欢, 页没有去修改,慢慢着就遗忘在那边了.
昨晚突然就看到一篇文章,部署 hexo 的, 于是就按照教程来部署了以下. 发现, 哎呦呦, 页面还是很赞的哦.
<!--more-->
---
## 分享下部署 hexo 站点用到的一些教程吧
### 搭建相关
1. [HEXO+Github,搭建属于自己的博客](http://www.jianshu.com/p/465830080ea9)
2. [hexo,你的博客](http://ibruce.info/2013/11/22/hexo-your-blog/?utm_source=tuicool)

### 主题相关
1. [hexo next主题](http://theme-next.iissnan.com/getting-started.html#description-setting)
2. [动动手指，NexT主题与Hexo更搭哦（基础篇）](http://www.arao.me/2015/hexo-next-theme-optimize-base/#hexo_NexT主题首页title的优化)
3. [动动手指，不限于NexT主题的Hexo优化（SEO篇）](http://www.arao.me/2015/hexo-next-theme-optimize-seo/)

---

## FAQ

### Q: 点击 标签 tags , 报404错误,找不到页面
由于默认是没有创建 tags 的index.html 页面. 创建方法很简单, 只需要  `hexo new page tags` 新建一个 tag 页面

#### 1. tag页面内容
```
title: Tagcloud
date: 2014-12-22 12:39:04
type: tags
comments: false # 关闭这个页面的多说或者 Disqus 评论
---
```

#### 2. 主题配置文件(theme/_config.yml) 新增菜单
```
menu:
  home: /
  archives: /archives
  tags: /tags
```