---
title: 了解二维码(四)：Reed-Solomon code
copyright: true
date: 2019-03-05 16:31:59
categories:
- QR
- 了解二维码
- 了解二维码(四)：Reed-Solomon code
tags:
- QR
---

在前面我们学习了有限域和多项式，可是为什么要学习它们呢，是因为这是像Reed-Solomon这样的纠错码的主要见解：我们不是仅仅将消息视为一系列（ASCII）数字，而是将其视为遵循非常明确的有限域算法规则的多项式.

也就是通过多项式和有限域算法表示数据，我们给数据添加了一个结构，消息的值仍然不变，而且这个结构还能让我们通过它利用定义良好的数学规则对损坏的消息进行修复操作。

<!--more-->

与BCH码类似，Reed-Solomon码通过将表示消息的多项式除以不可约的生成多项式来编码，然后余数是RS码，我们将其附加到原始消息。

我们之前曾说过，BCH码和大多数其他纠错码背后的原理是使用一个缩小的词典，其中包含非常不同的词，以便最大化词之间的距离，而更长的词有更大的距离：这里的原理是相同的，首先是因为我们用增加距离的附加符号（余数）来延长原始信息，其次因为余数几乎是唯一的（由于精心设计的不可约生成多项式），因此可以通过巧妙的算法利用它来推导部分原始消息。

总结一下，就像加密一样：我们的生成多项式是我们的编码字典，多项式除法是使用字典（生成多项式）将我们的消息转换为RS代码的运算符。（我们的消息是明文，按多项式除法使用编码字典这个算法而转化为RS代码的运算符）

> 加密：对原来为明文的文件或者数据按照某种算法进行处理，使之变成一段不可读的代码，这段代码一般被叫做密文。只有在输入对应的密钥之后才能显示出本来内容。

# RS生成多项式

RS码使用类似于BCH码的方法去生成多项式，生成多项式是$\,\left (x-a^{n} \right)\,$的乘积，在QR码中从$\,n=0\,$开始，例如：

> $\,g_{4}\, =\, \left (x-\alpha ^{0}  \right )\left (x-\alpha ^{1}  \right )\left (x-\alpha ^{2}  \right )\left (x-\alpha ^{3}  \right )=  01 x^{4} + 0f x^{3} + 36 x^{2} + 78 x + 40\,$

这是一个计算了指定n个纠错符号的RS码需要的生成多项式。

    def rs_generator_poly(nsym):
        g = [1]
        for i in range(0, nsym):
            g = gf_poly_mul(g, [1, gf_pow(2, i)])
    return g

这个是根据nsym是判断多项式需要nsym个$\,\left (x-a^{n} \right)\,$的乘积


# 多项式除法

除法中

 <center>![](chufa.png)</center>

其中除数与商之间的乘法就是用的在有限域中的乘法，如果乘积太大，就mod一个不可约多项式（通常是100011101）然后把得出的乘积控制在256的范围内，再继续往下计算。以下是其中一部分的乘法得出的乘积再放进除法公式中继续运算。

 <center>![](chenfa.png)</center>

