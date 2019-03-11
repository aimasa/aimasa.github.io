---
title: hexo deploy不报错但是无法显示
copyright: true
date: 2019-02-20 11:20:44
categories:
- hexo错误笔记
- hexo deploy
tags:
- hexo err类型
---

    hexo g
    hexo s
    hexo d
错误描述：三步下来，hexo s这一步是没有错的，也能在本地预览已经生成好的静态页面，但是hexo d之后，不报错，但是就是没法正确部署到github page上面。而且还收到github发来的邮件，说是主题不被支持。
<!--more-->

删掉.deploy_git，然后git clean ,接着hexo d -g重新生成这个文件夹，并且将页面部署到github page中

