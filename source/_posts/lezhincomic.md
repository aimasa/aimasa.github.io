---
title: Lezhin爬虫使用
copyright: true
date: 2019-09-08 18:19:11
categories:
- 爬虫
- 漫画
tags:
- 爬虫
---

# Lezhin爬虫使用方法



lezhin爬虫配置文件介绍

<!--more-->



## 配置文件模板

```

#需要爬的漫画信息
[comic_info]
comic_chinese_name = 我的哥哥我的老师
comic_name = mybromyssam
series_id_first = 25
series_id_last = 25
; ---------------------------------
; 需要存放的文件路径以及压缩格式
[folder_info]
zip_type = zip
folder_name_header = H:/

; ----------------------------------
; 账号登录网站后得到的必要信息
[comic_request_info]
access_token = 
comic_id = 
lezhin_cookie = 

;------------------------------------
;下载完一话漫画下第二话需要等待时间
[spare_time]
spare_time = 10
```



注：配置文件后缀名为 .ini，也可以是.config

该脚本打包成的exe使用环境为 window10（我觉得window系列的系统都能用它，我没试过，因为我的电脑是window10，所以我这里标注的使用环境仅为window10），运行之后，想中断运行的话按`ctrl+c`

只能爬取自己已经购买了的漫画，或者网站确定为免费的漫画。

## 配置参数详解：

### comic_name、comic_id

<center>{% qnimg GitReadMePicture\LezhinComic\first.png %}</center>
点进去之后就能在浏览器的路径上找到对应漫画的comic_name

<center>{% qnimg GitReadMePicture\LezhinComic\getcomicname.png %}</center>
接着往下拉，按下F12点，进想要下载的漫画的第一话（随便哪话都能找到以下信息）

=========================================================================================================

重点分割线

======================================================================================================



点开`NetWork`这栏

<center>{% qnimg GitReadMePicture\LezhinComic\getLezhincookie.png %}</center>
找到红框中的链接内容，点开之后会在旁边弹出一个小框

<center>{% qnimg GitReadMePicture\LezhinComic\comic_nameandid.png %}</center>
在这个小框中，划拉到`header`这栏最下面，会看到Query xxxx，里面alias就是配置文件里对应的`comic_name`，`_`的值就是配置文件里的`comic_id`【ps:`comic_id`是我命名有问题，我也不知道`_`是代表什么。】

### lezhin_cookie

在`header`这栏中间会看到`cookie`，这个值是`lezhin_cookie`

<center>{% qnimg GitReadMePicture\LezhinComic\cookie.png %}</center>
<center>{% qnimg GitReadMePicture\LezhinComic\togetaccesstoken.png %}</center>
### access_token

按`F5`刷新，切换按钮到红框指定的位置，根据下图的指示，能够找到配置文件中`access_token`对应的值。

<center>{% qnimg GitReadMePicture\LezhinComic\getaccesstoken.png %}</center>
【因为如果哪天爬不到文件，有一个原因是cookie失效，或者access_token失效，那时候就需要重复一遍获取cookie和access_token的步骤去更新它们】

### comic_chinese_name、series_id_first、series_id_last、zip_type、folder_name_header、spare_time

这些参数都是自己可以随便定义的。

`comic_chinese_name`：自己定义的漫画的中文名

`series_id_first、series_id_last`：想要下载漫画的集数范围。例：如果想要下载该漫画1-15集，那么`series_id_first=1`，`series_id_last=15`  ;如果只想下载15集，那么`series_id_first = 15`，`series_id_last=15`

`zip_type`：想压缩成的格式。例：`zip_type = zip`，文件则被压缩成zip格式

`folder_name_header`：想要存储的地址前缀。例：想要把`我的哥哥我的老师`漫画全部存在`D:/comic`里面，然后`folder_name_header = D:/comic`，运行此脚本，会在`D:/comic`里面生成一个`我的哥哥我的老师`这个总文件夹，里面会有你下载的漫画集数（下载下来的图片是分集装好）。

`spare_time`：爬完上一话，继续爬下一话时候的间隔时间。因为怕爬取漫画过快，被检测发现是用了脚本在爬，所以加了一个`spare_time`，可以`spare_time=0`，但是可能会有被封号的风险哟。`spare_time=1`，就意思是间隔时间为1s。

> eg:    alias : myxxxx  =>  comic_name = myxxxx



接着把自己的配置文件在本地的存放位置在程序开始时填一下，如：配置文件名为：`lezhin.config`，放在电脑的`D:/configfile`这个文件夹下面，运行程序，出现`配置文件路径：`的字样时候，输入`D:/配置文件/lezhin.config`。好了，回车，开始爬数据了，如果突然闪退，可以看log里面的记录。



## 总结

这个脚本对我来说现阶段最麻烦的就是cookie和access_token这两个参数需要手动获取。emmm看看能不能改进吧。

因为是在闲暇时候摸鱼写出来的脚本，可能会有很多不足，欢迎大家给我提issue。

## 爬虫代码

[lezhin爬虫](https://github.com/aimasa/LezhinComic)





## 注：禁止用此爬虫爬下来的漫画进行二次贩卖！！！！！！！！！！请尊重他人版权！！！！！！！！！！！！！！！！此爬虫仅做学习用途！！！！！！！！！！！！！

注：禁止用此爬虫爬下来的漫画进行二次贩卖！！！！！！！！！！请尊重他人版权！！！！！！！！！！！！！！！！此爬虫仅做学习用途！！！！！！！！！！！！！