于是得出编码信息为12 34 56 37 e6 78 d9。

    def gf_poly_div(dividend, divisor):
        '''Fast polynomial division by using Extended Synthetic Division and optimized for GF(2^p) computations
        (doesn't work with standard polynomials outside of this galois field, see the Wikipedia article for generic algorithm).'''
        # CAUTION: this function expects polynomials to follow the opposite convention at decoding:
        # the terms must go from the biggest to lowest degree (while most other functions here expect
        # a list from lowest to biggest degree). eg: 1 + 2x + 5x^2 = [5, 2, 1], NOT [1, 2, 5]

        msg_out = list(dividend) # Copy the dividend
        #normalizer = divisor[0] # precomputing for performance
        for i in range(0, len(dividend) - (len(divisor)-1)):# 因为余数得比除数小。所以就让余数的长度比除数小1.
            #msg_out[i] /= normalizer # for general polynomial division (when polynomials are non-monic), the usual way of using
            # synthetic division is to divide the divisor g(x) with its leading coefficient, but not needed here.
            coef = msg_out[i] # precaching
            if coef != 0: # log(0) is undefined, so we need to avoid that case explicitly (and it's also a good optimization).
                for j in range(1, len(divisor)): # in synthetic division, we always skip the first coefficient of the divisior,
                                              # because it's only used to normalize the dividend coefficient
                    if divisor[j] != 0: # log(0) is undefined
                        msg_out[i + j] ^= gf_mul(divisor[j], coef) # 这里因为伽罗瓦域中多项式除法的特殊性，所以直接跳过divisor[0]，因为第一个数是为了量定除数需要乘多少去与被除数求余。然后后面的商就依次根据divisor[j]去确定。
                        # (but xoring directly is faster): msg_out[i + j] += -divisor[j] * coef

    # The resulting msg_out contains both the quotient and the remainder, the remainder being the size of the divisor
    # (the remainder has necessarily the same degree as the divisor -- not length but degree == length-1 -- since it's
    # what we couldn't divide from the dividend), so we compute the index where this separation is, and return the quotient and remainder.
        separator = -(len(divisor)-1)
        return msg_out[:separator], msg_out[separator:] # 返回商和余数，再在后面的公式把商加在msg_out数组前头

然后出来了一个高效的编码方法：

    def rs_encode_msg(msg_in, nsym):
        '''Reed-Solomon 主要的编码功能, 用的是多项式长除法 (algorithm Extended Synthetic Division)'''
        if (len(msg_in) + nsym) > 255: raise ValueError("Message is too long (%i when max is 255)" % (len(msg_in)+nsym))
        gen = rs_generator_poly(nsym)
        # Init msg_out with the values inside msg_in and pad with len(gen)-1 bytes (which is the number of ecc symbols).
        msg_out = [0] * (len(msg_in) + len(gen)-1)
        # Initializing the Synthetic Division with the dividend (= input message     polynomial)
        msg_out[:len(msg_in)] = msg_in

        # Synthetic division main loop
        for i in range(len(msg_in)):
            # Note that it's msg_out here, not msg_in. Thus, we reuse the updated     value at each iteration
            # (this is how Synthetic Division works: instead of storing in a temporary register the intermediate values,
            # we directly commit them to the output).
            coef = msg_out[i]

            # log(0) is undefined, so we need to manually check for this case. There's no need to check
            # the divisor here because we know it can't be 0 since we generated it.
            if coef != 0:
                # in synthetic division, we always skip the first coefficient of the divisior, because it's only used to normalize the dividend coefficient (which is here useless since the divisor, the generator polynomial, is always monic)
                for j in range(1, len(gen)):
                    msg_out[i+j] ^= gf_mul(gen[j], coef) # equivalent to msg_out[i+j] += gf_mul(gen[j], coef)

        # At this point, the Extended Synthetic Divison is done, msg_out contains the quotient in msg_out[:len(msg_in)]
        # and the remainder in msg_out[len(msg_in):]. Here for RS encoding, we don't need the quotient but only the remainder
        # (which represents the RS code), so we can just overwrite the quotient with the input message, so that we get
        # our complete codeword composed of the message + code.
        msg_out[:len(msg_in)] = msg_in

        return msg_out

这段新的代码把编码和长除法功能加在了一起，语句更简短。

这种算法速度更快，但在实际应用中仍然很慢，特别是在Python中。 有一些方法可以通过使用各种技巧来优化速度，例如内联（而不是gf_mul，替换为操作以避免调用），通过预计算（gen和coef的对数，甚至通过生成乘法表 -  通过使用内存视图（比如通过更改所有列表），使用静态类型构造（将gf_log和gf_exp分配给array.array（'i'，[...]）），但似乎后者在Python中效果不佳 通过使用PyPy运行它，或者将算法转换为Cython或C扩展名