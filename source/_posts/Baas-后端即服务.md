---
title: Baas 后端即服务
date: 2016-04-30 12:30:38
tags: Baas 
categories: Baas
---
这两天才原来 Baas 这种技术, 原来依赖实在是孤落寡闻了, 发现这个东西, 就像发现新大陆一样很兴奋. 主要是考虑到以前要做一个 app, 或者是 web 项目, 还需要自己去搭建后端环境, 最简单的还需要使用 Node+Mongose 实现一个最基本的CRUD. 然后在去实现前端的相关东西. 
**现在, 有了Baas,我们就可以纯粹的做我们前端相关的东西.** 
<!--more-->
---

## Baas

### 是什么?
> [百度百科]BaaS（后端即服务：Backend as a Service）公司为移动应用开发者提供整合云后端的边界服务。

>MBaaS（移动后端服务系统：Mobile Backend as a Service）、SaaS（软件即服务：Software as a Service）、IaaS（基础设施即服务：Infrastructure as a Service）和PaaS（平台即服务：Platform as a Service）早已为业界人士所熟悉 ，BaaS生态系统正从一个小众垂直领域迅速成为非常重要的行业环节

### 有什么用?
> 提供`后端存储`,`实时通讯`,`数据推送`,`短信发送`,`数据分析`,`IM`,`文件上传`等的相关功能.

> 通俗的说法就是, 服务端接口已经有人帮你写好了, 你不用纠结谁帮你写后端,或者去搭建后端环境,编写接口啦.

### 如何选择一个优秀的Baas?
> [占位,后期补充]




---
### Bass的服务厂商

目前只用过 leanCloud [早期名字:AVOS Cloud] , 野狗. 这两个都还不错. JavaScript SDK 使用非常简单. 但是野狗的访问速度好像快一点, 但是保存数据一个应用好像就是一个 大的Object 去保存,需要规划好数据如何存, 否则会很容易把现有数据覆盖的.

LeanCloud
![](/uploads/QQ20160430-0%E5%89%AF%E6%9C%AC.png)

野狗
![](/uploads/QQ20160430-1.png)

#### 后台数据存储
1. StackMob Product | StackMob
2. Parse Products
3. AVOS Cloud AVOS Cloud 
4. Bmob Bmob移动后端云服务平台

#### 应用数据分析
1. 友盟 友盟-专业的移动开发者服务平台
2. TalkingData TalkingData-专业的无线互联网数据服务平台
3. 魔方 魔方-移动应用服务平台
4. AVOS Cloud Analytics 功能 - AVOS Cloud

#### 移动终端测试
1.Testin Testin云测
2.班墨云测试 全球首款智能云测试系统
3.DroidPilot Android自动化测试工具DroidPilot
4.摩测 e世博,e世博注册首选平台

#### 应用发布
1.一键云 关于我们
2.抓猫网 抓猫移动广告聚合优化平台

#### 消息推送
1. 极光推送 JPush极光推送
2. 个推 个推开放平台
3. AVOS Cloud Push 功能 - AVOS Cloud

#### 信息识别
1.语义云 首页 (自然语义)
2.慧眼开发平台 http://smarkeye.mongtx.com/ (图像)
3.AngelEyes http://www.angeleyes.it/ (图像)
4.Face++ Face++ 最好的免费人脸识别云服务 (人脸)
5.Face-API http://faceapi.cn/ (人脸)

#### 应用内广告
1.掌淘联盟 http://appgo.cn/
2.抓猫网 抓猫移动广告聚合优化平台

#### 未分类
1.短信宝 短信宝-为中小网站提供专业的短信服务 (短信开放接口)
2.亲加 亲加 | 移动应用沟通解决方案 (实时语音)