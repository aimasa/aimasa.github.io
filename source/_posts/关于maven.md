---
title: 关于maven
copyright: true
date: 2019-04-18 15:07:41
categories:
- maven
tags:
- maven
---

之前一直只是单纯的知道maven是用来加载库的，然后去代码里面调用它。里面<dependencies></dependencies>这里面是存放需要被调用的库的依赖。但是maven里面包括的内容除了<dependencies></dependencies>还有别的，所以就去查了一些资料，然后总结在这。

<!--more-->

Maven 主要帮助用户完成以下 3 个方面的工作：

* 生命周期管理，便捷的构建过程；
* 依赖管理，方便引入所需依赖 Jar 包；
* 仓库管理，提供统一管理所有 Jar 包的工具；

---------([摘自博客](https://www.jianshu.com/p/b4ef9978d85d))

在建了一个maven项目之后，会发现在项目里面有一个pom.xml文档

# 什么是pom

项目对象模型或POM是Maven的基本工作单元。 它是一个XML文件，其中包含有关Maven用于构建项目的项目和配置详细信息的信息。 它包含大多数项目的默认值。 这方面的例子是：target是构建目录;构建源目录是src / main / java; src / test / java是测试源目录。执行任务时，Maven在当前目录中查找POM。 它读取POM，获取所需的配置信息，然后执行目标。

maven install时候，会自动把pom里面添加的依赖下载下来。

# maven的生命周期

maven内部有三个构建生命周期：分别是：clean, default, site

我在那个博客里面找到了这张展示default构建生命周期核心阶段的图：

<center>{% qnimg 关于maven/life.png %}</center>

当执行mvn install时候，maven就会自动执行生命周期中的validate, compile, test, package, verify, install这些阶段，并将 package 生成的包发布到本地仓库中。

Maven 将构建过程定义为 default lifecycle

## 阶段和插件的关系

就像上面说的那样，maven把构建过程定义成default lifecycle，并且将它里面的一个个阶段划分为phase，但是这些phase只是规定执行顺序，对于每个阶段做什么工作，就要看pom.xml里面的插件（plugins）了。

pom.xml里面有这么个元素叫<plugins>，它是用来把phase和做的目标<goal>绑定在一起，当要执行某个<phase>时候，就会调用插件来完成绑定的目标，一个阶段里面可以绑定好多个目标，也可以不绑定目标。

但是不配置<plugins>也可以，因为maven有默认的<plugins>。

在输出日志里面，会有一系列的 插件(plugin):版本号:<goal>(phase) 输出，可以根据日志里面的这种输出去观察哪些阶段会调用插件去完成那些<goal>了。

## 依赖

在<dependencies></dependencies>里面的依赖一般都是这种形式出现：

    	<dependency>
		<groupId>mysql</groupId>
		<artifactId>mysql-connector-java</artifactId>
		<scope>runtime</scope>
	    </dependency>

## 关于maven整体

| groupId|一般用该项目的组织或团体的域名来标识，例如:org.apache.maven.plugins  |

| artifactId | 代表唯一的工程名 |

|version| 版本号|

|packaging| 标识打包的类型，例如有:jar, war, tar |

|dependencies| 该工程内依赖的其他 jar 包|

## 远程仓库

如果 Maven 在中央仓库中也找不到依赖的库文件，它会停止构建过程并输出错误信息到控制台。为避免这种情况，Maven 提供了远程仓库的概念，它是开发人员自己定制仓库，包含了所需要的代码库或者其他工程中用到的 jar 文件。

## <respositories>

我的理解就是可以通过这个repositories节点，去访问给出的url地址上的这个仓库，去下载自己需要的库，因为这个库在可能是自己的远程仓库里面，就没法从中央仓库里面下载到这个库，所以就使用这个节点，里面配置各种你可能需要用到的远程库。


	<repositories>
		<repository>
			<id>spring-snapshots</id>
			<name>Spring Snapshots</name>
			<url>https://repo.spring.io/snapshot</url>
			<snapshots>
				<enabled>true</enabled>
			</snapshots>
		</repository>

## Maven 依赖搜索顺序

当我们执行 Maven 构建命令时，Maven 开始按照以下顺序查找依赖的库：

- 步骤 1 － 在本地仓库中搜索，如果找不到，执行步骤 2，如果找到了则执行其他操作。
- 步骤 2 － 在中央仓库中搜索，如果找不到，并且有一个或多个远程仓库已经设置，则执行步骤 4，如果找到了则下载到本地仓库中已被将来引用。
- 步骤 3 － 如果远程仓库没有被设置，Maven 将简单的停滞处理并抛出错误（无法找到依赖的文件）。
- 步骤 4 － 在一个或多个远程仓库中搜索依赖的文件，如果找到则下载到本地仓库已被将来引用，否则 Maven 将停止处理并抛出错误（无法找到依赖的文件）。

----------------------[摘自极客学院maven教程](https://wiki.jikexueyuan.com/project/maven/repositories.html)

# 关于${xxx.version}

一般，会经常看到官网上放出的写maven依赖中有个语句是

    <version>${xxx.version}<version>

但是maven总是报错，是因为你这里没有指明这个添加的依赖的版本，maven是不会自动给你找到相关依赖的最新版本的包并且去下载的，所以你需要在<properties></properties>里面加上  
  
    <xxx.version>1.3.0.Final</xxx.version>

这样的话，就是**${xxx.version}**的值就是对应给出的**1.3.0.Final**了。

# 参考资料

[maven入门](https://www.jianshu.com/p/b4ef9978d85d)

[maven生命周期](https://www.jianshu.com/p/fd43b3d0fdb0)

[极客学院maven教程](https://wiki.jikexueyuan.com/project/maven/repositories.html)