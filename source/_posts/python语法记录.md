---
title: python语法记录
copyright: true
date: 2019-08-21 11:17:19
categories:
- python语法
- numpy语法记录
tags:
- python语法

---

这是在学习kmeans时候碰到的一些语法问题，先记下来，方便日后使用

<!--more-->

```python
power(vector2 - vector1, 2)
```

`power`是`numpy`这个类的一个运算函数，是用来计算次方的，上面那行代码意思是计算$(vector2 - vector1)^{2}$,所以`power(x,y)`是计算$\,x^{y}\,$这个公式的.



```python
sum(power(vector2 - vector1, 2))
```

`sum`也是`numpy`这个类的一个运算函数，但是在上式中vector是一个二维数组（可以这样说，是n行两列的矩阵中取的第一行），`num(x,y)`运算是$x + y$，所以里面就是`power(vector2 - vector1, 2)`计算出来的数组里面的第一行第一列和第二行第二列的数值相加。（等有机会试试`sum`的括号里放多行两列数组）



