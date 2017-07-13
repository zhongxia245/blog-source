---
title: github如何绑定域名
date: 2016-04-30 06:41:53
tags: 
	- hexo 
	- github 
	- 域名
---
很早之前就买了两个域名, zhongxia.win [万网买的] ,izhongxia.cn 但是由于之前是使用 BAE 来构建 Node的项目, 想绑定上域名,却一直不得法, 然后对域名解析 起了 深深的无奈. 
早上起来试了一下使用 github pages 去绑定 自己之前的域名 zhongxia.win ,既然成功了. 赶紧记录下 hexo + github pages 域名解析方法. 
<!--more-->
---

## 实现步骤

### STEP1: 添加CNAME文件

在网站的根目录下创建一个 CNAME 的文件,然后添加 域名 `[域名不要 http 或者 www 的前缀 ]`
```
zhongxia.win
```
如果是直接在GitHub网页上添加文件的话，会遇到一个问题就是在通过`hexo g -d`之后hexo会把根目录下的CNAME文件删除。

所以要把CNAME文件添加到 `/source` 目录下，这样 `hexo g -d` 之后hexo会自动把 `CNAME` 复制到 `/puclic` 目录下然后将 `/public` 路径下的内容进行复制并push到远程 `master` 分支的根目录下。



### STEP2: 上万网设置域名解析

直接上 [阿里云控制台-域名](https://dc.aliyun.com/tcparse/dns.htm?init=false&dtoken=ugM-vg1OlBao1QhoL)

最简单粗暴的就是 点击 新手引导设置, 解析网站, 然后把 服务器 ip 地址设置为这个  `192.30.252.153` 

设置完成如下图:
![](/uploads/github-hexo-yuming.png)


### STEP3: 坐等域名解析生效访问
输入 `zhongxia.win`  或者 `www.zhongxia.win`

效果如下 `昨晚刚用hexo搭建好,因此啥都没有`

![](/uploads/QQ20160430-0.png)



