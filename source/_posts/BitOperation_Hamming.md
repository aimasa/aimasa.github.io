---
title: 位运算_汉明距离
copyright: true
date: 2019-02-19 11:01:08
categories:
- 位运算代码示例
- 汉明距离
tags:
- 位运算代码示例
---

汉明距离是用来计算两个不同字符串之间不同的值有多少个。二进制中就是两个二进制相减，结果中1的个数
<!--more-->

    def hamming_weight(x): #不同bit的数量
        weight = 0
        while x > 0:
            weight += x & 1
            x >>= 1
    return weight
    
    def qr_decode_format(fmt):
        best_fmt = -1
        best_dist = 15
        for test_fmt in range(0, 32):
            test_code = (test_fmt << 10) ^ qr_check_format(test_fmt << 10)#得出（0-2^5）左移10位然后能被生成子整除的5bit编码
            '''
             计算汉明距离的公式
            '''
            test_dist = hamming_weight(fmt ^ test_code)
            if test_dist < best_dist:
                best_dist = test_dist
                best_fmt = test_fmt
            elif test_dist == best_dist: #如多个码字与fmt距离相同，则都不选（就是不止一个与原信息的汉明距离相同）
                best_fmt = -1
    return best_fmt

用fmt^test_code算出fmt和test_code中二进制的不同，再去统计相减结果为1的个数，最后得出汉明距离。（也可以叫做计算出汉明权重）

汉明权重：指一个字符串中非零字符的个数；对于二进制串，即其中‘1’的个数。

