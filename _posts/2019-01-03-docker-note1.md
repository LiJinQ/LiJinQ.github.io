---
layout:     post
title:      Docker学习笔记（一）Docker的安装与基本命令
subtitle:   Docker学习笔记
date:       2019-01-03
author:     LJQ
header-img: img/post-bg-docker-note1.jpg
catalog: true
tags:
    - Docker
    - 运维
---

# Linux下安装docker
#### 下载docker
```sudo wget -qO- https://get.docker.com | sh```

- sudo：普通用户，希望以root的身份执行命令
- wget：命令行的下载工具
- https://get.docker.com：docker的下载地址
- -q：希望减少wget的输出

#### 将指定用户添加到docker组里
>sudo usermod -aG docker 你指定的用户

使用命令```docker info```可以查看docker信息

# docker架构
![](https://upload-images.jianshu.io/upload_images/15504753-09382a5d88816a69.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

>中间是**DOCKER_Host**，是进行docker操作的虚拟机，虚拟机上运行了一个核心程序：**docker deamon**，docker的各种操作都是通过这个程序进行的，比如下载docker的镜像、运行一个docker容器，我们通过在左边的**Client**输入docker命令到*docker deamon*程序，然后由*docker deamon*程序进行实际的操作，*docker deamon*可以根据```push```等命令从右边的**Register**拖取需要的镜像到Host中。

# 基本docker命令
#### 从远程拉取镜像命令
>docker pull 镜像名称

当我们需要镜像时，可以使用```docker pull```命令。
如，当我们需要使用ubuntu镜像时，可以使用命令```docker pull ubuntu```
![](https://upload-images.jianshu.io/upload_images/15504753-310d012b1677d3ab.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

#### 从主机向镜像拷贝命令
>docker cp 主机文件路径 *容器的CONTAINER ID*:容器路径

如我想将我的主机上的fun.html这个文件拷贝到nginx镜像下的usr文件下，可以使用命令```docker cp fun.html 141a71acb5d8://usr```
![](https://upload-images.jianshu.io/upload_images/15504753-4cc91e518b1ca97f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


#### 运行docker命令
>docker run 相应的镜像名 在该镜像上进行的操作 

如  ```docker run ubuntu echo hello docker```
这行命令的意思是在一个ubuntu镜像中运行一个*echo hello docker*命令。
嗯，第一个命令总是hello 什么，手动滑稽。
![](https://upload-images.jianshu.io/upload_images/15504753-3f3562d01b12a4bd.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

可以看到，如果是第一次使用该镜像，docker会自动为你下载该镜像，然后再执行相应操作。
#### 查看本地所有镜像
>docker images

我们可以通过运行```docker images```来查看docker在本地的所有镜像。

![](https://upload-images.jianshu.io/upload_images/15504753-45524ed3e31de425.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

如，当我们需要运行docker.io/nginx这个镜像时，可以直接使用```docker run docker.io/nginx```命令

#### 查看正在运行的docker进程
>docker ps

![](https://upload-images.jianshu.io/upload_images/15504753-cef185c8af15bf60.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

#### 停止docker进程
>docker stop docker的CONTAINER ID

我们可以根据```docker ps```命令查到正在运行的所有容器，*docker ps*会为我们提供容器的**CONTAINER ID**，我们可以根据这个id停止一个容器。
如，输入命令```docker stop 1636c21c99c3```
![](https://upload-images.jianshu.io/upload_images/15504753-411ccff98e1bffed.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

docker会在停止容器的同时将该容器的CONTAINER ID返回。

#### 保存docker改动
>docker commit -m 改动日志 *容器的CONTAINER ID* 镜像名称

docker在容器内做的所有改动都是暂时的，因此，当我们在stop docker前需要将之前的命令保存。
如，输入命令```docker commit -m 'docker保存' 141a71acb5d8 fun ``` 

![](https://upload-images.jianshu.io/upload_images/15504753-b244f2e771aeb681.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

我们可以发现在提交保存时，会返回一个新的**IMAGE ID**，因为docker的保存实际上是在本地生成一个新的镜像，当我们输入命令```docker images```时，可以看到我们的本地镜像多出了一个名称为**fun**的镜像。

#### 删除docker镜像
>docker rmi 镜像的IMAGE ID || 镜像名

删除docker镜像需要的属性是**IMAGE ID**或**REPOSITORY**。
如，输入命令```docker rmi 3c7f9c37880e```和```docker rmi fun1```

![](https://upload-images.jianshu.io/upload_images/15504753-77328952da624c71.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

#### 清除容器
> docker ps -a
docker rm 容器的CONTINER ID

我们知道```docker ps```命令只会显示正在运行的容器，不会显示停止的容器，当我们需要查看所有容器时，可以使用命令```docker ps -a```。

![](https://upload-images.jianshu.io/upload_images/15504753-ec139a047efa52ce.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

当我们需要清除不需要的容器时，可以使用命令```docker rm CONTAINER ID```
如，我们需要清除上图的ubuntu容器，可以输入命令```docker rm 6a60a0864210```

![](https://upload-images.jianshu.io/upload_images/15504753-4cba28d1e0760f0f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

可以看到该容器已经删除

#### End
本次学习资源来自慕课网的**西北峰**老师
[附上课程地址](https://www.imooc.com/learn/867)
