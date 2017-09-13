---
title: 修改Centos的SSH服务默认端口
date: 2017-09-13 11:07:42
tags: linux
---
## 一、修改默认SSH端口

```
### 1.  修改 ssh配置
sudo vi /etc/ssh/sshd_config
```
修改默认端口
![](https://ws3.sinaimg.cn/large/006tKfTcly1fjhr8p6ywsj30sa0i0tco.jpg)

禁用Root帐号登录，设置允许登录的帐号 ，禁止使用密码登录
![](https://ws3.sinaimg.cn/large/006tKfTcly1fjhr997ukjj30jq0gujtq.jpg)

### 2. 修改完成重启 sshd服务

```
sudo service sshd restart
```

### 3. 尝试登录
>如果不加 端口号，则登录不了
```
ssh -p 39999 zhongxia@www.izhongxia.com
```