---
title: java打包供maven使用
copyright: true
date: 2019-04-28 20:36:08
categories:
- java语法
- java打包供maven使用
tags:
---

因为之前老师说好的说让我自己试试怎么对项目打包，然后把包封装成相关依赖，这样可以直接在maven里面添加依赖，然后对包进行调用。

<!--more-->

# 把项目打包

用的是fatjar对项目进行的打包，又快又方便。

我用的是eclipse4.4版本，对项目右键会有build FatJar的选项，然后点进去，选择好生成jar包的位置【注意：包存放的文件夹名中间不能有空格，不然包转成依赖语句会报错】，然后jar包就生成了。

# 把包转换成依赖

首先win+R进入cmd命令行，然后cd到该jar包存放的位置，再然后输入以下命令行

    mvn install:install-file -Dfile=jar包的位置 -DgroupId=上面的groupId -DartifactId=上面的artifactId -Dversion=上面的version -Dpackaging=jar

-----------------------------------[摘自博客](https://blog.csdn.net/u012759397/article/details/53437502)

然后回车，一顿操作猛如虎，再新建一个项目，添加：

    <dependency>
    <groupId>com.qrcode</groupId><!--DgroupId等于的参数值-->
    <artifactId>qr</artifactId><!--DartifactId等于的参数值-->
    <version>0.1</version><!--Dversion等于的参数值-->
    </dependency>

# 参考资料

[博客](https://blog.csdn.net/u012759397/article/details/53437502)