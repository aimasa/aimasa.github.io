 

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


这是一类通用的纠错码，BCH码：现代里德 - 所罗门码的父族，以及有基本的检测和校正机制。
格式化信息用BCH码编码，该BCH码允许检测和校正一定数量的比特错误。
检查编码信息的过程类似于长除法过程，只是使用异或代替减法（a remainder of zero：余数为零）
<!--more-->

# BCH

## BCH错误检测

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

如果记格式信息为F，生成子为G，(F<<10)/G的商为Q、余数为R (即F<<10 == Q*G + R)，则最终的编码信息

>  C = (F << 10) ^ ((F << 10) mod G) = (Q*G + R) - R = Q*G

从而C应当是能够被G整除的。如果收到的C不能被G整除，说明传输出错了。

## BCH纠错

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

## 伽罗瓦域乘法和减法

mod2^8就是让加减乘除里面的结果小于2^8，在0-255的区间里面。具体参见笔记[伽罗瓦域乘法部分](http://www.aimasa.github.io/2019/02/20/GaloisField-mult/)

在伽罗瓦域的乘法中，两个数相乘，它的值可能大于2^8，所以在进行乘法后，会对得出的结果进行取模（就是求余），而其中除数为100011101（0x11d）

100011101（0x11d）是Reed-Solomon代码的**通用**本原多项式。100011101表示8度多项式，其是“不可约的”（意味着它不能表示为两个较小多项式的乘积）。

素数多项式的附加信息（可以跳过）：什么是素数多项式？ 它相当于素数，但在伽罗瓦域中。 请记住，Galois Field使用的值为2的倍数作为生成器。 当然，在标准算术中，素数不能是2的倍数，但在伽罗瓦域中，它是可能的。 为什么我们需要素数多项式？ 因为要保持在场的边界，我们需要计算Galois场上方任何值的模数。 为什么我们不用Galois Field大小模数？ 因为我们最终会得到许多重复值，并且我们希望在字段中拥有尽可能多的唯一值，因此在使用素数多项式进行模数或XOR时，数字始终只有一个投影。

另外我们可以发现，用加法也能得出我们想要的结果：$\, 2^{x}\, \cdot \, 2^{y}\, =\, 2^{x+y}\, $

## 基于对数的乘法（次幂的加法）

但是我们怎么样才能知道10001001是α的几次幂呢？这个问题被称为[离散对数](http://aimasa.github.io/2019/02/27/%E7%A6%BB%E6%95%A3%E5%AF%B9%E6%95%B0/)（离散对数在一些特殊情况下可以快速计算。然而，通常没有具非常效率的方法来计算它们。）

    gf_exp = [0] * 512 # Create list of 512 elements. In Python 2.6+, consider using bytearray
    gf_log = [0] * 256

    def init_tables(prim=0x11d):
    '''Precompute the logarithm and anti-log tables for faster computation later, using the provided primitive polynomial.'''
        # prim is the primitive (binary) polynomial. Since it's a polynomial in the binary sense,
        # it's only in fact a single galois field value between 0 and 255, and not a list of gf values.
        global gf_exp, gf_log
        gf_exp = [0] * 512 # 就是和gf_log相反的表（gf_log值是下标，gf_log的下标是它的值）
        gf_log = [0] * 256 # (把2的幂和它对应的幂会生成的值通过这个下标和对应的值的关系连在一起)
        # For each possible value in the galois field 2^8, we will pre-compute the logarithm and anti-logarithm (exponential) of this value
        x = 1
        for i in range(0, 255):
            gf_exp[i] = x # compute anti-log for this value and store it in a table
            gf_log[x] = i # compute log at the same time
            x = gf_mult_noLUT(x, 2, prim)

        # If you use only generator==2 or a power of 2, you can use the following which is faster than gf_mult_noLUT():
        #x <<= 1 # multiply by 2 (change 1 by another number y to multiply by a power of 2^y)
        #if x & 0x100: #类似于x> = 256，但速度要快得多
            #(because 0x100 == 256)
            #x ^= prim # substract the primary polynomial to the current value (instead of 255, so that we get a unique set made of coprime numbers), this is the core of the tables generation

        #优化：反日志表的大小加倍，这样我们就不需要修改255来保持在边界内
        #（因为我们主要使用这个表来增加两个GF数，不再增加）。
        for i in range(255, 512):
            gf_exp[i] = gf_exp[i - 255]
        return [gf_log, gf_exp]

这段代码会生成一个表，这个表里面是0-256对应的2的这些次幂的答案，然后如果要计算乘法的话，对方给出了一个大值然后用gf_log[x]找出对应的次幂，再进行加法运算。最后的一个循环是为了防止运算出来的幂相加的值超过255，所以把上限改成了512.（$\,2^{255}\,=\,00000001$然后又开始新一轮的循环2次幂。）

    def gf_mul(x,y):
    if x==0 or y==0:
        return 0
    return gf_exp[gf_log[x] + gf_log[y]]#这样就可以不用再多一步%255去防止gf_exp溢出了的运算了。

## 基于对数的除法

    def gf_div(x,y):
        if y==0:
            raise ZeroDivisionError()
        if x==0:
            return 0
        return gf_exp[(gf_log[x] + 255 - gf_log[y])% 255]

如果x对应的次幂比y对应的要小的话，加上255找到之后对应的幂还是和本身一样，最后求255的模的意思是让幂保持在0-255之间。0-254内的数值与255-510内的值

