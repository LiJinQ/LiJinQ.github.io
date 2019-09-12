---
layout:     post
title:      大数据学习笔记（一）
subtitle:   虚拟机搭建hadoop集群
date:       2019-09-12
author:     LJQ
header-img: img/post-bg-bigdata1.jpg
catalog: true
tags:
    - Bigdata
    - Hadoop
    - Linux 
---

2# 虚拟机环境
 
 >操作系统：centos7
 >内存分配：2g
 >硬盘分配：30g
 >预计搭建集群数：3
 
 # 搭建单机环境
 
 ### 关闭防火墙
 
 自己机器为了方便，公司环境为了安全请开放相应端口
 
 ##### 关闭防火墙
 
 ~~~bash
 systemctl stop firewalld.service
 ~~~
 
 ##### 禁用防火墙
 
 ~~~bash
 systemctl disabled firewalld.service
 ~~~
 
 ##### 查看防火墙
 
 ~~~bash
 systemctl status firewalld.service
 ~~~
 
 ### 修改ip
 
 ~~~bash
 vi /etc/sysconfig/network-scripts/ifcfg-ens33
 ~~~
 
 ifcfg-ens33网卡名称不同版本不同
 ![](https://upload-images.jianshu.io/upload_images/15504753-2d81493db8d096b0.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
 
 ~~~bash
 vi /etc/resolv.conf
 ~~~
 
 ![](https://upload-images.jianshu.io/upload_images/15504753-f069083bca632bee.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
 
 ### 重启网卡
 
 ~~~bash
 service network restart
 ~~~
 
 ### 修改主机名并与ip映射
 
 ##### 修改主机名
 
 ~~~bash
 hostnamectl set-hostname 主机名
 ~~~
 
 ##### ip和主机名关系映射
 
 ~~~bash
 vi /etc/hosts
 ~~~
 
 > 192.168.1.121(虚拟机1ip) bigdata1（虚拟机1主机名）
 > 192.168.1.122 bigdata2
 > 192.168.1.123 bigdata3
 
 ##### 在windows的C:\Windows\System32\drivers\etc\hosts添加
 
 > 192.168.1.121 bigdata1
 > 192.168.1.122 bigdata2
 > 192.168.1.123 bigdata3
 
 没有修改权限解决方案：
 1.将hosts文件复制到桌面
 2.记事本打开文件修改
 3.替换etc目录下的hosts
 4.提示点击继续
 
 ### 安装jdk
 
 [centos7踩坑（一）安装JDK8+Maven3](https://www.jianshu.com/p/2e931aa78e9a)
 
 ### 安装hadoop
 
 ##### 下载hadoop压缩包
 
 >[https://archive.apache.org/dist/hadoop/common/hadoop-2.8.4/](https://archive.apache.org/dist/hadoop/common/hadoop-2.8.4/)
 
 ##### 解压压缩包
 
 ~~~bash
 tar -zxvf hadoop-2.8.4.tar.gz -C 你想安装hadoop的目录
 ~~~
 
 ##### 添加环境变量
 
 ~~~bash
 vi /etc/profile
 ~~~
 
 ~~~bash
 #路径为你的安装目录
 export HADOOP_HOME=/opt/module/hadoop-2.8.4
 export PATH=$HADOOP_HOME/bin:$PATH:$HADOOP_HOME/sbin
 ~~~
 
 ##### 验证
 输入命令```hadoop```
 
 ![](https://upload-images.jianshu.io/upload_images/15504753-1cf86494c3c48718.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
 
 # 搭建伪分布式
 
 ### 修改配置文件
 
 ##### core-site.xml
 
 >文件位置：你的hadoop目录/etc/hadoop
 
 ~~~bash
 vi core-site.xml
 ~~~
 
 ~~~xml
 <configuration>
 
 <!-- 指定HDFS中NameNode的地址-->
 <property>
 <name>fs.defaultFS</name>
 <value>hdfs://主机名:9000</value>
 </property>
 <!-- 指定hadoop运行时产生文件的存储目录-->
 <property>
 <name>hadoop.tmp.dir</name>
 <value>hadoop路径/data/tmp</value>
 </property>
 
 </configuration>
 
 ~~~
 
 ##### hdfs-site.xml
 
 >文件位置：你的hadoop目录/etc/hadoop
 
 ~~~bash
 vi hdfs-site.xml
 ~~~
 
 ~~~xml
 <configuration>
 
 <!--数据冗余数-->
 <property>
 <name>dfs.replication</name>
 <value>1</value>
 </property>
 
 </configuration>
 
 ~~~
 
 #####  yarn-site.xml
 
 >文件位置：你的hadoop目录/etc/hadoop
 
 ~~~bash
 vi yarn-site.xml
 ~~~
 
 ~~~xml
 <configuration>
 
 <!-- Site specific YARN configuration properties -->
 
 <!-- reducer获取数据的方式 -->
 <property>
 <name>yarn.nodemanager.aux-services</name>
 <value>mapreduce_shuffle</value>
 </property>
 
 <!-- 指定YARN的ResourceManager的地址-->
 <property>
 <name>yarn.resourcemanager.hostname</name>
 <value>主机名1</value>
 </property>
 
 <!-- 日志聚集功能使能 -->
 <property>
 <name>yarn.log-aggregation-enable</name>
 <value>true</value>
 </property>
 
 <!-- 日志保留时间设置7天(秒) -->
 <property>
 <name>yarn.log-aggregation.retain-seconds</name>
 <value>604800</value>
 </property>
 
 </configuration>
 
 ~~~
 
 #####  mapred-site.xml
 
 >文件位置：你的hadoop目录/etc/hadoop
 >源文件没有这个文件，只有mapred-site.xml.template，需要先改名
 
 ~~~bash
 mv mapred-site.xml.template  mapred-site.xml
 vi mapred-site.xml
 ~~~
 
 ~~~xml
 <configuration>
 
 <!-- 指定mr运行在yarn上-->
 <property>
 <name>mapreduce.framework.name</name>
 <value>yarn</value>
 </property>
 
 <!--历史服务器的地址-->
 <property>
 <name>mapreduce.jobhistory.address</name>
 <value>主机名1:10020</value>
 </property>
 
 <!--历史服务器页面的地址-->
 <property>
 <name>mapreduce.jobhistory.webapp.address</name>
 <value>主机名1:19888</value>
 </property>
 
 </configuration>
 
 ~~~
 
 ##### hadoop-env.sh
 
 >文件位置：你的hadoop目录/etc/hadoop
 
 ~~~bash
 vi  hadoop-env.sh
 ~~~
 
 ~~~bash
 #将java环境变量改为绝对路径
 export JAVA_HOME=/opt/module/jdk1.8.0_221/
 ~~~
 
 ### 格式化NameNode
 
 ##### 命令
 ~~~bash
 hdfs namenode -format
 ~~~
 
 ##### 格式化原因
 >NameNode主要被用来管理整个分布式文件系统的命名空间(实际上就是目录和文件)的元数据信息，同时为了保证数据的可靠性，还加入了操作日志，所以，NameNode会持久化这些数据(保存到本地的文件系统中)。对于第一次使用HDFS，在启动NameNode时，需要先执行-format命令，然后才能正常启动NameNode节点的服务。
 
 ##### 格式化做的事情
 >在NameNode节点上，有两个最重要的路径，分别被用来存储元数据信息和操作日志，而 这 两 个 路 径来 自 于 配置 文 件 ， 它们 对 应 的属 性 分别 是dfs.name.dir和dfs.name.edits.dir，同时，它们默认的路径均是/tmp/hadoop/dfs/name。格式化时，NameNode会清空两个目录下的所有文件，之后，会在目录dfs.name.dir下创建文件hadoop.tmp.dir这个配置，会让dfs.name.dir和dfs.name.edits.dir会让两个目录的文件生成在一个目录里。
 
 ##### 提示
 
 **只有第一次搭建时才能使用该命令！！**
 
 ### 启动集群检验
 
 ##### 命令
 
 ~~~bash
 start-all.sh
 ~~~
 
 ##### 查看运行进程
 
 ~~~bash
 jps
 ~~~
 
 ![](https://upload-images.jianshu.io/upload_images/15504753-dde7879c5b8704ed.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
 
 ##### 查看web页面
 
 > 浏览器输入：ip:50070
 
 ![](https://upload-images.jianshu.io/upload_images/15504753-c27e685c11d603ca.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
 
 ##### 查看Yarn页面
 
 > 浏览器输入：ip:8088
 
 ![](https://upload-images.jianshu.io/upload_images/15504753-73d82e6df0e6cbeb.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
 
 
 # 完全分布式集群
 
 ### 克隆两台虚拟机
 
 ![](https://upload-images.jianshu.io/upload_images/15504753-2b7204fe950c2d52.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
 
 ### 关闭防火墙
 
 ~~~bash
 systemctl stop firewalld.service
 ~~~
 
 ### 启动虚拟机修改ip
 
 ~~~bash
 vi /etc/sysconfig/network-scripts/ifcfg-ens33
 service network restart
 ~~~
 
 ![](https://upload-images.jianshu.io/upload_images/15504753-5e0bb72756b3158d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
 
 ### 修改hostname文件
 
 ~~~bash
 vi /etc/hostname
 ~~~
 
 重复一次获得bigdata2和bigdata3
 
 ### 删除伪分布式的data
 
 >文件位置，格式化后的hadoop文件
 
 ~~~bash
 rm -fr data/
 ~~~
 
 ### 发送hosts文件
 在bigdata1中使用scp命令
 
 ~~~bash
 #scp 本机文件位子 用户名@主机名:目标主机文件位置
 scp /etc/hosts root@bigdata2:/etc/
 ~~~
 
 ### 配置ssh
 
 > 每个节点都要操作以下命令
 
 ##### 生成密钥
 
 ~~~bash
 ssh-keygen
 ~~~
 
 ##### 将节点密钥放入目标主机密钥库
 
 ~~~bash
 ssh-copy-id 节点1主机名
 ssh-copy-id 节点2主机名
 ssh-copy-id 节点3主机名
 ~~~
 
 ### 修改hadoop配置文件
 
 >以下操作三台虚拟机同步
 
 ##### hdfs-site.xml
 
 ~~~xml
 <configuration>
 
 <!--数据冗余数-->
 <property>
 <name>dfs.replication</name>
 <value>3</value>
 </property>
 
 <!--secondary的地址-->
 <property>
 <name>dfs.namenode.secondary.http-address</name>
 <value>主机名1:50090</value>
 
 </property><!--关闭权限-->
 <property>
 <name>dfs.permissions</name>
 <value>false</value>
 </property>
 
 </configuration>
 
 ~~~
 
 ##### yarn-site.xml
 
 将yarn部署到节点2
 ~~~xml
 <configuration>
 
 <!-- Site specific YARN configuration properties -->
 
 <!-- reducer获取数据的方式 -->
 <property>
 <name>yarn.nodemanager.aux-services</name>
 <value>mapreduce_shuffle</value>
 </property>
 
 <!-- 指定YARN的ResourceManager的地址-->
 <property>
 <name>yarn.resourcemanager.hostname</name>
 <value>节点2主机名</value>
 </property>
 
 <!-- 日志聚集功能使能 -->
 <property>
 <name>yarn.log-aggregation-enable</name>
 <value>true</value>
 </property>
 
 <!-- 日志保留时间设置7天(秒) -->
 <property>
 <name>yarn.log-aggregation.retain-seconds</name>
 <value>604800</value>
 </property>
 
 
 </configuration>
 
 ~~~
 
 ##### mapred-site.xml
 
 将历史服务器yarn节点改为节点2
 
 ~~~xml
 <configuration>
 
 <!-- 指定mr运行在yarn上-->
 <property>
 <name>mapreduce.framework.name</name>
 <value>yarn</value>
 </property>
 
 <!--历史服务器的地址-->
 <property>
 <name>mapreduce.jobhistory.address</name>
 <value>节点2主机名:10020</value>
 </property>
 
 <!--历史服务器页面的地址-->
 <property>
 <name>mapreduce.jobhistory.webapp.address</name>
 <value>节点2主机名:19888</value>
 </property>
 
 </configuration>
 
 ~~~
 
 ##### 配置集群主机名
 添加datanode节点
 
 >文件位置
 >hadoop目录/etc/hadoop
 
 ~~~bash
 vi slaves
 ~~~
 
 ~~~txt
 节点主机名1
 节点主机名2
 节点主机名3
 ~~~
 
 ### 启动集群
 
 ##### 节点1
 
 重新格式化namenode
 
 ~~~bash
 hadoop namenode -format
 ~~~
 
 启动hdfs
 
 ~~~bash
 start-dfs.sh
 ~~~
 
 ##### 节点2
 
 启动Yarn
 
 ~~~bash
 start-yarn.sh
 ~~~
 
 ### 检测
 
 >浏览器输入：节点1ip:50070
 >点击Datanodes标签
 
 ![](https://upload-images.jianshu.io/upload_images/15504753-424321758d233618.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
