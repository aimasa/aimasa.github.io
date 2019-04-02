---
title: 为Next主题添加统计阅读量的功能以及开评论
copyright: true
date: 2019-03-29 09:09:00
categories:
- hexo 更新配置
- 添加统计阅读量以及打开评论
tags:
- hexo
---


# 需要准备工作

我先在leancloud注册了一个账号，用来统计阅读量和去Next主题里设置开放评论用。

<!--more-->

# 具体步骤

## 开放评论

注册完这个账号，然后创建项目（企业开发的话要钱，所以选择个人开发）。接着去Next主题里面设置

    valine:
      enable: true
      appid:  # your leancloud application appid你点进你leancloud账号里新建的项目里面，然后点进设置，会看到项目自动生成的appid，然后复制过来
      appkey: # your leancloud application appkey它的位置就在appid下面一行
      notify: false # mail notifier , https://github.com/xCss/Valine/wiki通知
      verify: false # Verification code验证码（评论前要输入的）
      placeholder: Just go go # comment box placeholder评论框提示你输入的话语
      avatar: mm # gravatar style默认头像
      guest_info: nick,mail # custom comment header评论前要输入的信息
      pageSize: 10 # pagination size一页默认展示的评论数

然后就能开评论了，因为我刚开始设置输入错了appid和appkey，出现了402错误（反正会提示你哪错了，就不做解释辽。）

### 给评论加上邮件通知

这个是跟着[这个博客](https://github.com/zhaojun1998/Valine-Admin)的方法来的，虽然之前的博客教我怎么用leancloud自带的邮件通知功能去提醒别人评论已回复，但是说是因为是正在开发的功能，还不太稳定，同时还会被要求开验证码，觉得很不方便，所以我换了个博客跟着用第三方的邮件提醒功能。

因为过程有些繁琐，我也只是跟着它的方法来的，所以就只在这放个链接好了。这是个开源项目。

## 添加统计阅读量功能

在你新建的项目的存储里面，新建一个叫Counter的class，ACL权限选择无限制，里面的appid和appkey和上面获取的是一样的，在这里就不讲是怎么去获取的了。

    # Show number of visitors to each article.
    # You can visit https://leancloud.cn get AppID and AppKey.
    leancloud_visitors:
      enable: true
      app_id: #AppID
      app_key: #AppKey

# 参考资料

[参考博客(添加统计阅读量功能)](https://notes.doublemine.me/2015-10-21-%E4%B8%BANexT%E4%B8%BB%E9%A2%98%E6%B7%BB%E5%8A%A0%E6%96%87%E7%AB%A0%E9%98%85%E8%AF%BB%E9%87%8F%E7%BB%9F%E8%AE%A1%E5%8A%9F%E8%83%BD.html#%E9%85%8D%E7%BD%AELeanCloud)

[参考博客(添加统计阅读量功能)](https://valine.js.org/notify.html)

[参考博客(让博客支持评论功能)](https://github.com/xCss/Valine/wiki/Valine-%E8%AF%84%E8%AE%BA%E7%B3%BB%E7%BB%9F%E4%B8%AD%E7%9A%84%E9%82%AE%E4%BB%B6%E6%8F%90%E9%86%92%E8%AE%BE%E7%BD%AE)

[第三方邮件回复自带定时器](https://github.com/zhaojun1998/Valine-Admin/blob/master/高级配置.md#自定义邮件模板)

