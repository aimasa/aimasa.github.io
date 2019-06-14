---
title: 俄罗斯农夫算法
copyright: true
date: 2019-02-20 10:01:19
categories:
- 位运算代码示例
- 俄罗斯农夫算法
tags:
- 位运算代码示例
- 算法
---

  俄罗斯农民乘法是在一个乘数和被乘数之间进行操作，降低运算需要花费的时间。
  <!--more-->
[参考博客](http://www.cnblogs.com/yangqingli/p/4745167.html)  

[参考代码来源](https://en.wikiversity.org/wiki/Reed%E2%80%93Solomon_codes_for_coders)

这个算法流程是：乘数和被乘数，一个乘2一个除2，如果被乘数为奇数，则它除以2后去掉余数。如果被乘数是偶数，则继续往下乘除。
等被乘数除以2的结果为1的时候，运算结束，把被乘数为奇数的结果所对应的乘数乘以2的结果相加，最终结果为正确乘积。

      52 x 25
      104  12
      208   6
      416   3
      832   1
     -----------
      52+416+832=1300

   以下是位运算代码示例： 

    
    def gf_mult_noLUT(x, y, prim=0, field_charac_full=256, carryless=True):
    '''Galois Field integer multiplication using Russian Peasant Multiplication algorithm (faster than the standard multiplication + modular reduction).
    If prim is 0 and carryless=False, then the function produces the result for a standard integers multiplication (no carry-less arithmetics nor modular reduction).'''
        r = 0
        while y: # while y is above 0
            if y & 1: r = r ^ x if carryless else r + x # y is odd, then add the corresponding x to r (the sum of all x's corresponding to odd y's will give the final product). Note that since we're in GF(2), the addition is in fact an XOR (very important because in GF(2) the multiplication and additions are carry-less, thus it changes the result!).
            y = y >> 1 # equivalent to y // 2
            x = x << 1 # equivalent to x*2
            if prim > 0 and x & field_charac_full: x = x ^ prim # GF modulo: if x >= 256 then apply modular reduction using the primitive polynomial (we just subtract, but since the primitive number can be above 256 then we directly XOR).
    return r

      
其中y&1是用来判断y是奇数还是偶数，if carryless是用来判断这个是普通乘法还是伽罗瓦乘法，然后做出相应的算法

prim > 0是判断是不是伽罗瓦乘法，是的话，再对结果进行相应的处理

x & field_charac_full这个是判断x是否大于field_charac_full，大于的话就模field_charac_full以控制x在相应范围内，小于的话就可以直接输出结果。