---
title: 【Hexo】三步搞定留言板功能（Gitalk）
tags: hexo
description: >-
  hexo 有很多留言板功能，但是这篇文章主要讲解的是 Gitalk ，它其实使用 Git 仓库的 Issue 来进行留言的管理，界面比较简洁美观。
  如果有缺点的话，那么就是它需要有 github 帐号，才能留言哈。
abbrlink: 41249
date: 2018-11-18 00:55:45
---

Hexo 有很多留言板的第三方插件（多说、disqus、Gitment、Gitalk 等等），但是这里主要讲解怎么使用 Gitalk， 因为它比较好看，移动端支持友好(Gitment 移动端稍微差点)。

## 零、总结

整理一下使用 Gitalk 的步骤，可以大概分为一下几步：

1. 申请 GitHub 帐号，创建 GitHub Application 应用, 拿到应用的 `clientID` `clientSecret` _[《来，点这里》](https://github.com/settings/applications/new)_

2. 引入 gitalk 的代码

3. 配置 gitalk 的参数

4. 搞定~

## 一、一步一步来，不要急

### 1.1 申请 GitHub 帐号，创建 GitHub Application

目的就一个，拿到 `clientID` `clientSecret`, 说白了就是有权限去操作的 Git 仓库，给你创建 Issue 来放留言内容，_[《来，点这里》](https://github.com/settings/applications/new)_

大概这样
![](https://i.loli.net/2018/11/18/5bf0585749e7f.png)

> 注意事项： `authorization callback URL` 需要填写你部署上线后，博客的地址
> 作用：创建完成后拿到的密钥，只有在这个域名下才能用
> `其他参数随便`

创建好了后,红色的就是后面需要的.

![](https://i.loli.net/2018/11/18/5bf0549e0d317.png)

### 1.2 引入 gitalk 的代码

引入 Gitalk 的代码有很多种方式，最终只要保证这段代码在你的页面中即可。

这里讲常规用法。

在 `themes/[theme_name]/layout/_script/_comments/` 目录下，创建`gitalk.swig`文件。

```html
<!-- gitalk.swig -->
<link href="https://cdn.bootcss.com/gitalk/1.4.0/gitalk.min.css" rel="stylesheet" />
<script src="https://cdn.bootcss.com/gitalk/1.4.0/gitalk.min.js"></script>
<script src="https://cdn.bootcss.com/blueimp-md5/2.10.0/js/md5.min.js"></script>
<script type="text/javascript">
  var gitalk = new Gitalk({
    clientID: '{{ theme.gitalk.ClientID }}',
    clientSecret: '{{ theme.gitalk.ClientSecret }}',
    repo: '{{ theme.gitalk.repo }}',
    owner: '{{ theme.gitalk.owner }}',
    admin: ['{{ theme.gitalk.adminUser }}'],
    id: md5(location.pathname),   // ISSUE：location.href 限制50个字符 (应该是Issue 内容存数据库的标识， 具体在页面上无感)
    labels: '{{ theme.gitalk.labels }}'.split(',').filter(l => l),  // 需要的 labels需要一个数组，否则会报错
    perPage: {{ theme.gitalk.perPage }},
    pagerDirection: '{{ theme.gitalk.pagerDirection }}',
    createIssueManually: {{ theme.gitalk.createIssueManually }},
    distractionFreeMode: {{ theme.gitalk.distractionFreeMode }}
  })
  gitalk.render('gitalk-container')
</script>
```

把 `gitalk.swig` 引用进来。

```html
<!-- themes/[theme_name]/layout/_script/comments.swig -->
{% include '_comments/gitalk.swig' %}
```

> 这里不同的主题可能不太一样，如果没有找到这个 `comments.swig` ， 可以直接放到`themes/polarbear/layout/_layout.swig` 的 `body 结束标签前`

> 代码里面的一些参数，在下面在统一说下作用。

### 1.3 添加配置

上面用到很多参数，这些参数正常来说，放到 \_config.yaml 里面配置比较好， 后续修改，则不需要去改代码。

```yaml
# theme/[theme_name]/_config.yaml 添加配置
# ===========================================
# Comments Settings
# ===========================================
gitalk:
  enable: true # 是否启动
  ClientID: Your ClientID # 之前的Client ID
  ClientSecret: Your ClientSecret # 之前的Client Secret
  repo: blog_comments # 留言板内容需要存放的仓库名称
  owner: zhongxia245 # 你 github 的帐号 eg: www.github.com/zhongxia245 ,帐号就是245
  adminUser: zhongxia245 # 管理员用户
  labels: # issue 的标签
    - gitalk
  perPage: 15 # 每页展示条数
  pagerDirection: last # 排序方式是从旧到新（first）还是从新到旧（last）
  createIssueManually: false #如果当前页面没有相应的 isssue ，且登录的用户属于 admin，则会自动创建 issue。如果设置为 true，则显示一个初始化页面，创建 issue 需要点击 init 按钮。
  distractionFreeMode: false #是否启用快捷键(cmd|ctrl + enter) 提交评论.
```

### 1.4 部署上线，感受下

到这里就搞定了。

![](https://i.loli.net/2018/11/18/5bf05c9c5bd46.png)

## 三、可能遇见的坑

如果顺利的话，你可能就不需要看这个了，因为下面的这两个问题，在上面的的代码和配置中已经给解决了。

[_如果真的不行，那么请留言交流_](#comments)

### 1. id 字段太长，导致 初始化留言板的时候 `Validation Failed(422)` 错误

HTTP 编码为 422，大家应该都已经知道了这个问题的原因：文章的 URL 过长，生成 issue 时超过了 label 的长度限制

因此引入 Md5，对 id 的值进行一个加密，然后就控制在 50 个字符内。

```js
...
id: md5(location.pathname),
...
```

### 2. Error: i.concat(o).join is not a function. (In 'i.concat(o).join(",")', 'i.concat(o).join' is undefined)

这个应该是模板渲染的时候， 加上引号导致 labels 变成一个字符串， 然后没有 concat 功能

```js
...
 labels: '{{ theme.gitalk.labels }}'.split(',').filter(l => l),  // 需要的 labels需要一个数组，否则会报错
...
```

## 终、参考文章

1. [《Hexo 中 Gitalk 配置使用教程-可能是目前最详细的教程》](https://iochen.com/2018/01/06/use-gitalk-in-hexo/)
2. [《处理 Gitalk 中由于文章 URL 过长导致的 Validation Failed(422)》](<https://priesttomb.github.io/%E6%97%A5%E5%B8%B8/2018/02/12/%E5%A4%84%E7%90%86Gitalk%E4%B8%AD%E7%94%B1%E4%BA%8E%E6%96%87%E7%AB%A0URL%E8%BF%87%E9%95%BF%E5%AF%BC%E8%87%B4%E7%9A%84Validation-Failed(422)/>)
