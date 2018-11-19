---
title: Centos服务器前端环境安装
tags: linux
description: >-
  身为一个前端开发人员，需要安装的东西，有以下几个。 Nodejs , Npm , git , nginx , redis, mysql ,
  mongodb，本文则主要讲解在 centos 下，前端环境的安装。
abbrlink: 26092
date: 2018-05-23 15:09:03
---

身为一个前端开发人员，需要安装的东西，有以下几个。 Nodejs , Npm , git , nginx , redis, mysql , mongodb

## 一、NodeJs

参考文章：

- [《如何在 CentOS7 中安装 Nodejs》](https://www.cnblogs.com/lpbottle/p/7733397.html)
- [《NodeJs 官网下载》](https://nodejs.org/zh-cn/download/)

```bash
# 下载包
wget https://nodejs.org/dist/v8.11.2/node-v8.11.2-linux-x64.tar.xz

# 解压
tar xvf node-v8.11.2-linux-x64.tar.xz

cd  node-v8.11.2-linux-x64

# 安装必要的编译包
yum install gcc gcc-c++

# 编译，打包
./configure
make

# 安装
make install

node -v

npm -v
```

## 二、Git

```bash
yum install git
```

## 三、 Nginx

参考文章

- [《CentOS 7 下安装 Nginx》](https://www.linuxidc.com/Linux/2016-09/134907.htm)

```bash
# 安装依赖
yum install gcc-c++

yum install -y pcre pcre-devel

yum install -y zlib zlib-devel

yum install -y openssl openssl-devel

# 下载nginx包
wget -c https://nginx.org/download/nginx-1.10.1.tar.gz

# 解压nginx
tar -zxvf nginx-1.10.1.tar.gz

cd nginx-1.10.1

# 编译 安装
make

make install

whereis nginx

# 设置别名，任何地方都可以访问
ln /usr/local/nginx/sbin/nginx /usr/local/bin/nginx
```

### 3.1 ECS 服务器开放 80 端口，不然外网访问不了

1.  ECS 服务器 => 安全组 =》 配置规则 =》 添加安全组规则
2.  入方向 配置 80/80 端口
3.  如果还不行，则在配置一个出方向的规则

## 四、redis

参考文章：

- [《linux 下 yum 安装 redis 以及使用》](https://www.cnblogs.com/wiseroll/p/7061673.html)
- [《访问远程 Redis 服务。Connect to Remote Redis Server》](https://blog.csdn.net/kinginblue/article/details/51619445)

```bash
# 安装redis
yum install redis

# 启动redis
service redis start

# 查看redis是否开启
ps -ef | grep redis

# 连接redis
redis-cli

> set name zhongxia
> get name   // =>  zhongxia

# 远程连接redis服务器
whereis redis    // => redis: /etc/redis.conf

vim /etc/redis.conf

# 把17.0.0.1 改成0.0.0.0 表示不限制ip地址或者域名访问
bind 127.0.0.1  =》 bind 0.0.0.0

# 重启redis服务器
service redis stop

service redis start


# 远程连接redis
redis-cli -h ip地址 -p 6379
```

### 4.1 配置安全组端口

1.  ECS 服务器 => 安全组 =》 配置规则 =》 添加安全组规则
2.  入方向 配置 6379/6379 端口
3.  如果还不行，则在配置一个出方向的规则

## 五、mysql

参考文章：

- [《CentOS 7.0 下使用 yum 安装 MySQL》](https://www.linuxidc.com/Linux/2016-09/134940.htm?from=groupmessage)

```bash
# 1. 下载mysql的repo源
$ wget http://repo.mysql.com/mysql-community-release-el7-5.noarch.rpm

# 2. 安装mysql-community-release-el7-5.noarch.rpm包
$ rpm -ivh mysql-community-release-el7-5.noarch.rpm

# 安装这个包后，会获得两个mysql的yum repo源：/etc/yum.repos.d/mysql-community.repo，/etc/yum.repos.d/mysql-community-source.repo。

# 3. 安装mysql
$ yum install mysql-server

# 根据提示安装就可以了,不过安装完成后没有密码,需要重置密码

# 4. 重置mysql密码
$ mysql -u root


# 登录时有可能报这样的错：ERROR 2002 (HY000): Can‘t connect to local MySQL server through socket ‘/var/lib/mysql/mysql.sock‘ (2)，原因是/var/lib/mysql的访问权限问题。下面的命令把/var/lib/mysql的拥有者改为当前用户：

$ sudo chown -R root:root /var/lib/mysql

# 重启mysql服务

$ service mysqld restart

# 接下来登录重置密码：

$ mysql -u root  //直接回车进入mysql控制台
mysql > use mysql;
mysql > update user set password=password('123456') where user='root';
mysql > exit;
```

### 5.1 远程访问

参考文章

- [《阿里云下配置 MySQL 远程连接的步骤详解》](http://www.jb51.net/article/121173.htm)

```bash
# 登录数据库
mysql -u 用户名 -h localhost -p 密码

# 打开mysql
use mysql;

# %表示任何ip都能连接mysql
update user set host = '%' where user = 'root' ;

# 修改完后，刷新权限表，让配置生效
flush privileges ;
```

## 六、mongodb

参考文章

- [《MongoDB Download Center》](https://www.mongodb.com/download-center?jmp=nav#community)
- [《CentOS7.2 中安装 MongoDB》](https://blog.csdn.net/junshangshui/article/details/79371316)

```bash
# 下载安装包
wget https://fastdl.mongodb.org/linux/mongodb-linux-x86_64-3.6.5.tgz

# 解压
tar -xvzf mongodb-linux-x86_64-3.6.5.tgz

mv mongodb-linux-x86_64-3.6.5 /opt/mongodb-v3.6.5

# 把mongodb的bin目录设置到环境变量里面
vim ./bash_profile

PATH=$PATH:/opt/mongodb-v3.6.5/bin

source

# 创建目录存在数据
mkdir /data/db
mkdir /data/log
vim /data/log/mongodb.log

# 添加一个mongodb.conf
port=27017                          #端口
dbpath= /data/db                    #数据库存文件存放目录
logpath= /data/log/mongodb.log      #日志文件存放路径
logappend=true                      #使用追加的方式写日志
fork=true                           #不以守护程序的方式启用，即不在后台运行
maxConns=100                        #最大同时连接数
noauth=true                         #不启用验证
journal=true                        #每次写入会记录一条操作日志（通过journal可以重新构造出写入的数据）。
                                    #即使宕机，启动时wiredtiger会先将数据恢复到最近一次的checkpoint点，然后重放后续的journal日志来恢复。
storageEngine=wiredTiger            #存储引擎有mmapv1、wiretiger、mongorocks
bind_ip = 0.0.0.0                   #这样就可外部访问了，例如从win10中去连虚拟机中的MongoDB

# 后台启动mongodb
mongod --config /usr/mongodb/mongodb.conf


# 连接到数据库
mongo

> show databases;

> use admin;

> show tables;

> db.system.version.find();
```

### 6.1 mongodb 添加 帐号密码

```bash
# 数据库存在则切换过去，否则则新建一个数据库
use blog;

# 创建数据库
db.createUser({user:"test",pwd:"test@test",roles:[{role:"readWrite",db:"blog"}]})

# 重新启动数据库

# 方式一 【不推荐】
service mongod stop

# 方式二 【推荐】
>use admin

switched to db admin

>db.shutdownServer()
server should be down...

# 方式三
mongod --shutdown
```

```bash
> use blog;

> show tables;

2018-05-23T14:59:32.819+0800 E QUERY    [thread1] Error: listCollections failed: {
	"ok" : 0,
	"errmsg" : "not authorized on blog to execute command { listCollections: 1.0, filter: {}, $db: \"blog\" }",
	"code" : 13,
	"codeName" : "Unauthorized"
} :
_getErrorWithCode@src/mongo/shell/utils.js:25:13
DB.prototype._getCollectionInfosCommand@src/mongo/shell/db.js:941:1
DB.prototype.getCollectionInfos@src/mongo/shell/db.js:953:19
DB.prototype.getCollectionNames@src/mongo/shell/db.js:964:16
shellHelper.show@src/mongo/shell/utils.js:842:9
shellHelper@src/mongo/shell/utils.js:739:15
@(shellhelp2):1:1

> db.auth('zhongxia','izhongxia')
1

> show tables;
```
