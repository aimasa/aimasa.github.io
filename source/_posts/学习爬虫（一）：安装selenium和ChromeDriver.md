---
title: 学习爬虫（一）：安装selenium和ChromeDriver
copyright: true
date: 2019-05-10 17:33:44
categories:
- 爬虫、python
- 安装selenium和ChromeDriver
tags:
- 爬虫
---

关于爬虫的学习，先从selenium这个库开始学。

<!--more-->


# 关于selenium

先是
 
    pip install selenium

安装完这个，我以为就odk了，然后在pycharm编译器里面跟着网上给的代码敲了一段

    from selenium import webdriver

    browser = webdriver.Chrome()
    browser.get('https://www.baidu.com')

结果报错了

    This inspection detects names that should resolve but don't. Due to dynamic dispatch and duck typing, this is possible in a limited but useful number of cases. Top-level and class-level items are supported better than instance items.

大体意思就是模块不存在，然后我以为是pip install时候出错了，就回去再pip install一遍，结果发现，还是没有用。

然后上网找解决方案，跟着网上的教程去看了pycharm这个IDE里面设置的python环境的路径：

+ **File > settings > project:xxxxxxx(你导入pycharm的文件夹)  > project Interpreter**

就发现我pip install下载下来的selenium库地址和之前设置在IDE里面python环境的地址不一样，所以没法找到selenium这个模块。

然后去对这个IDE里面的python路径进行更改。

## 更改

<center>{% qnimg 学习爬虫（一）：安装selenium和ChromeDriver/pycharm.png %}</center>

点**show all……**

接着

<center>{% qnimg 学习爬虫（一）：安装selenium和ChromeDriver/add.png %}</center>

找到对应python-pip的相关python目录所在地，然后更改IDE依赖环境目录。

## 查找目录所在地

我是再次pip install selenium，然后看到了selenium存的位置，再根据这个位置找到相关信息。

# ChromeDriver

我弄好了，发现不再显示该模块不存在的信息了之后，运行了一遍，发现还是报错了。

    Service chromedriver unexpectedly exited

出现了这个报错信息。

## 装插件

所以我去安装chromedriver驱动[下载地址](http://npm.taobao.org/mirrors/chromedriver/)

下载好了之后，解压，再新建一个文件夹，里面只放解压出来的exe文件，再去控制面板里面配置环境path

然后再运行，成功调出了Chrome浏览器，并且让它自动进入百度搜索页面。

# browser.get()

browser.get()括号里面的字符串必须填写完整的网址，不然会显示无效网址的错误提示。

# 参考资料

[参考资料](https://blog.csdn.net/qq_41188944/article/details/79039690)