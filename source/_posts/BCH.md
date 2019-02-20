 

title: QR_detail_BCH
copyright: true
date: 2019-01-23 21:12:48
categories:
- QR
- 了解二维码
- BCH解码器
tags:
- QR
---
# BCH

这是一类通用的纠错码，BCH码：现代里德 - 所罗门码的父族，以及有基本的检测和校正机制。
格式化信息用BCH码编码，该BCH码允许检测和校正一定数量的比特错误。
检查编码信息的过程类似于长除法过程，只是使用异或代替减法（a remainder of zero：余数为零）
<!--more-->

当格式代码被所谓的代码生成器“分割”时，格式代码应产生的余数为零。
QR格式代码使用生成器10100110111.


（ demonstrated：示范）
（0x前缀表示该数字是十六进制，0b是二进制）
用fmt与生成子相除，返回余数
    
    def qr_check_format(fmt):
        g = 0x537    # = 0b10100110111 in python 2.6+
        for i in range(4,-1,-1):
            if fmt & (1 << (i+10)): #判断第一位是否为零（二进制除法不需要进位借位）
            fmt ^= g << i #开始计算
    return fmt
其中for i in range(4,-1,-1)里面i的范围是[4,0]的原因是：上述例子中举例用的生成器10100110111长度和用于和它进行相除的除数000111101011001长度相差为4，所以设置i的范围为[4,0]

1字=2字节(1 word = 2 byte) 

1字节=8位(1 byte = 8bit) 

这个函数可以用于编码生成5bit的编码信息（就是需要被生成器整除的编码信息）

    encoded_format = (format<<10) ^ qr_check_format(format<<10)
它利用上面的计算余数的函数得出format<<10除以生成器的余数，然后反推出在信息为format<<10的情况下除以生成器时候余数为零的5bit的编码信息。

汉明距离是使用在数据传输差错控制编码里面的，汉明距离是一个概念，它表示两个（相同长度）字对应位不同的数量，我们以d（x,y）表示两个字x,y之间的汉明距离。对两个字符串进行异或运算，并统计结果为1的个数，那么这个数就是汉明距离。

    def qr_check_format(fmt):
        g = 0x537    # = 0b10100110111 in python 2.6+
        for i in range(4, -1, -1):
            if fmt & (1 << (i+10)):
                fmt ^= g << i
    return fmt
    
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
            test_dist = hamming_weight(fmt ^ test_code)
            if test_dist < best_dist:
                best_dist = test_dist
                best_fmt = test_fmt
            elif test_dist == best_dist: #如多个码字与fmt距离相同，则都不选（就是不止一个与原信息的汉明距离相同）
                best_fmt = -1
    return best_fmt

代码中用fmt ^ test_code，然后统计结果中1的比特的个数，以此来比较fmt和test_code的汉明距离，倘若有多个与fmt的汉明距离一样的test_code，那么就相当于没有可以用于匹配的值，如果只有一个值的话，那么那个最大相似度的值则就是fmt被损坏前的值

（overflow：溢出；Naively：天真地）
>We'd like to define addition, subtraction, multiplication, and division for 8-bit bytes and always produce 8-bit bytes as a result, so as to avoid any overflow. Naively, we might attempt to use the normal definitions for these operations, and then mod by 256 to keep results from overflowing.

mod2^8就是让加减乘除里面的结果小于2^8，在0-255的区间里面。具体参见笔记[伽罗瓦域部分](http://www.aimasa.github.io/2019/02/18/GaloisFields/#more)

在伽罗瓦域的乘法中，两个数相乘，它的值可能大于2^8，所以在进行乘法后，会对得出的结果进行取模（就是求余），而其中除数为100011101（0x11d）

100011101（0x11d）是Reed-Solomon代码的**通用**本原多项式。100011101表示8度多项式，其是“不可约的”（意味着它不能表示为两个较小多项式的乘积）。

素数多项式的附加信息（可以跳过）：什么是素数多项式？ 它相当于素数，但在伽罗瓦域中。 请记住，Galois Field使用的值为2的倍数作为生成器。 当然，在标准算术中，素数不能是2的倍数，但在伽罗瓦域中，它是可能的。 为什么我们需要素数多项式？ 因为要保持在场的边界，我们需要计算Galois场上方任何值的模数。 为什么我们不用Galois Field大小模数？ 因为我们最终会得到许多重复值，并且我们希望在字段中拥有尽可能多的唯一值，因此在使用素数多项式进行模数或XOR时，数字始终只有一个投影。