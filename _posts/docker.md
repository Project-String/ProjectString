---
title: Docker的安装与使用
date: 2020-11-09 14:00
description: 商汤-工程院-容器平台实习随笔
categories:
 - 云计算
---

### docker安装
Docker在Mac上可以直接使用Homebrew进行安装。
```
brew cask install docker
```
安装完成后，查看版本号。
```
$ docker --version
Docker version 19.03.12, build 48a66213fe
```
另一种查询版本号的方式是`docker version`（没有 --），该查询方式可以分别列出client和server的版本信息。
### docker基本命令
下面来看下docker的一些针对镜像和容器的基本操作。
![](https://img2020.cnblogs.com/blog/2126877/202008/2126877-20200821092428279-1522112888.png)
图片转载至[https://www.cnblogs.com/duanxz/p/7905233.html](https://www.cnblogs.com/duanxz/p/7905233.html)

#### docker run
docker run是将镜像跑起来，变成容器。即在镜像的最外层加上一层*容器层*，该层可以支持写操作。

下面是官方文档给的例子：
```
docker run -i -t ubuntu:latest /bin/bash 
```
该命令的执行过程如下
1. 如果本地没有ubuntu镜像，使用docker pull从registry中拉取镜像
2. docker创建一个容器，使用docker container create
3. 加上一层读写层
4. 创建网络接口
5. 运行容器，执行/bin/bash命令，由于-i，-t标签，容器与宿主机终端可以交互
6. 当输入exit，容器停止运行，但是没有被remove，需要执行docker rm操作去移除容器

##### 几个标签
* 如果想要container后台挂起，使用 -d（--detach） 标签
* -it 两个标签一般一起使用，交互模式
* -p 映射端口号-p <host_port:contain_port>



#### docker image
docker image 是docker镜像相关的操作。
```
docker image ls # 列出所有的本地镜像，该操作与docker images相同
```

#### docker容器
```
docker ps # 列出所有正在运行的容器
CONTAINER ID      IMAGE      COMMAND      CREATED      STATUS      PORTS      NAMES
```
包含了容器的ID，所运行的镜像，所运行的指令，创建时间，状态，端口和名称（随机生成，如果不使用--name指定）

* 如果想要查看包括非运行状态的所有容器，使用 -a 标签




其他常用容器相关操作
```
docker logs <containerId|containerName> # 查询容器的日志
docker rm -f <containerId|containerName> # 删除容器，当容器正在运行时需要 -f 标签强制删除
docker top <containerId|containerName> # 查看容器中正在运行的进程
docker stats <containerId|containerName> # 实时监控容器的资源使用情况
docker attach # 
docker inspect # 
```
注意：使用docker attach 到该容器当中后，不能使用ctrl c！！！这样子，这个容器就消失了，使用ctrl q 退出到宿主机，保持容器继续运行。


#### docker registry操作
```
docker pull <image><:tag> # 从registry中拉取镜像
docker push <image><:tag> # 将本地镜像上传到registry
```




