---
title: 开始学习spring（二）：试图搭建一个相关项目
copyright: true
date: 2019-04-02 21:08:01
categories:
- spring相关
- 搭建项目（初级）
tags:
- spring相关
---

看了很多东西，但是概念太多了，脑壳痛，所以先搭一个spring Boot的项目，运行一下，再深入学习一下。在别人推荐下我用的是STS（Spring Tool Suite）去搭建的这个项目。

<!--more-->

# 新建项目

<center>{% qnimg 开始学习spring（二）：试图搭建一个相关项目/new.png %}</center>

在新建->project里面的弹出框选择Spring Starter Project去新建一个project。

<center>{% qnimg 开始学习spring（二）：试图搭建一个相关项目/then.png %}</center>

然后我箭头指的地方是我做过些许改动的地方，接着点击Next。

<center>{% qnimg 开始学习spring（二）：试图搭建一个相关项目/next.png %}</center>

再在红色框框框住的地方选择自己想要的插件，这样maven会自动在pom.xml里面自动填写相关代码去下载需要的配件的安装包，最后点击Finish，就完成了新建spring Boot的任务。

# 添加代码让项目成功运行

在src/main/java下面新建一个类，命名为xxxcontroller.java，然后在这个类里面写上如下代码：

    @Controller
    public class exampleControl {
	    @RequestMapping("/")
	    public String Index(Locale locale, Model model) {
	    	Date date = new Date();
	    	DateFormat dateFormat = DateFormat.getDateTimeInstance(DateFormat.LONG, DateFormat.LONG, locale);
	    	String formatDate = dateFormat.format(date);
	    	model.addAttribute("serverTime",formatDate);
	    	return "Index";
    
	    }

然后再在src/main/resources/templates里面新建一个HTML文件：index.html

    <!DOCTYPE html>
    <html>
    <head>
    <meta charset="UTF-8">
    <title>Insert title here</title>
    </head>
    <body>
    <h3>Spring Boot and Spring MVC</h3>
        <P th:text="'The time on the server is ' + ${serverTime}"></P>
    </body>
    </html>

接着得记住去原项目根目录下的pom.xml里面添上这样一行:

        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-thymeleaf</artifactId>
        </dependency>

Thymeleaf是一个XML/XHTML/HTML5模板引擎，用来渲染页面的。Thymeleaf的主要目标在于提供一种可被浏览器正确显示的、格式良好的模板创建方式，因此也可以用作静态建模。
之后就才显示

{% qnimg 开始学习spring（二）：试图搭建一个相关项目/result.png %}

不然会显示：

{% qnimg 开始学习spring（二）：试图搭建一个相关项目/404.png %}

在正在运行时的状态下，修改代码是不会直接反馈到浏览器上显示的，所以得停止运行，再重启。

# 运行后出现page no find

默认情况下spring boot只会扫描启动类当前包和以下的包，在如果配置正确，但是还是没有运行出现自己想要的界面的情况下，可能有原因是在Application.class这个类扫描时候没有扫描到你的controller控制类，所以你可以在你的Application.class这个类里面@SpringBootApplication下面加上@componentscan(controller所在的包。)

@SpringBootApplication
@componentscan(basePackages = "com.didispace.*")

# 关于该项目用到的注解

## @Controller

根据代码中给出的解释就是，带了这个注释的类就是控制器，允许通过类路径扫描自动检测实现类，通常与@ResponseMapping这类注释结合使用。

我刚刚谷歌了一下这方面的知识，用@Controller其实大部分是用来返回字符串，或者是字符串匹配的模板名称，即直接渲染视图，和HTML结合使用的，但是这个前提前后端配合度要高。（单用@Controller并且不做别的处理的话，返回的字符串没有对应的HTML页面的话，就会报错，出现Whitelabel Error Page这个页面）

### 在@Controller这个注解里面的@AliasFor

    @Retention(RetentionPolicy.RUNTIME)
    @Target(ElementType.METHOD)
    @Documented
    public @interface AliasFor {

    	/**
	     * Alias for {@link #attribute}.
    	 * <p>Intended to be used instead of {@link #attribute} when {@link #annotation}
    	 * is not declared &mdash; for example: {@code @AliasFor("value")} instead of
    	 * {@code @AliasFor(attribute = "value")}.
    	 */
    	@AliasFor("attribute")
	    String value() default "";
    
	    /**
	     * The name of the attribute that <em>this</em> attribute is an alias for.
	     * @see #value
	     */
	    @AliasFor("value")
	    String attribute() default "";}

就相当于value的用法和attribute的用法是一样的，值也是一样的，在这里来给注解的属性起别名，使它们互为别名，意义相同

### 返回字符串对应的html

    @Controller
    public class exampleControl {
	    @RequestMapping("/")
	    public String Index(Locale locale, Model model) {
	    	Date date = new Date();
	    	DateFormat dateFormat = DateFormat.getDateTimeInstance(DateFormat.LONG, DateFormat.LONG, locale);
	    	String formatDate = dateFormat.format(date);
	    	model.addAttribute("serverTime",formatDate);
	    	return "index";
	    }
     }

<center>{% qnimg 开始学习spring（二）：试图搭建一个相关项目/result.png %}</center>

### 返回json格式数据

如果想用@Controller这个注释可以返回json的话，就要在返回json的方法前面加上@ResponseBody，这样就会在浏览器页面输出json的格式。

user类：
    
    public class User {
	    private String name;

	    public void setName(String name) {
	    	this.name = name;
	    }

	    public String getName() {
	    	return this.name;
	    }
    }

controller层：

    @Controller
    public class exampleControl {
	    @RequestMapping("/")
	    @ResponseBody
	    public User userTest() {
	    	User user = new User();
	    	user.setName("haha");
	    	return user;
	    }
    }

<center>{% qnimg 开始学习spring（二）：试图搭建一个相关项目/result2.png %}</center>

## @RestController

这个注解和@Controller不一样，是由@Controller和@ResponseBody合在一起的，返回的是一个对象（字符串也可，json格式数据也可等等），其实和@Controller放在类上面，然后在需要返回json格式数据的方法上面加一个@ResponseBody的效果一样，只是一个是整体，一个是局部。（这个可以返回Restful风格---我也不知道什么风格，但是看到还要配置才能用，所以等以后用到了再来补吧。）


# 结果输出

<center>{% qnimg 开始学习spring（二）：试图搭建一个相关项目/result.png %}</center>

# 参考资料

[关于@Controller和@RestController](https://www.jianshu.com/p/b534b394dc7a)
[关于spring boot的学习](http://blog.didispace.com/springbootweb/)