---
layout:     post
title:      Docker学习笔记（二）创建持久化MySql容器
subtitle:   Docker学习笔记
date:       2019-01-04
author:     LJQ
header-img: img/post-bg-unix-linux.jpg
catalog: true
tags:
    - Docker
    - 运维
---
## 查找hub中的mysql镜像
```docker search mysql```
![](https://upload-images.jianshu.io/upload_images/15504753-46f088bba82c7bff.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 拉取mysql5.6的镜像
```docker pull mysql:5.6```
![](https://upload-images.jianshu.io/upload_images/15504753-3d3c16d03d7b9b08.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

##### 查看已下载镜像
```docker images```
![](https://upload-images.jianshu.io/upload_images/15504753-acb22ca4e3a4f67b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 运行容器
```docker run -p 3306:3306 --name mymysql -v $PWD/conf:/etc/mysql/conf.d -v $PWD/logs:/logs -v $PWD/data:/var/lib/mysql -e MYSQL_ROOT_PASSWORD=123456 -d mysql:5.6```
![](https://upload-images.jianshu.io/upload_images/15504753-98f775aaa7d0795e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
##### 参数解释
- ```-p 3306:3306``` ：将容器的 3306 端口映射到主机的 3306 端口。
- ```-v -v $PWD/conf:/etc/mysql/conf.d```：将主机当前目录下的 conf/my.cnf 挂载到容器的 /etc/mysql/my.cnf
- ```-v $PWD/logs:/logs```：将主机当前目录下的 logs 目录挂载到容器的 /logs
- ```-v $PWD/data:/var/lib/mysql```：将主机当前目录下的data目录挂载到容器的 /var/lib/mysql 
- ```-e MYSQL_ROOT_PASSWORD=123456```：初始化 root 用户的密码
##### 查看容器运行情况
```docker ps```

![](https://upload-images.jianshu.io/upload_images/15504753-f5470524f41f4446.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 操作mysql容器
##### 进入mysql容器
```docker exec -it mymysql bash```
##### 进入容器内mysql
```mysql -uroot -p123456```
##### 创建test数据库
```create database test;```
![](https://upload-images.jianshu.io/upload_images/15504753-e1408f46978cf44a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 停止容器验证
##### 停止容器
``` docker stop 7ad71afe1ea6```
##### 启动容器验证
```docker start mymysql```

![](https://upload-images.jianshu.io/upload_images/15504753-c0bd0b9d2e8d969d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 持久化构建成功
## End
学习内容来源[菜鸟教程|docker教程](http://www.runoob.com/docker/docker-tutorial.html)



