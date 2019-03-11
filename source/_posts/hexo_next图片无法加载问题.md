---
title: hexo_next图片无法加载问题
copyright: true
date: 2019-03-05 19:40:52
categories:
- hexo错误笔记
- hexo_next图片无法加载问题
tags:
- hexo err类型
---

错误描述：自从加入了可以插入公式的插件后，经常碰到图片无法显示的问题，去查看页面代码，发现
    
    ！[]()

无法被hexo g解析生成图片链接

<!--more-->

用谷歌不管怎么搜索关键词都没有用，所以我直接删掉了".deploy_git"，然后再用
    
    hexo d -g

重新生成静态页面并且部署在网站上，然后图片成功生成了。