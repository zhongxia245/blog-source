---
title: 【linux】SSH 免密登录
tags: linux
description: 免密登录，顾名思义，以后 SSH 连接上服务器，就不需要密码了。更多是方便一些自动化脚本的使用。比如一键提交代码部署。
abbrlink: 51409
date: 2018-11-18 15:13:57
---

免密登录，顾名思义，以后 SSH 连接上服务器，就不需要密码了。更多是方便一些自动化脚本的使用。比如一键提交代码部署。

## 零、总结

> 准备工作： 本地机器(serverA) + 一台服务器机子(serverB) （这里机子可以随意）

1. 本地机器生成密钥和公钥（id_rsa idrsa.pub）
2. 通过 scp 把公钥上传到服务器，然后放到 authorized_keys 文件中
3. 设置 ~/.ssh 700 权限 和 ~/.authorized_keys 权限 600 权限
4. 本地尝试登录下服务器（不用输入密码则成功）
5. 搞定~~

## 一、一步一步来，不要急

### 1. 生成密钥

这里只要执行 `ssh-keygen` 命令，然后一路回车即可。

```bash
╭─zhongxia@zhongxiadeMacBook-Pro.local ~
[zhongxia@serverA ~]$  ssh-keygen
Generating public/private rsa key pair.
Enter file in which to save the key (/Users/zhongxia/.ssh/id_rsa):
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in demo.
Your public key has been saved in demo.pub.
The key fingerprint is:
SHA256:UhMHkGRg9AX2EZfOEIvxrxFvZ9Uy5yPM8tKXPbIisrc zhongxia@zhongxiadeMacBook-Pro.local
The key's randomart image is:
+---[RSA 2048]----+
|   .+oB+B+o.     |
|   . +.*.*.    . |
|      o B+    + o|
|       . =o o. = |
|      . S +.o+ ..|
|       . + o+ . +|
|        .  . + +.|
|       . o .. + .|
|       .+Eo ..   |
+----[SHA256]-----+
```

### 2. 上传密钥

这里先看一下生成的密钥和公钥，然后在用 `scp` 上传。

```bash
[zhongxia@serverA ~]$ cd ~/.ssh

[zhongxia@serverA ~]$ ls -al

-rw-------    1 zhongxia  staff   1.6K 11  1  2016 id_rsa
-rw-r--r--    1 zhongxia  staff   407B 11  1  2016 id_rsa.pub

# 这里的 serverB 换成自己的 ip 地址 或者域名， zhongxia 换成你服务器的用户名
[zhongxia@serverA ~]$ scp ~/.ssh/id_rsa.pub zhongxia@serverB:/home/zhongxia/id_rsa.pub

#  登录下服务器
[zhongxia@serverA ~]$ ssh zhongxia@serverB

# 刚才上传到服务器的公钥文件   >> ： 把公钥文件内容追加到 authorized_keys这个文件中（没有这个文件会自动创建）
[zhongxia@serverB ~]$ cat id_rsa.pub >> ~/.ssh/authorized_keys
```

### 3. 设置 ~/.ssh 和 ~/.ssh/authorized_keys 的权限

SSH 免密登录，authorized_keys 文件的权限必须为 600，ssh 目录权限必须为 700，手动修改权限

```bash
[zhongxia@serverB ~]$ chmod 700 ~/.ssh
[zhongxia@serverB ~]$ chmod 600 ~/.ssh/authorized_keys
```

### 4. 免密登录测试

```bash
ssh zhongxia@serverB

# Last login: Sun Nov 18 14:53:37 2018 from xxx.xxx.xxx.xx
# Welcome to Alibaba Cloud Elastic Compute Service !
```

搞定~~~

## 二、可能遇到的问题

一路没有碰到问题，自然是好，但是往往有时候，错误总是来的那么突然。

```bash
# 提供两个定位错误的方式
# 1. 先用密码登录服务器,查看安全日志
su tail -f /var/log/secure

# 2. 在本机机器，使用 ssh的日志
ssh -vvv zhongxia@serverB
```

### 1. SSH Authentication Refused: Bad Ownership or Modes for Directory

使用方式 1 定位问题，你可能发现，日志中有这个错误，有的话， 问题就是 ssh 目录 和 authorized_keys 没有设置好。 认真在检查检查， 如果还是报错的话。
参考下[《这篇文章》](https://www.daveperrett.com/articles/2010/09/14/ssh-authentication-refused/)

### 2. pam_unix(sshdsession) session closed for user

使用方式 1 定位，上个问题搞定了，你可能发现还有这个问题。

这个问题，我在实现免密登录的时候，发现是因为我本地有多个密钥，不同的服务器使用不同的密钥，然后没有配置好 serverB 服务器使用的密钥，导致这个问题。

> 如果是你本机也是有很多个密钥，那么就检查你不同服务器配置不同密钥的方式是不是对的。 不想检查的话，就把本机 id_rsa.pub 的公钥扔到服务器即可。

在阿里云社区找到 这边文章[《SSH 连接时出现如下错误：pam_unix(sshdsession) session closed for user》](https://help.aliyun.com/knowledge_detail/41482.html)， `用户修改了默认的shell导致`。

```bash
cat /etc/passwd | grep user_name
test:x:1000:1000::/home/user_name:/sbin/nologin
```

## 三、参考文章

1. [SSH 免密登录原理及配置](https://my.oschina.net/binxin/blog/651565)
2. [SSH Authentication Refused: Bad Ownership or Modes for Directory](https://www.daveperrett.com/articles/2010/09/14/ssh-authentication-refused/)
3. [SSH 连接时出现如下错误：pam_unix(sshdsession) session closed for user](https://help.aliyun.com/knowledge_detail/41482.html)
