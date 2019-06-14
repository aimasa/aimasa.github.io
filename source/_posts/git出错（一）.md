---
title: git出错（一）
copyright: true
date: 2019-05-09 11:25:38
categories:
- git
- git出错
- src refspec master does not match any.
tags:
- git出错
---

报错：src refspec master does not match any

<!--more-->

# 操作过程

    git init
    git commit -m "first commit"
    git remote add origin git@github.com:aimasa/xxxxxxxx
    git push -u origin master

然后再回车的时候，git bash就报错了

    src refspec master does not match any

# 原因

我没有提交任何内容，我的本地库（.git）是空的，所以第一次push是提交一个空项目，里面没有任何东西，所以报错了

# 解决方法

把代码提交到本地，然后再推送一次

    git add .
    git commit -m "xxxx"
    git push -u origin master

推送成功