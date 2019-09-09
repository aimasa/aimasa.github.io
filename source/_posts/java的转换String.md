---
title: toString()和new String()
copyright: true
date: 2019-08-19 10:38:25
categories:
- java语法
- toString()和new String()
tags:
- java语法
---

用byte[]转换成String类型的时候，我用了(byte[]).toString()去转换，然后返回的结果居然是xxx@xxxx这个字符串，然后我很懵，点进源码才明白原因。

<!--more-->

```java
public String toString() {
    return getClass().getName() + "@" + Integer.toHexString(hashCode());
}
```
这是把类的类名加上“@”加上做的hashcode值转换成字符串返回输出，byte[]是个数组，是一个对象，所有的对象都是object的子类，所以数组可以使用object的方法，在object的方法里有toString()这个方法，但在没有被复写的情况下使用它，就会生成`getClass().getName() + "@" + Integer.toHexString(hashCode())`这个字符串。

所以我们用new String()的方式去转换byte[]值，将其变成String字符串输出。

```java
public String(byte bytes[]) {
    this(bytes, 0, bytes.length);
}
```
```java
public String(byte bytes[], int offset, int length) {
    checkBounds(bytes, offset, length);
    this.value = StringCoding.decode(bytes, offset, length);
}
```
这是这两种方法的输出结果


<center>{%qnimg java的转换String/example.png%}</center>

# 参考博客

[数组相关概念](https://blog.csdn.net/mrbacker/article/details/81638331)

[关于object的子类](https://zhidao.baidu.com/question/267505870.html)