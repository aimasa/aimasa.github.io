---
title: python语法记录
copyright: true
date: 2019-08-21 11:17:19
categories:
- python语法
- 各大库的琐碎语法记录
tags:
- python语法
mathjax: true

---

这是在学习python时候碰到的一些语法问题，先记下来，方便日后使用

<!--more-->

## numpy相关记录

```python
power(vector2 - vector1, 2)
```

`power`是`numpy`这个类的一个运算函数，是用来计算次方的，上面那行代码意思是计算$(vector2 - vector1)^{2}$,所以`power(x,y)`是计算$\,x^{y}\,$这个公式的.



```python
sum(power(vector2 - vector1, 2))
```

`sum`也是`numpy`这个类的一个运算函数，但是在上式中vector是一个二维数组（可以这样说，是n行两列的矩阵中取的第一行），`num(x,y)`运算是$x + y$，所以里面就是`power(vector2 - vector1, 2)`计算出来的数组里面的第一行第一列和第二行第二列的数值相加。（等有机会试试`sum`的括号里放多行两列数组）



## pandas相关记录

`read_csv()`：默认返回值是`DataFrame`

而加上了`chunksize=xxx`这个参数之后返回值就变成了：`TextFileReader `

在试运行文本处理的程序时候需要进行数据预处理，作者说是先在本机几条数据看看效果，所以我通过加参数`chunksize`把数据分块成小块这样，注意：并不是单纯的分割出来前多少条数据。

```python
import pandas as pd
test_data = pd.DataFrame(pd.read_csv('G:/Data using/new_data/new_data/test_set.csv',chunksize= 1000).get_chunk(1))
```

这是取分块成功后的第一块（就是前一千条哦）。是从1开始计数的。

`G:/Data using/new_data/new_data/test_set.csv`这是我存放数据的地址。

​        

除掉这种`pd.DataFrame().get_chunk(1)`的办法还有拼接的办法：

```python
import pandas as pd
test_data = pd.concat(pd.read_csv('G:/Data using/new_data/new_data/test_set.csv',chunksize= 1000), ignore_index=True)
```

## requests_html



# collection相关记录

collection.OrderedDict()是按放入元素的先后顺序进行排序。（有序字典）

# 普通语法

cls()：让我感觉像是封装，就是把一些参数经过处理然后封装起来。

`unicodedata.normalize()` 第一个参数指定字符串标准化的方式。 NFC表示字符应该是整体组成(比如可能的话就使用单一编码)，而NFD表示字符应该分解为多个组合字符表示。

- unicodedata.category(chr)  

把一个字符返回它在UNICODE里分类的类型。具体类型如下：

Code    Description

[Cc]    Other, Control

[Cf]    Other, Format

[Cn]    Other, Not Assigned (no characters in the file have this property)

[Co]    Other, Private Use

[Cs]    Other, Surrogate

[LC]    Letter, Cased

[Ll]    Letter, Lowercase

[Lm]    Letter, Modifier

[Lo]    Letter, Other

[Lt]    Letter, Titlecase

[Lu]    Letter, Uppercase

[Mc]    Mark, Spacing Combining

[Me]    Mark, Enclosing

[Mn]    Mark, Nonspacing

[Nd]    Number, Decimal Digit

[Nl]    Number, Letter

[No]    Number, Other

[Pc]    Punctuation, Connector

[Pd]    Punctuation, Dash

[Pe]    Punctuation, Close

[Pf]    Punctuation, Final quote (may behave like Ps or Pe depending on usage)

[Pi]    Punctuation, Initial quote (may behave like Ps or Pe depending on usage)

[Po]    Punctuation, Other

[Ps]    Punctuation, Open

[Sc]    Symbol, Currency

[Sk]    Symbol, Modifier

[Sm]    Symbol, Math

[So]    Symbol, Other

[Zl]    Separator, Line

[Zp]    Separator, Paragraph

[Zs]    Separator, Space



使用一个类，然后用`__init__`去初始化它，再返回值，相当于返回了一个封装好了的对象



```python
state_dict = kwargs.get('state_dict', None)
```

这是*args和**kwargs的使用方法。如果未给出该值则state_dict值为None

[参考](https://stackoverflow.com/questions/36901/what-does-double-star-asterisk-and-star-asterisk-do-for-parameters)

sys.version_info[0]可以得到python的版本

Python getattr() 函数   

pow(n) 是计算n次方的



## 参考博客

[pandas TextFileReader  to DataFrame](https://stackoverflow.com/questions/39386458/how-to-read-data-in-python-dataframe-without-concatenating)

[pandas官方文档](https://pandas.pydata.org/pandas-docs/stable/user_guide/io.html)