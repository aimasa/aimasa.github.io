---
title: 开始学习spring（三）：注解2
copyright: true
date: 2019-04-08 21:14:34
categories:
- spring相关
- 注解
- spring注解
tags:
- spring相关
---

虽然明白注解是怎么一回事了，但是还是好奇它的反射机制是怎么实现的，所以谷歌了一下，之后可能会试着自己看看源码。

注：在 Spring 中，构成应用程序主干并由Spring IoC容器管理的对象称为bean。bean是一个由Spring IoC容器实例化、组装和管理的对象。

[关于IoC(控制反转)](https://www.awaimai.com/2596.html)

<!--more-->

# RequestMapping

这个是用来处理请求地址映射的注解，它的注解有六个属性：

## value
**value**：指定请求的实际地址

**method**： 指定请求的method类型， GET、POST、PUT、DELETE等；

<center>{% qnimg 开始学习spring（三）：注解2/method.png %}</center>

**consumes**：指定处理请求的提交内容类型（Content-Type），例如application/json, text/html;

**produces**: 指定返回的内容类型，仅当request请求头中的(Accept)类型中包含该指定类型才返回；

**params**： 指定request中必须包含某些参数值是，才让该方法处理。

**headers**： 指定request中必须包含某些指定的header值，才能让该方法处理请求。

## Service

对服务类进行注解标记，让@componentScan将这些类装载注入到Spring的bean容器中，或者如果没有@ComponentScan这个注解的话，spring会自动去扫描Application这个类所在的包列表下面的类，如果含有@Service标注，那么被看做是服务类被装入spring的bean容器里面。

## Repository

@Repository标记该类为数据层，Dao层。

## Controller

@Controller层用于标记该类为控制层。

## Autowired

---------------[摘自博客](https://blog.csdn.net/walkerJong/article/details/7994326)等我用了一遍再回来补充。

## @ResponseStatus

@ResponseStatus这个注解是做异常处理的，然后可以自定义异常。当我们的Controller抛出异常，并且没有被处理的时候，他将返回HTTP STATUS 为指定值的 HTTP RESPONSE.
