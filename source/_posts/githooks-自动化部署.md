---
title: githooks 自动化部署
date: 2017-07-14 02:18:54
tags: git
categories: 部署、运维
---
## 一、写在前面
虽然知道自动化部署，也一直在用，但是自己去搭建一个自动化部署的环境，还是从未有过的。 
近期刚好准备搭建一个blog,然后就尝试一下 git hooks 实现自动化部署， 代码直接部署在阿里云服务器，并且使用 nginx 部署web程序。
这里刚好记录一下，如何使用 git  实现自动化部署。

## 二、git hooks 实现自动化部署

### 2.1 了解概念
1. 了解 git 的使用
2. 需要一台线上服务器（用来部署网站），一台开发机（自己的笔记本或电脑）

### 2.2 创建裸仓库
```bash 
ssh zhongxia@xxx.xxx.xxx.xxx              # 登录线上服务器
mkdir git                                 # 创建一个目录
mkdir blog.git dev-blog release-blog      # 创建几个目录（裸仓库目录和需要部署代码的文件目录
cd blog.git
git init --bare                           # 初始化裸仓库
```

>注意 不能创建文件名叫做 和 仓库名一样的,比如 blog。 否则提交上来的时候，无法把代码自动获取过来。

### 2.3 编写 githooks 文件
可以参照 blog.git/hooks/下的文件来进行参考
```bash
cd blog.git/hooks
cp post-receive.example post-receive
vim post-receive
```

文件内容改成如下
```bash
DIR_ONE=/home/zhongxia/git/dev-blog
DIR_TWO=/home/zhongxia/git/release-blog
# 开发环境
git --work-tree=${DIR_ONE} clean -fd
git --work-tree=${DIR_ONE} checkout --force
# 测试环境
git --work-tree=${DIR_TWO} clean -fd
git --work-tree=${DIR_TWO} checkout --force
# OR 其他操作
```

### 2.4 开发机创建一个仓库
开发机创建一个 git 仓库，然后设置 git remote 地址
```bash (Max Os)  
mkdir blog
git init
git remote add deploy zhongxia@xxx.xxx.xxx.xxx:home/zhongxia/git/blog.git
touch README.md
git push 
```

### 2.5 上线上服务器，看效果
此时，在服务器上，可以看到 dev-blog 和 release-blog 文件夹里面已经多了一个 README.md 文件出来


## 三、总结
git hooks 还可以有其他应用，比如 pre-commit 在提交前触发，验证代码风格，或者规范化 commit 的 msg 信息 等等...

这里只是简单的实现了一下自动化部署（不太完整， 还需要一个 nginx 来把dev-blog, release-blog 部署起来）


## 四、参考文章
1. [hexo常用命令笔记](https://segmentfault.com/a/1190000002632530#articleHeader12)