---
title: python_range的用法
copyright: true
date: 2019-01-30 16:10:39
categories:
- Python
- range()&array[::]
tags:
- python的用法
---

简单的讲了一下range的用法和array[::]的一些用法
<!--more-->

[参考博文](www.cnblogs.com/buro79xxd/archive/2011/05/23/2054493.html)
array = [1, 2, 5, 3, 6, 8, 4]

**其实这里的顺序标识是**

[1, 2, 5, 3, 6, 8, 4]

(0，1，2，3，4，5，6)

(-7,-6,-5,-4,-3,-2,-1)

那么两个[::]会是什么那？
> array[::2]
[1, 5, 6, 4]

> array[2::]
[5, 3, 6, 8, 4]

> array[::3]
[1, 3, 4]

> array[::4]
[1, 6] 

如果想让他们颠倒形成reverse函数的效果
> array[::-1]
[4, 8, 6, 3, 5, 2, 1]

> array[::-2]
[4, 6, 5, 1]

[参考博文](https://blog.csdn.net/zhenyu5211314/article/details/19069567):
函数原型：range（start， end， scan):

参数含义：

* start:计数从start开始。默认是从0开始。例如range（5）等价于range（0， 5）;
* end:技术到end结束，但不包括end.例如：range（0， 5） 是[0, 1, 2, 3, 4]没有5
* scan：每次跳跃的间距，默认为1。例如：range（0， 5） 等价于 range(0, 5, 1)


**所以，两个冒号时候，array[::]其中第一个数是数列的起始位置，第二个数是数列的最终位置，第三个是从第一位置往下数间隔的长度（如果为负数，则是倒过来往前数字符）**

**在range()中，有三个值，第三个值如果是负数则是减少，正数是增加**