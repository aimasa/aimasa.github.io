---
title: 开始学习spring（一）：关于一些基础知识点
copyright: true
date: 2019-04-02 14:44:53
categories:
- spring相关
- 基础指示点
tags:
- spring相关
---

现在正式开始学spring了，在此打卡（4.2）

<!--more-->

# 什么是POJOs

POJO, or Plain Old Java Object, is a normal Java object class (that is, not a JavaBean, EntityBean etc.) 

POJO（plain Old Java Object）它是一个正规的、简单的java对象，包含了业务逻辑处理和持久化逻辑等，但不是JavaBean、EntityBean等，**不具有任何特殊角色和不继承不实现任何其他java框架的类或接口。**

POJO里面是可以包含业务逻辑处理和持久化逻辑，也可以包含类似与JavaBean属性和对属性访问的set和get方法的。

代码示例：

    package com.demo.spring;
 
    public class DbHello { //简单的Java类，称之为POJO，不继承，不实现接口
        private DictionaryDAO dao;
        public void setDao(DictionaryDAO dao) {
            this.dao = dao;
        }
    }

# 什么是javabean

一种特殊又简单的类，

* 这个类必须具有一个公共的(public)无参构造函数；
* 所有属性私有化（private）；
* 私有化的属性必须通过public类型的方法（getter和setter）暴露给其他程序，并且方法的命名也必须遵循一定的命名规范。 
* 这个类应是可序列化的。（比如可以实现Serializable 接口，用于实现bean的持久性）
           
# EJB是什么

Enterprise JavaBean又叫企业级JavaBean(听说很老了，以后如果用到的话再回来补。)

# 依赖注入（DI）

就是减少依赖。里面牵涉到了依赖倒置（IoC）（这个和设计模式相关，我会找个时间把设计模式的笔记补上。）



# 参考资料

[关于POJOs](https://blog.csdn.net/tonny_guan/article/details/2250134)
[关于javabean](https://blog.csdn.net/chenchunlin526/article/details/69939337)