---
title: markdown插入公式
copyright: true
date: 2019-02-27 15:22:31
categories:
- markdown
- markdown插入公式
tags:
---

实测在[该博客](https://www.jianshu.com/p/054484d0892a)中提到的四种在markdown中插入公式的办法

<!--more-->


# 办法1：借助[在线公式编辑器](https://www.codecogs.com/latex/eqneditor.php)

<center>![](fangfa1_1.png)</center>

<center>=======></center>

<center>![](fangfa1_2.png)</center>

<center>![](fangfa1_3.png)</center>

# 办法2：借助Google Chart服务器

在需要插入公式的位置键入如下代码，并在“在此插入Latex公式”中改写成公式即可。

    <img src="http://chart.googleapis.com/chart?cht=tx&chl= 在此插入Latex公式" style="border:none;">

<center>![](fangfa2.png)</center>

# 办法3：借助forkosh服务器

与上一方法类似

<center>![](fangfa3.png)</center>

# 办法4：借助MathJax引擎！

在首部添加脚本代码，然后就可以在该文内像在latex中一样书写公式

<center>![](fangfa4.png)</center>
 
# 总结

只有方法1和方法4可行，方法1代码多，但是书写简单，无需记住数学公式的参考代码，但是达不到我想要的效果。方法4简单方便。只要你记住数学公式的书写代码，就没有问题了。

但是他们都是会让数学公式独占一行，所以我谷歌了一下怎么办，根据[这篇博文](http://stevenshi.me/2017/06/26/hexo-insert-formula/)去更改了我博客hexo框架和next主题的设置

（还有根着[这个博文](https://blog.csdn.net/wgshun616/article/details/81019687)改了一下渲染什么的，我也不知道有什么用.）

## 安装和配置

下载插件

    $ npm install hexo-math --save

在站点配置文件 _config.yml 中添加：

    math:
      engine: 'mathjax' # or 'katex'
      mathjax:
        # src: custom_mathjax_source
        config:
          # MathJax config

在 next 主题配置文件中 themes/next-theme/_config.yml 中将 mathJax 设为 true:

    # MathJax Support
    mathjax:
      enable: true
      per_page: false
      cdn: //cdn.mathjax.org/mathjax/latest/MathJax.js?config=TeX-AMS-MML_HTMLorMML

## 使用

就是在数学公式的书写代码前面后面各加一个$符号。

然后弄完了之后我的有些图片能显示有些图片不能，我也不知道为什么，所以我经过激烈的心理斗争，决定，算了，不用这个方法了，前面的方法也挺好用的。

## 最后的使用办法

我找到了[这个博客](https://blog.csdn.net/emptyset110/article/details/50123231)
根据它里面教我的去执行

    npm install hexo-math --save
    hexo math install

然后更改

用编辑器打开marked.js（在./node_modules/marked/lib/中）

Step 1:
    escape: /^\\([\\`*{}\[\]()# +\-.!_>])/,

替换成

    escape: /^\\([`*\[\]()# +\-.!_>])/,

这一步是在原基础上取消了对\\,\{,\}的转义(escape)

Step 2:
    em: /^\b_((?:[^_]|__)+?)_\b|^\*((?:\*\*|[\s\S])+?)\*(?!\*)/,

替换成

    em:/^\*((?:\*\*|[\s\S])+?)\*(?!\*)/,

这样一来MathJax就能与marked.js共存了。

就这样，我又能很好地插入数学公式了。


# 数学公式

[参考文档](https://juejin.im/post/5a6721bd518825733201c4a2)

[该文档提供的下载地址](https://link.juejin.im/?target=http%3A%2F%2Ffiles.cnblogs.com%2Fhoukai%2FLATEX%25E6%2595%25B0%25E5%25AD%25A6%25E7%25AC%25A6%25E5%258F%25B7%25E8%25A1%25A8.rar)

如果该链接打不开，请进入参考文档去寻找正确地址。