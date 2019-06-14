---
title: 文件上传到mongdb的方法
copyright: true
date: 2019-05-23 16:38:02
categories:
- spring相关
- mongodb存储
tags:
- mongodb
---

思路：先构造一个请求体，然后获取文件类型，进行比对，如果不是excel类型拒绝，是则存入请求体.

把文件内容转成输入流，再从输入流中读取转成二进制byteArray类型

对这个二进制数组做哈希