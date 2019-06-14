---
title: springBootTest注入失败
copyright: true
date: 2019-06-05 09:26:51
categories:
- spring相关
- Test
- SpringBootTest注入失败
tags:
- 运行error
---

在用测试测dao层时候，虽然都有用注解:dao层用注解@Repository标记，Test类里面注入用@Autowired标记。

但是还是显示注入失败。

<!--more-->

查了半天方法，最后去了spring boot的运行类里面加上了指定包扫描@ComponentScan(basePackages = {"扫描的包的共有的包名部分"})

然后刚刚发现，之所以扫不出来是因为我包名的命名错误

{% qnimg springBootTest注入失败/package.png %}
