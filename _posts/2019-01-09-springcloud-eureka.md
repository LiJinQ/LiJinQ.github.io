---
layout:     post
title:      Spring Cloud（一）服务注册与发现
subtitle:   Eureka-Server与Eureka-Client
date:       2019-01-09
author:     LJQ
header-img: img/post-bg-rwd.jpg
catalog: true
tags:
    - Spring Cloud
---

# Spring Cloud简介

>Spring Cloud是一系列框架的有序集合。它利用Spring Boot的开发便利性巧妙地简化了分布式系统基础设施的开发，如服务发现注册、配置中心、消息总线、负载均衡、断路器、数据监控等，都可以用Spring Boot的开发风格做到一键启动和部署。Spring Cloud并没有重复制造轮子，它只是将目前各家公司开发的比较成熟、经得起实际考验的服务框架组合起来，通过Spring Boot风格进行再封装屏蔽掉了复杂的配置和实现原理，最终给开发者留出了一套简单易懂、易部署和易维护的分布式系统开发工具包。

# 服务注册中心-Eureka Server

Eureka Service为框架内的服务提供注册功能。微服务在启动后，可以自动注册到Eureka Service提供的服务注册中心里，用户可以通过注册中心检测微服务的健康情况。

### idea创建

**File->new->project->Spring Initializr**

