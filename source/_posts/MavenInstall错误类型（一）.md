---
title: MavenInstall错误类型（一）
copyright: true
date: 2019-04-03 08:57:51
categories:
- 运行error
- Maven Install错误类型：Failed to execute goal org.apache.maven.plugins:maven-surefire-plugin:2.22.1
tags:
- Maven Install error
---

工具：STS

在Maven Install加载pom.xml里面添加的依赖时候，结果出了错误：Failed to execute goal org.apache.maven.plugins:maven-surefire-plugin:2.22.1

<!--more-->

我百度了一会，各种方法都试过，却没有用，结果发现是我之前点击Spring Boot App这里让这个项目运行起来了，但是却没有关闭，然后再点击Maven Install，所以报错了。