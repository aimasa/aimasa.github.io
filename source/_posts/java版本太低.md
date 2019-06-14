---
title: java版本太低
copyright: true
date: 2019-04-28 19:32:56
categories:
- 运行error
- java版本太低
tags:
- 运行error
---

导入一个本来是没有语法错误的项目，然后IDE到处报错，说是jdk版本太高，要更改成低版本。

<!--more-->

对着被导入的项目点击右键-> properties->java build path

然后双击JRE System Library，然后更换jre的版本号（和本地装的jre的版本号一致）

<center>{% qnimg java版本太低/confi.png %}</center>

然后成功