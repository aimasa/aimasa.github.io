---
title: java的~、|、<<、>>、>>>、^
copyright: true
date: 2019-03-14 10:08:58
categories:
- java语法
- java中的"~"
tags:
- java语法
---


在看BitSet的源码的时候，我看到了这样一段代码

    words[wordIndex] &= ~(1L << bitIndex);

其中~(1L<<bitIndex)我不太明白这是什么意思，所以谷歌了一下,就顺便查了一下日常容易被我弄错的逻辑运算符的用法。

<!--more-->

其中

~：是按位取反运算符

如：~(10010010)=01101101

所以这句的意思是在1L左移bitIndex位后，对words[wordIndex]这个第一个bit的位清零

# 容易混淆的逻辑运算符

顺便解释一下|这个的意思

    |：这个是按位或运算

    >> 是带符号右移，若左操作数是正数，则高位补“0”，若左操作数是负数，则高位补“1”.

    << 左移，不管正负数左移时候，最高位都不用管，只需要在后面补零就可以了，和<<<不带符号左移一样，所以就没有不带符号左移

    >>> 是无符号右移，无论左操作数是正数还是负数，在高位都补“0”

计算机都是用补码存储数据的。所以当一个数带符号右移或者左移，就是单纯的对该数进行除乘。

所以在带符号右移或者左移时候，为了保证数字在这个安全的距离能够得出想要的正确结果（乘除2的标准结果），所以int设置的可活动的位移是32，就是左右移32位时候，就会恢复数字的原本值，long设置的可活动位移是64.

emmmm感觉我语言讲述的不是很清楚，所以附上例子把。

    public class test {
        public static void main(String[] args) {
	        long a=-5;
	        System.out.println((a << 64));  
	        // output: -5  
        }
    }

这是long的情况，接下来我放int的例子：

    public class test {
        public static void main(String[] args) {
	        int a=-5;
	        System.out.println((a << 32));  
	        // output: -5  
        }
    }

恩，就是这样，我是这样理解的。

# 关于^

^这个是异或运算符。

# 参考资料

[位移参考](https://www.jianshu.com/p/0236b51b903f)