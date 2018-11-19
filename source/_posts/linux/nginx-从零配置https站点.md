---
title: 5分钟让你的网站支持 https
tags: nginx
description: 讲解配置 https 的流程，从哪里申请 https 证书，到 nginx 配置，以及可能遇见的问题和解决方案。
abbrlink: 44912
date: 2018-01-05 14:00:27
---

# 从零配置一个 https 网站

![](https://ws1.sinaimg.cn/large/006tNc79gy1fn5oylkmdzj30j6034748.jpg)

## 一、申请一个 https 证书

可以去腾讯云申请一个免费的证书(DV SSL 证书)

https://console.qcloud.com/ssl

![](https://ws3.sinaimg.cn/large/006tNc79gy1fn5om1op5yj31940skwgd.jpg)

## 二、Nginx 配置 Https 解析

```bash
# 登录云服务器
ssh xxxx@xxx

# 查看nginx位置
whereis nginx

# 修改配置nginx配置文件
vim nginx.conf

# 验证配置文件是否正常
nginx -t

# 重启nginx
nginx -s reload
```

### 2.1 修改 nginx.conf

配置 https 解析，并且把 80 端口自动重定向到 443 端口

```
user  zhongxia;
worker_processes  1;

events {
    worker_connections  1024;
}

http {
    include       mime.types;
    default_type  application/octet-stream;

    sendfile        on;

    #keepalive_timeout  0;
    keepalive_timeout  65;

    gzip  on;

    # http自动重定向到https
    server {
        listen 80;
        server_name www.izhongxia.com;
        rewrite ^(.*)$  https://$host$1 permanent;
    }

    server {
        listen 443;
        server_name www.izhongxia.com;          #填写绑定证书的域名
        ssl on;
        ssl_certificate     /usr/local/nginx/conf/certificate/1_www.izhongxia.com_bundle.crt;   # 证书地址
        ssl_certificate_key /usr/local/nginx/conf/certificate/2_www.izhongxia.com.key;   # 证书地址
        ssl_session_timeout 5m;
        ssl_protocols TLSv1 TLSv1.1 TLSv1.2;        #按照这个协议配置
        ssl_ciphers ECDHE-RSA-AES128-GCM-SHA256:HIGH:!aNULL:!MD5:!RC4:!DHE;#按照这个套件配置
        ssl_prefer_server_ciphers on;

        location / {
            root   /home/zhongxia/git/release-blog/;
            index  index.html index.htm;
        }
    }
}
```

## 三、可能遇见的问题

### 3.1 nginx 找不到 ssl 模块

```
# 找到nginx的源码，重新构建
--with-http_ssl_module

# 如果找不到源码，重新下载一个
# 查看 nginx版本，如果为1.7.4
nginx -V
# 则下载源码下来
wget http://nginx.org/download/nginx-1.7.4.tar.gz

# 解压,并进入目录
./configure --with-http_ssl_module

make

# 会覆盖旧的版本, 可以进入 /usr/local/nginx/sbin rename nginx => ngxin.old
make install  
```

### 3.2 刚配置好后，https 可以访问网站，但是 chrome 提示不安全

在其他浏览器访问一下，在来 chrome 访问，然后居然可以了。 具体原因未知
