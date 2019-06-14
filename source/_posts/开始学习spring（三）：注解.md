---
title: 开始学习spring（三）：注解
copyright: true
date: 2019-04-04 14:49:42
categories:
- spring相关
- 注解
- 注解大体简介
tags:
- spring相关
---

因为spring Boot项目里面一直要用到注解，所以我特地开了一篇文章来介绍元注解和自定义注解。关于@ResponseMapping这类经常用到的注解可能会零开一篇去写。

现有的spring提供了大量的注解，而且点进去会发现它的源码很短，注解中用到的Annotations元注解其实只是元数据，和业务逻辑一点关系都没有，既然和业务逻辑没有关系，那么就必须有人来实现这些逻辑。Annotations仅仅提供它定义的属性(类/方法/包/域)的信息.

当我们使用Java的标注Annotations(例如@Override)时，JVM就是一个用户，它在字节码层面工作。

bean 是组件的意思。

<!--more-->

# 元注解

一共有以下个元注解

* @Document
* @Target
* @Retention
* @Inherited


## @Target

它是用来约束注解的使用范围的，可以用ElementType类型去给它定义使用范围，一下是它的源代码：

    public enum ElementType {
        /** Class, interface (including annotation type), or enum declaration */
        TYPE,

        /** Field declaration (includes enum constants) */
        FIELD,

        /** Method declaration */
        METHOD,

        /** Formal parameter declaration */
        PARAMETER,

        /** Constructor declaration */
        CONSTRUCTOR,

        /** Local variable declaration */
        LOCAL_VARIABLE,

        /** 用于另外一个注解上 */
        ANNOTATION_TYPE,

        /** Package declaration */
        PACKAGE,

        /**
         * Type parameter declaration
         *
         * @since 1.8
         */
        TYPE_PARAMETER,

        /**
         * Use of a type
         *
         * @since 1.8
         */
        TYPE_USE
    }

如果这个注解中@Target没有被申明，那么意思是它可以被应用在任何元素之上。

# @Retention

这个元注解是用来约束注解的生命周期的。里面有三个状态，一个是源码级别（source）、一个是类文件级别（class）、还一个是运行时级别（runtime）

* source ： 注解将被编译器丢弃（这个类型的注解信息只保留在源码里面，源码经过编译后，注解信息会被丢弃，不会保留在编译好的class里面。）如java内置注解：@Override、@SuppressWarnning等
* class ： 注解在class文件里可用，但是会被vm（虚拟机）抛弃（这个类型的注解信息只会保存在源码里面和class文件里面，在运行时候，不会载入虚拟机里面），**当注解没有定义Retention的时候，会默认是class**，如java内置注解：
* runtime ： 注解信息将在运行期（JVM）中也保留，因此可以通过反射机制读取注解的信息。（源码、class文件和虚拟机中都有注解的信息。）如SpringMvc中的@Controller、@Autowired、@RequestMapping等，如java内置注解：@Deprecated(用于标明已经过时的方法或类)等。

# @Documented

@Documented 被修饰的注解会生成到javadoc中

# @Inherited

可以让注解被继承，但这并不是真的继承，只是通过使用@Inherited，可以让子类Class对象使用getAnnotations()获取父类被@Inherited修饰的注解。

# 关于自定义注解里面的一些语句

    String name() default "";//指定义了name这个属性，默认是空字符串。

声明注解类型时候，不能用包装类型，只能用基本类型去声明

    @Target({ElementType.TYPE})
    @Retention(RetentionPolicy.RUNTIME)
    @Documented
    @Component
    public @interface Controller {
	    @AliasFor(annotation = Component.class)
	    String value() default "";
    }

因为上面除掉元注解外还有@component，说明@controller这个注解属于@component这个注解

我们定义了自己的注解并将其应用在业务逻辑的方法上。所以就需要我们写一个用户程序调用我们的注解。这里需要使用**反射机制**。


# 参考资料

[我觉得写得很详细的](https://blog.csdn.net/javazejian/article/details/71860633)

[注解是什么](http://www.importnew.com/10294.html)

