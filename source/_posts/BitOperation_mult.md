---
title: 位运算_乘法
copyright: true
date: 2019-02-19 10:31:26
categories:
- 位运算代码示例
- 伽罗瓦域的乘法
tags:
- 位运算的代码示例
---

伽罗瓦域的加减法运算都是用异或的方法运算的，所以它的乘法和普通的乘法也是不一样的。
<!--more-->

<center>{% qnimg BitOperation_mult/field_multiplication.png %}
</center>

其中乘法运算完成后，结果相加的步骤和普通乘法不一样，它用的是异或

所以根据[该链接中的代码](https://en.wikiversity.org/wiki/Reed%E2%80%93Solomon_codes_for_coders)举例:
    
    def cl_mul(x,y):
    '''Bitwise carry-less multiplication on integers'''
        z = 0
        i = 0
        while (y>>i) > 0:
            if y & (1<<i): #判断y?=1
                z ^= x<<i #判断x乘的是y的第几位
            i += 1
    return z

