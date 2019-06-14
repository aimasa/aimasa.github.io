---
title: SpringCloud学习
copyright: true
date: 2019-06-05 14:28:23
categories:
- Spring Cloud学习（一）
- 微服务的概念
tags:
- Spring Cloud
---

之前我写的代码都是单架构模式，就是把多个功能放在同一个应用里面，就像之前写的小程序后台一样

修改了部分代码，就得整体再重新打包编译，没有修改的部分，甚至不需要使用到这部分代码的功能也因为重新打包编译而全部不能使用……

所以要通过分布式和集群的方式把单架构模式改造一下

<!--more-->

# 微服务概念

一个springboot就是一个微服务，而且这个springboot做的事情很单一。在我的理解里面，就是把之前的一个整体的分层架构的springboot分成controller、service、dao这三个部分，

# 微服务注册

虽然把springboot分了三部分，但这三部分应该怎么建立连接，相互之间应该怎么进行联络，所以就要引入一个微服务注册中心的概念了。这个微服务注册中心在 springcloud 里就叫做 eureka server, 通过它把就可以把微服务注册起来，以供将来调用。

# 微服务访问

一个服务通过微服务注册中心定位并访问另外一个微服务。

# 分布式概念

博客里介绍：本来一个spring boot就能完成的任务现在分布在多个spring boot里面做。
就是不同的部分的微服务可以由不同的团队去开发，耦合度低。

# 集群

就是同样的功能，但是用的端口不一样，如果8080挂了，我可以用8081这个端口的这个功能的微服务，这叫高可用==。

# 参考博客

[Spring Cloud入门（对这篇博客做的笔记）](http://how2j.cn/k/springcloud/springcloud-distribution/2037.html)