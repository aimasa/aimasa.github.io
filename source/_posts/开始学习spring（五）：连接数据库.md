---
title: 开始学习spring（五）：连接数据库
copyright: true
date: 2019-04-17 11:01:44
categories:
- spring相关
- 连接数据库
tags:
- spring相关
---

因为这边都用的是jooq框架来对数据库进行操作，然后我就开始学jooq框架惹==。我暑假还是想好好的活着，不想再像上个暑假一样痛苦。

不过刚开始接触这个框架，坑也没少踩，我会在下面分别列出来。

用的是maven

<!--more-->

# 第一步：装依赖

在jooq的官网看，首先第一步，在pom中要加上这三个依赖

		<dependency>
			<groupId>org.jooq</groupId>
			<artifactId>jooq</artifactId>
		</dependency>
		<dependency>
			<groupId>org.jooq</groupId>
			<artifactId>jooq-meta</artifactId>
		</dependency>
		<dependency>
			<groupId>org.jooq</groupId>
			<artifactId>jooq-codegen</artifactId>
		</dependency>

- jooq ：核心包，CRUD（增删查改）核心类所在大本营。
- jooq-meta ：数据管理和操作的核心代码。
- jooq-codegen ：负责数据库代码生成，主要负责JOOQ的代码生成功能。

# 第二步：加插件

			<plugin>
				<groupId>org.jooq</groupId>
				<artifactId>jooq-codegen-maven</artifactId>
				<executions>
					<execution>
						<goals>
							<goal>generate</goal>
						</goals>
					</execution>
				</executions>
				<dependencies>
					<dependency>
						<groupId>mysql</groupId>
						<artifactId>mysql-connector-java</artifactId>
						<version>5.1.39</version>
					</dependency>
				</dependencies>
				<configuration>
					<jdbc>
						<driver>com.mysql.jdbc.Driver</driver>
						<url>jdbc:mysql://localhost:3306/exercise_test?useSSL=true</url>
						<user>root</user>
						<password></password>
					</jdbc>
					<generator>
						<database>
							<name>org.jooq.util.mysql.MySQLDatabase</name>
							<includes>.*</includes>
							<excludes></excludes>
							<inputSchema>exercise_test</inputSchema>
							<forcedTypes>
								<forcedType>
									<userType>java.util.Date</userType>
									<converter>com.example.util.DateConverter</converter>
									<types>datetime</types>
								</forcedType>
							</forcedTypes>
						</database>
						<target>
							<!-- The destination package of your generated classes (within the 
								destination directory) -->
							<packageName>com.example.pojo</packageName>
							<!-- The destination directory of your generated classes -->
							<directory>/src/main/java</directory>
						</target>
						<generate>
							<pojos>true</pojos>
							<daos>true</daos>
							<deprecated>false</deprecated>
						</generate>
					</generator>

				</configuration>
			</plugin>

这一部分是用来生成数据库映射代码的插件。

里面是对数据库的信息进行配置：

				<configuration>
					<jdbc>
						<driver>com.mysql.jdbc.Driver</driver>
						<url>jdbc:mysql://localhost:3306/exercise_test?useSSL=true</url>
						<user>root</user>
						<password></password>
					</jdbc>
					<generator>
						<database>
							<name>org.jooq.util.mysql.MySQLDatabase</name>
							<includes>.*</includes><!--点后面的所有文件-->
							<excludes></excludes>
                            <!--映射的数据库的架构名（就是你的表是放在哪个架构下的）-->
							<inputSchema>exercise_test</inputSchema>
							<forcedTypes>
								<forcedType>
									<userType>java.util.Date</userType>
									<converter>com.example.util.DateConverter</converter>
									<types>datetime</types>
								</forcedType>
							</forcedTypes>
						</database>
						<target>
							<!-- 你数据库映射出来的表打算放在代码里面的哪个包下 -->
							<packageName>com.example.pojo</packageName>
							<!-- 这个包是应该在哪个class下面 -->
							<directory>/src/main/java</directory>
						</target>
						<generate>
							<pojos>true</pojos>
							<daos>true</daos>
							<deprecated>false</deprecated>
						</generate>
					</generator>

				</configuration>

这里面配置这段就是把数据库中的表映射出来的。

                             <forcedTypes>
								<forcedType>
									<userType>java.util.Date</userType>
									<converter>com.example.util.DateConverter</converter>
									<types>datetime</types>
								</forcedType>
							</forcedTypes>

这段代码是用来映射时间戳的，其中

    <converter>com.example.util.DateConverter</converter>

是放有映射方法的包，没有放映射方法的话，那这段无效==。可以按ctrl进去，查看这个包，如果无法查看的话，那么说明这个包不是需要的。

# 数据库的架构

放张图就很详细了：

<center>{% qnimg 开始学习spring（五）：连接数据库/schema.jpg %}</center>

# 出错地方

这样配置完成后，我的这个项目名旁边跟了一个红**×**，所以就右键项目，选中**maven**，出现子目录，选中**update project**，点击，如果配置没有错误的话，红**×**会自然消失。

然后就是maven install的问题了，显示BUILD FAILED失败，里面有一句话，翻译过来是问我有没有配置错jdk，所以我就去perferences里面的 java->install JREs看了看里面的包是不是配置的jdk包，如果不是，点击add->Standard VM->next->选择jdk的包

再然后maven install就没有再出问题了，同时，也将我数据库里的表转换成了pojo的包里的java类了。

# 参考资料

[参考博客](http://www.mysunshinedreams.com/orm%E7%9A%84%E5%B0%8F%E6%B8%85%E6%96%B0-jooq/)