![](https://upload-images.jianshu.io/upload_images/15504753-cd1961c20141daac.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

**next**

![](https://upload-images.jianshu.io/upload_images/15504753-e6500d4773db8fc1.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

**next->Cloud Discory->Eureka Server**

![](https://upload-images.jianshu.io/upload_images/15504753-602b82a430c36ff5.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

**next->finish**

### pom

创建后的pom如下

```
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.1.2.RELEASE</version>
        <relativePath/> <!-- lookup parent from repository -->
    </parent>
    <groupId>com.example</groupId>
    <artifactId>demo</artifactId>
    <version>0.0.1-SNAPSHOT</version>
    <name>demo</name>
    <description>Demo project for Spring Boot</description>

    <properties>
        <java.version>1.8</java.version>
        <spring-cloud.version>Greenwich.RC2</spring-cloud.version>
    </properties>

    <dependencies>
        <!--eureka server  Spring Cloud 2.0.0以上引入 -->
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-netflix-eureka-server</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
    </dependencies>

    <dependencyManagement>
        <dependencies>
            <dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-dependencies</artifactId>
                <version>${spring-cloud.version}</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
        </dependencies>
    </dependencyManagement>

    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
            </plugin>
        </plugins>
    </build>

    <repositories>
        <repository>
            <id>spring-milestones</id>
            <name>Spring Milestones</name>
            <url>https://repo.spring.io/milestone</url>
        </repository>
    </repositories>

</project>

```

##### 2.0以下版本需要引入以下依赖

```
<!--eureka server -->
		<dependency>
			<groupId>org.springframework.cloud</groupId>
			<artifactId>spring-cloud-starter-eureka-server</artifactId>
		</dependency>
```

##### 2.0以上版本需要引入以下依赖

```
<!--eureka server -->
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-netflix-eureka-server</artifactId>
        </dependency>
```

### application.yml

```
server:
  port: 8761

eureka:
  instance:
    hostname: localhost
  client:
    #服务器端不注册eureka
    registerWithEureka: false
    #服务器端不需要抓取服务列表
    fetchRegistry: false
    serviceUrl:
      #注册中心地址
      defaultZone: http://${eureka.instance.hostname}:${server.port}/eureka/
  server:
    enable-self-preservation: false # 设为false，关闭自我保护
    eviction-interval-timer-in-ms: 40000  # 清理间隔（单位毫秒，默认是60*1000）

```

### DemoEurekaApplication.java

Eureka Server需要在启动类上添加```@EnableEurekaServer```注解

```
@EnableEurekaServer
@SpringBootApplication
public class DemoEurekaApplication {

    public static void main(String[] args) {
        SpringApplication.run(DemoEurekaApplication.class, args);
    }

}

```

### 运行效果

启动项目后浏览器访问*http://localhost:8761*

![](https://upload-images.jianshu.io/upload_images/15504753-c69a371ef68e60d8.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

# 微服务提供者-Eureka Client

### idea创建

项目创建方式同上，在选择组件时选择**web->web**可以直接引入web依赖

![](https://upload-images.jianshu.io/upload_images/15504753-89c568e57c8d4d36.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### pom

```
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.1.1.RELEASE</version>
        <relativePath/> <!-- lookup parent from repository -->
    </parent>
    <groupId>com.ljq</groupId>
    <artifactId>demo_client1</artifactId>
    <version>0.0.1-SNAPSHOT</version>
    <name>demo_client1</name>
    <description>Demo project for Spring Boot</description>

    <properties>
        <java.version>1.8</java.version>
        <spring-cloud.version>Greenwich.RC2</spring-cloud.version>
    </properties>

    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        <!--eureka client Spring Cloud 2.0.0以上引入-->
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
        </dependency>

        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
    </dependencies>

    <dependencyManagement>
        <dependencies>
            <dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-dependencies</artifactId>
                <version>${spring-cloud.version}</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
        </dependencies>
    </dependencyManagement>

    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
            </plugin>
        </plugins>
    </build>

    <repositories>
        <repository>
            <id>spring-milestones</id>
            <name>Spring Milestones</name>
            <url>https://repo.spring.io/milestone</url>
            <snapshots>
                <enabled>false</enabled>
            </snapshots>
        </repository>
    </repositories>

</project>

```

##### 2.0以下版本需要引入以下依赖

```
<!--eureka client-->
		<dependency>
			<groupId>org.springframework.cloud</groupId>
			<artifactId>spring-cloud-starter-eureka</artifactId>
		</dependency>
```

##### 2.0以上版本需要引入以下依赖

```
<!--eureka client-->
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
        </dependency>
```

### application.yml

```
spring:
  application:
    #微服务名称，名字中不要出现下划线“_”，否则在使用Feign时会报错
    name: service-client1

eureka:
  client:
    serviceUrl:
      #注册中心地址
      defaultZone: http://localhost:8761/eureka/
server:
  #服务开放端口
  port: 8762
```

### DemoClient1Application.java

2.0版本以下需要在启动类添加```@EnableEurekaClient```注解，
2.0版本以上则无需添加注解。

```
@SpringBootApplication
@RestController
public class DemoClient1Application {

    /**
     * 测试接口
     * @return
     */
    @GetMapping("/test")
    public String test(){
        return "测试成功！";
    }

    public static void main(String[] args) {
        SpringApplication.run(DemoClient1Application.class, args);
    }

}
```

### 运行效果

##### 运行项目后查看注册中心。

![](https://upload-images.jianshu.io/upload_images/15504753-6e15d20ba42e6590.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
service-client1服务已经注册到注册中心。

##### 查看客户端接口

浏览器输入*http://localhost:8762/test*

![](https://upload-images.jianshu.io/upload_images/15504753-b4032bcd4f568636.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

# 参数配置

```
server:
  port: 8761

eureka:
  instance:
    hostname: localhost
    #注册中心接受client注册间隔，默认为90s，不可小于leaseRenewalIntervalInSeconds
    lease-expiration-duration-in-seconds: 30
    #client发给注册中心心跳的频率，默认为30s，若注册服务过慢可修改此值
    lease-renewal-interval-in-seconds: 15
    # 设置微服务调用地址为IP优先（默认为false）
    prefer-ip-address: true
    # 只有prefer-ip-address=true时才会生效
    ip-address: 192.168.6.66
  client:
    #服务器端不注册eureka
    registerWithEureka: false
    #服务器端不需要抓取服务列表
    fetchRegistry: false
    serviceUrl:
      #注册中心地址
      defaultZone: http://${eureka.instance.hostname}:${server.port}/eureka/
    #表示客户端多久去拉取一次服务注册信息，默认为30秒
    registry-fetch-interval-seconds: 30
    #开启健康检查（依赖spring-boot-starter-actuator）
    healthcheck:
      enabled: true
  server:
    #是否开启自我保护模式，默认为true
    enable-self-preservation: false
    #清理无效节点的时间间隔，单位毫秒，默认是60*1000
    eviction-interval-timer-in-ms: 40000
```

# End

### 项目源码地址

- [eureka-server](https://github.com/LiJinQ/demo_eureka.git)

- [eureka-client](https://github.com/LiJinQ/demo_client1.git)

### 参考资料

- [SpringCloud教程第一篇](https://www.fangzhipeng.com/springcloud/2017/07/12/sc01-eureka/)

- [spring cloud eureka 参数配置](https://segmentfault.com/a/1190000008378268)

- [SpringCloud系列第03节之注册中心Eureka进阶](https://jadyer.cn/2017/01/17/springcloud-eureka-advance/)


