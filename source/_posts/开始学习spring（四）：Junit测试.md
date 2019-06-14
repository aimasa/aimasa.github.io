---
title: 开始学习spring（四）：Junit测试
copyright: true
date: 2019-04-10 11:11:31
categories:
- spring相关
- Junit测试
tags:
- spring相关
---

跟着[博客](http://blog.didispace.com/spring-boot-learning-1/)开始学怎么用Junit对写出的部分代码进行单元测试。

<!--more-->

# 实例

跟着它给的代码：


    @RunWith(SpringJUnit4ClassRunner.class)
    @SpringBootTest(classes=MockServletContext.class)
    @WebAppConfiguration
    public class DemoApplicationTests {
    	private MockMvc mvc;

    	@Before
    	public void Setup() throws Exception {
    		mvc = MockMvcBuilders.standaloneSetup(new exampleController()).build();
	    }
	    @Test
	    public void contextLoads() throws Exception{
	    	mvc.perform(MockMvcRequestBuilders.get("/").accept(MediaType.APPLICATION_JSON))
	    	        .andExpect(status().isOk())
		            .andExpect(content().string(equalTo("index"))).andDo(print());
		}

	}

但是很奇怪的是一直在
    
     .andExpect(content().string(equalTo("index"))).andDo(print());

这个部分报错，说**The method content()/equalTo() is ambiguous for the type DemoApplicationTests**

结果我发现在我直接敲入这段代码时候，eclipse会直接帮忙自动导入对应的包：

    import static org.hamcrest.CoreMatchers.equalTo;
    import static org.hamcrest.Matchers.equalTo;
    import static org.springframework.test.web.client.match.MockRestRequestMatchers.content;
    import static org.springframework.test.web.servlet.result.MockMvcResultMatchers.content;

里面会有不同包里面的同名称的类文件，在这里我们选用：

    import static org.hamcrest.Matchers.equalTo;
    import static org.springframework.test.web.servlet.result.MockMvcResultMatchers.content;

# 讲解

## 初始化

### MockMvcBuilders

这个是用来构造MockMvc的构造器，主要是有两种实现，分别是StandaloneMockMvcBuilder和DefaultMockMvcBuilder一种是独立安装和集成Web环境测试（此种方式并不会集成真正的web环境，而是通过相应的Mock API进行模拟测试，无须启动服务器）。

较新版的Spring Boot取消了@SpringApplicationConfiguration这个注解，用@SpringBootTest就可以了

### 集成web环境测试

MockMvcBuilders.webAppContextSetup(WebAppContext webAppContext):指定的webAPPContext会从**上下文中去获取相应的控制器**并得到相应的MockMvc

怎么获取呢？所以就在测试的程序前面加上@ContextConfiguration(classes = xxx.class)（xxx.class是自己定义的配置类，继承WebMvcConfigurerAdapter 这个类的类，新版spring boot不用继承这类再去使用了，会报错，它改成直接用接口形式implements这个类，并且去使用，还有一个方法来着去使用这个类，但是我忘了，可以直接谷歌一下，WebMvcConfigurerAdapter配置类其实是Spring内部的一种配置方式，采用JavaBean的形式来代替传统的xml配置文件形式进行针对框架个性化定制，它是对需要进行拦截的地方是要使用的拦截器配好对）。

（拦截器是什么：拦截器相当于把一部分会需要经常改动的代码提取出来，然后对一些固有操作执行前进行拦截，去运行在不同情况下需要运行的代码。减少代码的冗余度，提高重用率。也就是AOP的一种运用。）

如果没有@ContextConfiguration是会报错的。

@WebAppConfiguration：测试环境使用，用来表示测试环境使用的ApplicationContext将是WebApplicationContext类型的；value指定web应用的根；

@Autowired WebApplicationContext wac:注入web环境的applicationContext容器；

然后通过MockMvcBuilders.webAppContextSetup(this.wac).build();创建一个MockMvc去测试。

### 独立测试方式

这个MockMvcBuilders.standaloneSetup(Object... controllers)可以通过参数去指定一组或者一个控制器，而不需要上下文获取了。

只需两个步骤：创建相应的控制器，然后注入相应的依赖；通过MockMvcBuilders.standaloneSetup(Object... controllers).bulid()去获取一个MockMvc去测试。

## RequestBuilder

RequestBuilder这个是用来存放模拟输入的命令的。比如在这里我要测试控制层的输出结果是不是我想要的，然后我会用RequestBuilder.get/post/put…(/网址/).param()…这样去模拟数据输入，进而测试程序有没有错误。
 
    		request = get("/users/");

RequestBuilder执行完这一步之后，里面会存放如下参数：

<center>{% qnimg 开始学习spring（四）：Junit测试/RequestBuilder.png %}</center>

可以从图中看到里面有我刚开始设置进去的网址"/users",然后其他值设置之类地方都是显示null，或者[]或者{}。

# 关于content().string(equalTo(xxx))

跟着敲完对控制层的测试类之后，一直报错，我debug发现，不是程序里面的问题，是我参数出的问题。

我里面的参数不是少了对字符串的外标的双引号，就是字符串的排列顺序不对

我设置的返回值是对User实例化后的字符串的值的返回

    public class User {
	    private String name;
	    private long id;
	    private Integer age;
    }

里面按照顺序需要：name 、 id 、 age这个顺序返回值，然后碰到字符串时候，需要上标双引号，但是因为本身我就是设置它需要根据我给出的字符串去比较是不是我想要的结果，所以在content().string(equalTo(xxx))里面，xxx这个字符串中的双引号需要转义字符：\"

    content().string(equalTo("[{\"name\":\"nana\",\"id\":1,\"age\":12}]"))).andDo(print())

## 测试部分

perform：执行一个RequestBuilder请求，会自动执行SpringMVC的流程并映射到相应的控制器执行处理；

andExpect：添加ResultMatcher验证规则，验证控制器执行完成后结果是否正确；

andDo：添加ResultHandler结果处理器，比如调试时打印结果到控制台；

andReturn：最后返回相应的MvcResult；然后进行自定义验证/进行下一步的异步处理；

accept：接收的返回的信息格式，这里是接收的是json类型数据。

ContentResultMatchers content()：得到响应内容验证器；

# 参考资料
[关于拦截器讲解比较详细的博客](https://blog.csdn.net/cankingapp/article/details/7626566)

[关于测试部分讲解详细的博客](https://www.cnblogs.com/lyy-2016/p/6122144.html)