---
title: Nginx简易教程
date: 2017-11-02 11:33:39
tags: Nginx
categories: 部署、运维
---
# Nginx 入门教程
简单的说明了如何使用，并且说了几种 nginx 常用的情况， 如果只是常规使用 nginx， 这个就够用了。 

## 一、正向代理和反向代理
![image](http://upload-images.jianshu.io/upload_images/3101171-71de739352457081.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240&_=5945200)

## 二、常用 nginx 命令
```bash
nginx -s stop       快速关闭Nginx，可能不保存相关信息，并迅速终止web服务。
nginx -s quit       平稳关闭Nginx，保存相关信息，有安排的结束web服务。
nginx -s reload     因改变了Nginx相关配置，需要重新加载配置而重载。
nginx -s reopen     重新打开日志文件。
nginx -c filename   为 Nginx 指定一个配置文件，来代替缺省的。
nginx -t            不运行，而仅仅测试配置文件。nginx 将检查配置文件的语法的正确性，并尝试打开配置文件中所引用到的文件。
nginx -v            显示 nginx 的版本。
nginx -V            显示 nginx 的版本，编译器版本和配置参数。
```

## 三、Nginx 实战配置
>具体配置代码去文章里面找，这几个实战的配置，在实际使用中，还是比较常用的。

1. http反向代理配置
2. 负载均衡配置
3. 网站有多个webapp的配置
4. https反向代理配置
5. 静态站点配置
6. 跨域解决方案

## 四、学习文章列表
[《nginx简易教程》](http://www.cnblogs.com/jingmoxukong/p/5945200.html)

