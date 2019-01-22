---
layout:     post
title:      官方文档阅读（一）
subtitle:   Spring Cloud（GreenWich.RC2）
date:       2019-01-22
author:     LJQ
header-img: img/post-bg-read-springcloud2.jpg
catalog: true
tags:
    - Translation 
    - Spring Cloud
---

# 前言

之前看了公众号《石杉的架构笔记》的一篇推送《英语差的程序员如何无障碍阅读官方文档》，故决定从今天开始按照石杉老师的方法，每天花上半个小时阅读官方文档，锻炼自己的阅读英文文档能力，以此文章以日记形式每日更新，用于督促自己。

# 2019-01-22

>Spring Cloud provides tools for developers to quickly build some of the common patterns in distributed systems (e.g. configuration management, service discovery, circuit breakers, intelligent routing, micro-proxy, control bus). Coordination of distributed systems leads to boiler plate patterns, and using Spring Cloud developers can quickly stand up services and applications that implement those patterns. They will work well in any distributed environment, including the developer’s own laptop, bare metal data centres, and managed platforms such as Cloud Foundry.
Version: 1.0.0.BUILD-SNAPSHOT

>Spring Cloud 为开发者快速构建分布式系统的通用组件（如，配置管理，服务发现，断路器，智能路由，微服务，控制总线）提供工具。分布式系统间的协调导致**锅炉板模式**，使得Spring Cloud开发者基于这些样板可以快速建立服务和应用。他们适应于任何分布式环境，包括开发者自己的笔记本电脑本地环境、裸机数据中心和托管平台，如**Cloud Foundry**
版本：1.0.0.BUILD-SNAPSHOT


- **锅炉板模式**：初看时有些迷茫，就查了一下，发现开源中国的[frankiegao123](https://www.oschina.net/question/3210513_2218453)将其翻译为**样板**或**套路**，意为Spring Cloud为开发者们提供各种样板，使得开发者可以快速部署分布式系统

- **Cloud Foundry**：是业界第一个开源PaaS云平台，它支持多种框架、语言、运行时环境、云平台及应用服务，使开发人员能够在几秒钟内进行应用程序的部署和扩展，无需担心任何基础架构的问题。
