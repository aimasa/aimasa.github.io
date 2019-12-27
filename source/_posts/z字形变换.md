---
title: z字形变换
copyright: true
date: 2019-07-18 10:06:03
categories:
- 算法
- z字形变换
tags:
- 算法
mathjax: true
---

将一个给定字符串根据给定的行数，以从上往下、从左到右进行 Z 字形排列。

比如输入字符串为 "LEETCODEISHIRING" 行数为 3 时，排列如下：

    L   C   I   R
    E T O E S I I G
    E   D   H   N

之后，你的输出需要从左往右逐行读取，产生出一个新的字符串，比如："LCIRETOESIIGEDHN"。

[力扣（LeetCode）](https://leetcode-cn.com/problems/zigzag-conversion)

<!--more-->

我用暴力破解，z形输出了输入的字符串，结果发现看错了题目……只是要我z形排序。

然后官方题解特别简洁

它先是创建了一个列表，这个列表的长度代表了z字形需要被分成的行数（n = 3时候list.length = 3）

列表里面装的元素类型是stringBuffer，把每行会被输出的内容拼接在一起，如图：

<center>{%qnimg z字形变换/example.png%}</center>
`flag`就是一个布尔（Boolean）值，用来判断z字形输出的走向，应该往上走还是往下走。它控制的是list存储里面应该往上存储还是向下存储，换句话来说就是`list<StringBuffer>.get(i)`中的`i`是增加还是还是减少。

最后再把里面的`list`里面的`stringBuffer`循环输出进行拼接，最后返回正确的值。

所以超简单，但是我就是没想到。

# 代码

[代码](https://github.com/aimasa/exercise_demo/tree/tablebookexercise/src/exercise/demo/convert)

