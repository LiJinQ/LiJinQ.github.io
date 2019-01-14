---
layout:     post
title:      自动化部署（一）
subtitle:   jenkins+github+maven+docker
date:       2019-01-08
author:     LJQ
header-img: img/post-bg-map.jpg
catalog: true
tags:
    - Docker 
    - 运维
---

>本文适用于自动化部署Springboot项目，原理是将jenkins和我们的github关联，当git上的代码更新时，会通知到jenkins服务器，由jenkins服务器通过本地更新仓库自动生成jar包并通过Dockerfile脚本部署项目。

# 准备环境
### java+maven
需要JDK8和Maven3环境，详细配置见[安装JDK8+Maven3](http://lijinquan.top/2018/12/04/Linux-note1/)
### git+jenkins
服务器需要git环境和Jenkins服务，详细配置见[Git+Jenkins](http://lijinquan.top/2018/12/07/Linux-note2/)
# Jenkins插件安装
系统管理->插件管理->可选插件
需要安装以下插件
##### GitHub plugin
![](https://upload-images.jianshu.io/upload_images/15504753-b7953ff9951ec2e4.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


##### Publish Over SSH
![](https://upload-images.jianshu.io/upload_images/15504753-594a70ef9473a849.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


##### Maven相关
![](https://upload-images.jianshu.io/upload_images/15504753-a20555b74c681d1b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

# 配置SSH
参照[https://www.cnblogs.com/YatHo/p/6739814.html](https://www.cnblogs.com/YatHo/p/6739814.html)
# 新建任务
在Jenkins页面新建一个自由风格（freestyle）的任务
ps:本篇的项目用例使用SpringCloud的eureka注册中心，项目源码见[git](https://github.com/LiJinQ/demo_eureka)
### 创建任务
![](https://upload-images.jianshu.io/upload_images/15504753-f3b5fa6edccd373a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
### General
![](https://upload-images.jianshu.io/upload_images/15504753-cb6cb454b36ec713.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
### 源码管理
![](https://upload-images.jianshu.io/upload_images/15504753-fe5875687a5e60b8.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![](https://upload-images.jianshu.io/upload_images/15504753-92bb622f731c85ba.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 构建触发器
![](https://upload-images.jianshu.io/upload_images/15504753-1dc203ce1023eacb.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 构建
![](https://upload-images.jianshu.io/upload_images/15504753-2fbd92c83617637f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 构建后操作
![](https://upload-images.jianshu.io/upload_images/15504753-cb617eb86bfb0427.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
##### Exec command
```
#将jar包拷贝到自定义路径下，ps：buildimage.sh，run.sh，用到的Dockerfile都在自定义路径下，该文件需要自行创建
cp /var/lib/jenkins/workspace/demo_eureka/target/demo_eureka-0.0.1-SNAPSHOT.jar /usr/local/project/eureka    
#进入到该路径
cd /usr/local/project/eureka
#添加执行权限并执行脚本
chmod +x buildimage.sh
./buildimage.sh
 chmod +x run.sh
./run.sh
```
##### buildimage.sh
```
#使用Dockerfile创建Docker容器
docker build -t demo_eureka:1.0 .
```
##### run.sh
```
#执行该容器并映射端口8761
docker run --name="demo_eureka" -p 8761:8761 -d demo_eureka:1.0
```
##### Dockerfile
```
# 版本信息
#java：latest 为centos官方java运行环境镜像，600多M ,可以提前pull到主机本地
FROM java:latest
MAINTAINER cds "ljq"

#复制文件到容器中的/usr/local/jar/
ADD  demo_eureka-0.0.1-SNAPSHOT.jar /usr/local/jar/

#重命名jar名
RUN mv /usr/local/jar/demo_eureka-0.0.1-SNAPSHOT.jar  /usr/local/jar/app.jar

#开启内部服务端口项目端口
EXPOSE 8761
#默认的容器启动执行命令
CMD ["java","-jar","/usr/local/jar/app.jar"]
```
# 上传项目到GitHub
![](https://upload-images.jianshu.io/upload_images/15504753-5f3708bb90774e42.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
浏览器输入：http://ip:8761
![](https://upload-images.jianshu.io/upload_images/15504753-d39535f8b4edd9d7.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
部署成功！

# End
参考博客[jenkins+maven+docker+github全自动化部署SpringBoot实例](https://segmentfault.com/a/1190000014325300)