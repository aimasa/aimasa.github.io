---
title: 位位运算_除法
copyright: true
date: 2019-02-19 10:48:51
categories:
- 位运算代码示例
- 除法求余
tags:
- 位运算代码示例
---

这个除法是普通的除法，用来求余的除法。

<!--more-->

<center>![](divided.png)</center>

所以根据[该链接中的代码](https://en.wikiversity.org/wiki/Reed%E2%80%93Solomon_codes_for_coders)举例:

    def qr_check_format(fmt):
        g = 0x537    # = 0b10100110111 in python 2.6+
        for i in range(4,-1,-1):
            if fmt & (1 << (i+10)): #判断第一位是否为零（二进制除法不需要进位借位）
            fmt ^= g << i #开始计算
    return fmt

其中for i in range(4,-1,-1)里面i的范围是[4,0]的原因是：上述例子中举例用的生成器10100110111长度和用于和它进行相除的除数000111101011001长度相差为4，所以设置i的范围为[4,0]
（其中生成器做被除数）