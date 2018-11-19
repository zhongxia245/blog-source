---
title: 快速了解docker
tags: docker
descriptioin: 介绍 docker 的一些基础命令，以及几个简单的小例子，帮助快速入门。后面就介绍一下， 如何使用 docker 来进行部署微服务。
abbrlink: 48296
date: 2017-11-21 17:24:10
---

介绍 docker 的一些基础命令，以及几个简单的小例子，帮助快速入门。后面就介绍一下， 如何使用 docker 来进行部署微服务。

## docker 架构图

![](https://ws4.sinaimg.cn/large/006tKfTcgy1flpums8f2nj30ed0awmyj.jpg)

## 零、学习文章

1.  [《Docker 教程 | 菜鸟教程》](http://www.runoob.com/docker/docker-tutorial.html)
2.  [《一小时 Docker 教程》](https://blog.csphere.cn/archives/22)

> 下面这些内容，来源于 [《一小时 Docker 教程》](https://blog.csphere.cn/archives/22)。

## 一、Docker 基础

这篇基础文章是方便用户在使用 cSphere 平台之前,了解 docker 基础知识。

针对已经有一定的 Linux 基础知识的用户。

### 1.1 Docker 是什么

Docker 是一个改进的容器技术。具体的“改进”体现在，Docker 为容器引入了镜像，使得容器可以从预先定义好的模版（images）创建出来，并且这个模版还是分层的。

Docker 经常被提起的特点：

- 轻量，体现在内存占用小，高密度
- 快速，毫秒启动
- 隔离，沙盒技术更像虚拟机

### 1.2 Docker 技术的基础：

- namespace，容器隔离的基础，保证 A 容器看不到 B 容器. 6 个名空间：User,Mnt,Network,UTS,IPC,Pid
- cgroups，容器资源统计和隔离。主要用到的 cgroups 子系统：cpu,blkio,device,freezer,memory
- unionfs，典型：aufs/overlayfs，分层镜像实现的基础

### 1.3 Docker 组件：

- docker Client 客户端————>向 docker 服务器进程发起请求，如:创建、停止、销毁容器等操作
- docker Server 服务器进程—–>处理所有 docker 的请求，管理所有容器
- docker Registry 镜像仓库——>镜像存放的中央仓库，可看作是存放二进制的 scm

### 1.4 Docker 安装

- Docker 的安装非常简单，支持目前所有主流操作系统，从 Mac 到 Windows 到各种 Linux 发行版具体参考： [docker 安装](https://docs.docker.com/installation/)

## 二、Docker 常见命令

### 2.1 容器相关操作

```
docker create # 创建一个容器但是不启动它
docker run # 创建并启动一个容器
docker stop # 停止容器运行，发送信号SIGTERM
docker start # 启动一个停止状态的容器
docker restart # 重启一个容器
docker rm # 删除一个容器
docker kill # 发送信号给容器，默认SIGKILL
docker attach # 连接(进入)到一个正在运行的容器
docker wait # 阻塞到一个容器，直到容器停止运行
```

### 2.2 获取容器相关信息

```
docker ps # 显示状态为运行（Up）的容器
docker ps -a # 显示所有容器,包括运行中（Up）的和退出的(Exited)
docker inspect # 深入容器内部获取容器所有信息
docker logs # 查看容器的日志(stdout/stderr)
docker events # 得到docker服务器的实时的事件
docker port # 显示容器的端口映射
docker top # 显示容器的进程信息
docker diff # 显示容器文件系统的前后变化
```

### 2.3 导出容器

```
docker cp # 从容器里向外拷贝文件或目录
docker export # 将容器整个文件系统导出为一个tar包，不带layers、tag等信息
```

### 2.4 执行

```
docker exec # 在容器里执行一个命令，可以执行bash进入交互式
```

### 2.5 镜像操作

```
docker images # 显示本地所有的镜像列表
docker import # 从一个tar包创建一个镜像，往往和export结合使用
docker build # 使用Dockerfile创建镜像（推荐）
docker commit # 从容器创建镜像
docker rmi # 删除一个镜像
docker load # 从一个tar包创建一个镜像，和save配合使用
docker save # 将一个镜像保存为一个tar包，带layers和tag信息
docker history # 显示生成一个镜像的历史命令
docker tag # 为镜像起一个别名
```

### 2.6 镜像仓库(registry)操作

```
docker login # 登录到一个registry
docker search # 从registry仓库搜索镜像
docker pull # 从仓库下载镜像到本地
docker push # 将一个镜像push到registry仓库中
```

## 三、常用命令

1.  获取 Container IP 地址（Container 状态必须是 Up）

```
docker inspect id | grep IPAddress | cut -d '"' -f 4
```

2.  获取端口映射

```
docker inspect -f '{{range $p, $conf := .NetworkSettings.Ports}} {{$p}} -> {{(index $conf 0).HostPort}} {{end}}' id
```

3.  获取环境变量

```
docker exec container_id env
```

4.  杀掉所有正在运行的容器

```
docker kill $(docker ps -q)
```

5.  删除老的(一周前创建)容器

```
docker ps -a | grep 'weeks ago' | awk '{print $1}' | xargs docker rm
```

6.  删除已经停止的容器

```
docker rm `docker ps -a -q`
```

7.  删除所有镜像，小心

```
docker rmi $(docker images -q)
Dockerfile
Dockerfile是docker构建镜像的基础，也是docker区别于其他容器的重要特征，正是有了Dockerfile，docker的自动化和可移植性才成为可能。
```

## 四、Dockerfile

不论是开发还是运维，学会编写 Dockerfile 几乎是必备的，这有助于你理解整个容器的运行。

```
FROM , 从一个基础镜像构建新的镜像
FROM ubuntu
MAINTAINER , 维护者信息
MAINTAINER William <wlj@nicescale.com>
ENV , 设置环境变量
ENV TEST 1
RUN , 非交互式运行shell命令
RUN apt-get -y update
RUN apt-get -y install nginx
ADD , 将外部文件拷贝到镜像里,src可以为url
ADD http://nicescale.com/  /data/nicescale.tgz
WORKDIR /path/to/workdir, 设置工作目录
WORKDIR /var/www
USER , 设置用户ID
USER nginx
VULUME <#dir>, 设置volume
VOLUME [‘/data’]
EXPOSE , 暴露哪些端口
EXPOSE 80 443
ENTRYPOINT [‘executable’, ‘param1’,’param2’]执行命令
ENTRYPOINT ["/usr/sbin/nginx"]
CMD [“param1”,”param2”]
CMD ["start"]
```

docker 创建、启动 container 时执行的命令，如果设置了 ENTRYPOINT，则 CMD 将作为参数

## 五、Dockerfile 最佳实践

- 尽量将一些常用不变的指令放到前面
- CMD 和 ENTRYPOINT 尽量使用 json 数组方式
- 通过 Dockerfile 构建 image
  docker build csphere/nginx:1.7 .

- 镜像仓库 Registry
  镜像从 Dockerfile build 生成后，需要将镜像推送(push)到镜像仓库。企业内部都需要构建一个私有 docker registry，这个 registry 可以看作二进制的 scm，CI/CD 也需要围绕 registry 进行。

- 部署 registry

```
mkdir /registry
docker run  -p 80:5000  -e STORAGE_PATH=/registry  -v /registry:/registry  registry:2.0
```

- 推送镜像保存到仓库

```
假设192.168.1.2是registry仓库的地址：

docker tag  csphere/nginx:1.7 192.168.1.2/csphere/nginx:1.7
docker push 192.168.1.2/csphere/nginx:1.7
```

## 六、几个简单小例子

### 6.1 创建并拉取 busybox

```
# docker run -it --name con01 busybox:latest
/ # ip addr    #容器里执行
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default
link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
inet 127.0.0.1/8 scope host lo
   valid_lft forever preferred_lft forever
Segmentation fault (core dumped)
/ # ping www.csphere.cn
PING www.csphere.cn (117.121.26.243): 56 data bytes
64 bytes from 117.121.26.243: seq=0 ttl=48 time=3.139 ms
64 bytes from 117.121.26.243: seq=1 ttl=48 time=3.027 ms
^C
--- www.csphere.cn ping statistics ---
2 packets transmitted, 2 packets received, 0% packet loss
round-trip min/avg/max = 3.027/3.083/3.139 ms
exit    #退出容器
```

### 6.2 创建测试容器

```
docker run -d --name con03 csphere/test:0.1
efc9bda4a2ff2f479b18e0fc4698e42c47c9583a24c93f5ce6b28a828a172709
```

### 6.3 登陆到 con03 中

```
# docker exec -it con03 /bin/bash
[root@efc9bda4a2ff /]# exit
```

### 6.4 停止 con03

```
# docker stop con03
con03
```

### 6.5 开启 con03

```
# docker start con03
con03
```

### 6.6 删除 con03

```
# docker ps -a
CONTAINER ID        IMAGE                    COMMAND                CREATED             STATUS                      PORTS                                             NAMES
efc9bda4a2ff        csphere/test:0.1         "/usr/local/bin/run    4 minutes ago       Up 17 seconds                                                                 con03
99aa6ee25adc        busybox:latest           "/bin/sh"              14 minutes ago      Exited (0) 12 minutes ago                                                     con02
831c93de9b9f        busybox:latest           "/bin/sh"              2 hours ago         Up 27 minutes                                                                 con01
# docker rm con02     #容器停止的状态
# docker rm -f con03  #容器开启的状态
```

## 七、镜像操作

### 7.1 从 docker hub 官方镜像仓库拉取镜像

```
# docker pull busybox:latest
atest: Pulling from busybox
cf2616975b4a: Pull complete
6ce2e90b0bc7: Pull complete
8c2e06607696: Already exists
busybox:latest: The image you are pulling has been verified. Important: image verification is a tech preview feature and should not be relied on to provide security.
Digest: sha256:38a203e1986cf79639cfb9b2e1d6e773de84002feea2d4eb006b52004ee8502d
Status: Downloaded newer image for busybox:latest
```

### 7.2 从本地上传镜像到镜像仓库

```
docker push 192.168.1.2/csphere/nginx:1.7
```

### 7.3 查找镜像仓库的某个镜像

```
# docker search centos/nginx
NAME                                     DESCRIPTION                                     STARS     OFFICIAL   AUTOMATED
johnnyzheng/centos-nginx-php-wordpress                                                   1                    [OK]
sergeyzh/centos6-nginx                                                                   1                    [OK]
hzhang/centos-nginx                                                                      1                    [OK]
```

### 7.4 查看本地镜像列表

```
# docker images
TAG                 IMAGE ID            CREATED             VIRTUAL SIZE
docker.io/csphere/csphere   0.10.3              604c03bf0c9e        3 days ago          62.72 MB
docker.io/csphere/csphere   latest              604c03bf0c9e        3 days ago          62.72 MB
csphere/csphere             0.10.3              604c03bf0c9e        3 days ago          62.72 MB
registry                    2.0                 2971b6ce766c        7 days ago          548.1 MB
busybox                     latest              8c2e06607696        3 weeks ago         2.43 MB
```

### 7.5 删除镜像

```
docker rmi busybox:latest        #没有容器使用此镜像创建，如果有容器在使用此镜像会报错：Error response from daemon: Conflict, cannot delete 8c2e06607696 because the running container 831c93de9b9f is using it, stop it and use -f to force
FATA[0000] Error: failed to remove one or more images
docker rmi -f busybox:latest     #容器使用此镜像创建，此容器状态为Exited
```

### 7.6 查看构建镜像所用过的命令

```
# docker history busybox:latest
IMAGE               CREATED             CREATED BY                                      SIZE
8c2e06607696        3 weeks ago         /bin/sh -c #(nop) CMD ["/bin/sh"]               0 B
6ce2e90b0bc7        3 weeks ago         /bin/sh -c #(nop) ADD file:8cf517d90fe79547c4   2.43 MB
cf2616975b4a        3 weeks ago         /bin/sh -c #(nop) MAINTAINER Jérôme Petazzo     0 B
```
