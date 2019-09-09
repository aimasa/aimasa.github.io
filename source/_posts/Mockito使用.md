---
title: Mockito使用
copyright: true
date: 2019-06-24 19:59:21
categories:
- spring相关
- Mockito使用
tags:
- spring相关
---

关于Mockito的使用，跟着文档边看边试着那些用法。

- 来一段从官网搞出来的话

> Mockito is a mocking framework that tastes really good. It lets you write beautiful tests with a clean & simple API. Mockito doesn’t give you hangover because the tests are very readable and they produce clean verification errors. 

- 翻译

> Mockito是一个体感非常好的mocking框架，它能够让你用简洁干净的API写出漂亮的测试方法……

好了后面的不翻译了，我看了一下，简而言之就是这个测试框架非常好用，特别好用，快来用吧

<!--more-->

# Mockito使用

## 创建mock对象

刚开始我是`mock()`一个对象：

    UserPersonService mockedUserPerson = Mockito.mock(UserPersonService.class);

ps:其中`UserPersonService`是我想调通的service类。

然后发现可以用`@Mock`然后`private`这个类，然后就很方便的创建了一个Mock对象了：

	@Mock
	private UserPersonService userPersonService;

## 验证行为

这里就是验证方法是否真的被调用，或者调用了多少次。

    List mockedList = mock(List.class);

    //using mock object 使用mock对象
    mockedList.add("one");
    mockedList.clear();

    //verification 验证
    verify(mockedList).add("one");
    verify(mockedList).clear();

里面是验证

## 做测试桩

打桩就是对调用的方法给它模拟返回值。然后再后面对这个方法进行调用，输入与之前设定的一样的输入值时候，如果那个方法响应了就会把自己之前模拟的返回值返回出来。这时候我们就能用输出到控制台语句去观摩一下是不是自己预期设定的结果。

    LinkedList mockedList = mock(LinkedList.class);

     //stubbing
     // 测试桩
     when(mockedList.get(0)).thenReturn("first");
     when(mockedList.get(1)).thenThrow(new RuntimeException());

这是官网给出来的使用方法，里面当`mockedList.get(0)`时候返回的是自己设定好的`first`这个值，然后`mockedList.get(1)`时候返回的就是自己设定好的抛异常`new RuntimeException()`

当然，如果自己没有对某个输入参数进行打桩的话，输出就会默认`null`。

这个方法也就是看能不能调通这个方法，看是否会响应，会的话就会输出期望值，还能查看这个方法被调用了多少次。

# 使用心得

我试着用`@Mock`去注解一个类，然后打桩，但是发现打完桩，还是不能返回我打桩时候设定的返回值。

然后问大佬们，他们给我说用`@MockBean`，之后就成功了，所以在看`@Mock`和`@MockBean`两个注解的区别

## @Mock

它们允许模拟类或接口，并记录和验证其上的行为。

将字段标记为模拟。

允许速记模拟创建。
最小化重复的模拟创建代码。
使测试类更具可读性。
使验证错误更容易阅读，因为字段名称用于标识模拟。

## @MockBean

就是对不需要验证的类加上这个注解，然后它会自动将这个类代入测试的`controller`中的被`@Autowired`注解的类。然后返回自己打桩时候设置的值

Not only will @MockBean provide you with a mock, it will also add that mock as a bean (as the name suggests) within the ApplicationContext, and override any existing beans either by name or by type

----------------[摘自博文](https://gooroo.io/GoorooTHINK/Article/16943/Spring-Boot-14--MockBean-and-SpyBean/24301#.XRQk2egzaM8)

