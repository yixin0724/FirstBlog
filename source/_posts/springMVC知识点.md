---
title: springMVC知识点
date: 2022-10-12 21:35:20
cover: ./img/springMVC.png
tags: Java
categories: java
---



SpringMVC
1.简介
	SpringMVC是隶属于Spring框架的一部分，主要是用来进行Web开发，是对Servlet进行了封装。
	SpringMVC是处于Web层的框架，所以其主要的作用就是用来接收前端发过来的请求和数据然后经过处理并将处理的结果响应给前端，所以如何处理请求和响应是SpringMVC中非常重要的一块内容
	REST是一种软件架构风格，可以降低开发的复杂性，提高系统的可伸缩性，后期的应用也是非常广泛。

	SpringMVC主要负责的就是
		controller如何接收请求和数据
		如何将请求和数据转发给业务层
		如何将响应数据转换成json发回到前端
	
	SpringMVC是表现层的框架也就说web层
	Mybatis是数据层的框架

2.入门案例
	SpringMVC实现流程：
		1.创建web工程(Maven结构)
		2.设置tomcat服务器，加载web工程(tomcat插件)
		3.导入坐标(SpringMVC+Servlet)
		4.定义处理请求的功能类(UserController)
		5.设置请求映射(配置映射关系)
		6.将SpringMVC设定加载到Tomcat容器中

	使用步骤：
		①创建一个web项目
		②导入springmvc和servlet的坐标和tomcat7插件
		③创建SpringMVC控制器类(等同于Servlet功能，写在Controller包中)
			@Controller
			public class UserController {
				@RequestMapping("/save")
				public String save(){
			        System.out.println("user save ...");
			        return "{'moudle':'springmvc'}";
				}
			}
		④创建Spring配置类(初始化环境，设定对应加载的bean)
			@Configuration
			@ComponentScan("com.itheima.controller")
			public class SpringMvcConfig {
			}
		⑤删掉web.xml，改换为SpringMVC的(写在配置类包中)
			public class ServletContainersInitConfig extends AbstractDispatcherServletInitializer {
			    //加载springmvc配置类
			    protected WebApplicationContext createServletApplicationContext() {
			        //初始化WebApplicationContext对象
			        AnnotationConfigWebApplicationContext ctx = new AnnotationConfigWebApplicationContext();
			        //加载指定配置类
			        ctx.register(SpringMvcConfig.class);
			        return ctx;
			    }
	
			    //设置由springmvc控制器处理的请求映射路径
			    protected String[] getServletMappings() {
			        return new String[]{"/"};
			    }
	
			    //加载spring配置类
			    protected WebApplicationContext createRootApplicationContext() {
			        return null;
			    }
			}
	注意：这个配置类也可也继承AbstractAnnotationConfigDispatcherServletInitializer这个类，然后重写3个接口，可以简化开发
		如public class ServletContainersInitConfig extends AbstractAnnotationConfigDispatcherServletInitializer {
		    protected Class<?>[] getRootConfigClasses() {
		        return new Class[0];
		    }
	
		    protected Class<?>[] getServletConfigClasses() {
		        return new Class[]{SpringMvcConfig.class};
		    }
	
		    protected String[] getServletMappings() {
		        return new String[]{"/"};
		    }
	
		    //乱码处理
		    @Override
		    protected Filter[] getServletFilters() {
		        CharacterEncodingFilter filter = new CharacterEncodingFilter();
		        filter.setEncoding("UTF-8");
		        return new Filter[]{filter};
		    }
		}


3.bean加载控制
	SpringMVC加载表现层的相关bean(controller)
	Spring加载业务bean(service)和功能bean(DataSource)
	问题：因为功能不同，如何避免Spring错误加载到SpringMVC的bean?
	两种方法来避免：
		①Spring加载的bean设定扫描范围为精准范围，例如service包、dao包等
		②Spring加载的bean设定扫描范围为com.itheima,排除掉controller包中的bean
	注意：第二种方法采用的是注解的形式，如@ComponentScan(value="com.itheima",excludeFilters=@ComponentScan.Filter(type=FilterType.ANNOTATION,classes=Controller.class))

	ComponentScan相关属性
		excludeFilters:排除扫描路径中加载的bean,需要指定类别(type)和具体项(classes)
		includeFilters:加载指定的bean，需要指定类别(type)和具体项classes)

4.请求与响应
	 设置请求映射路径
	 在出现有不同的控制器中，访问路径一样，这个时候我们需要加上前缀避免完全一样，同时可以在类的上面写上@RequestMapping("前缀")，这样该类中的访问路径默认前面都带上了这个

	 请求参数
	 get：在get请求中，参数直接在请求网址中，因此直接在请求网址上加上?然后加参数即可，两个参数之间用&隔开，同时在控制器中，直接在方法的形参位置接收即可
	 post：后台代码都是不变的，还是写在形参里，参数利用postman，直接在那个Body中的form加即可，因为post的参数在请求体中
	
	 中文乱码处理
	 post:以前处理是在过滤器中，现在是在配置类中，在SPringMVC那个替换web.xml的类中，重写getServletFilters()方法，直接返回一个SpringMVC写好的过滤器
	 代码如：CharacterEncodingFilter filter = new CharacterEncodingFilter();
			filter.setEncoding("UTF-8");
			return new Filter[]{filter};
	
	前后端参数不一样时
	在参数传递的时候，如果出现后台和传的参数不一样，可以在后台的方法中的参数前加上注解@RequestParam("指明前端传的参数名字")
	
	当前端给的参数，后端给的该参数的对象时，直接复制给该对象。
	
	当前端给多个相同名字的参数时，后端给一个该名字的数组，即可接收
	
	当想用集合接收的时候，需要先在方法形参的前面加上注解@RequestParam然后直接用集合接收即可

5.json数据传递参数
	对于JSON数据类型，我们常见的有三种:
		①json普通数组（["value1","value2","value3",...]）
		②json对象（{key1:value1,key2:value2,...}）
		③json对象数组（[{key1:value1,...},{key2:value2,...}]）
	对于JSON普通数组
	第一步导入依赖：
		<groupId>com.fasterxml.jackson.core</groupId>
		<artifactId>jackson-databind</artifactId>
		<version>2.9.0</version>
	第二部在postman中点击body里面的raw然后选择json进行传输
	第三步在SpringMVC配置类中进行加上注解@EnableWebMvc开启json注解接收
	第四步控制类对应方法中参数前面加上注解@RequestBody
	其他都和这个一样


	日期类型参数传递
	Spring默认的日期格式为2088/08/18
	想要SPring接收所有的日期参数，在不符合默认的格式前加上注解并写上他的格式@DateTimeFormat(pattern="yyyy-MM-dd") 
	
	类型转换器
	Convert接口(org.springframework.core.convert.converter
)
	前端传递字符串，后端使用日期Date接收
	前端传递JSON数据，后端使用对象接收
	前端传递字符串，后端使用Integer接收
	后台需要的数据类型有很多中
	在数据的传递过程中存在很多类型的转换
	问:谁来做这个类型转换?
	答:SpringMVC
	其中Convert接口可以处理
	public interface Converter<S, T> {
		@Nullable
		//该方法就是将从页面上接收的数据(S)转换成我们想要的数据类型(T)返回
		T convert(S source);
	}
	注意：* S: the source type
		* T: the target type
		SpringMVC的配置类把@EnableWebMvc当做标配配置上去，不要省略
		其中HttpMessageConverter接口，该接口是实现对象与JSON之间的转换工作


6.响应
	在控制类中在方法中可以直接返回一个jsp等页面，也可也在方法上加注解@ResponseBody表示返回一个文本内容即为字符串
	如果想要返回一个pojo的对象，即json数据，首先需要在方法上加上注解@ResponseBody，然后导入jackson-databind依赖(在查询数据库那里也是这样用)，在这里面是HttpMessageConverter类型转换器在起作用

7.REST风格
	概述：表现形式状态转换,它是一种软件架构风格，其实就是访问网络资源的形式
	如传统风格资源描述形式
		http://localhost/user/getById?id=1 查询id为1的用户信息
		http://localhost/user/saveUser 保存用户信息
		REST风格描述形式
		http://localhost/user/1
		http://localhost/user
	优点：隐藏资源的访问行为，无法通过地址得知对资源是何种操作，书写简化

	注意：他是按照行为动作(即请求方式)进行区分对资源的操作
	如按照REST风格访问资源时使用行为动作区分对资源进行了何种操作
		http://localhost/users 查询全部用户信息 GET（查询）
		http://localhost/users/1 查询指定用户信息 GET（查询）
		http://localhost/users 添加用户信息 POST（新增/保存）
		http://localhost/users 修改用户信息 PUT（修改/更新）
		http://localhost/users/1 删除用户信息 DELETE（删除）
	根据REST风格对资源进行访问称为RESTful。


	入门案例
	在原先基础上，只需要在控制类中的方法注解@RequestMapping修改value路径，和添加method=RequestMethod.请求方法即可
	如果在理解添加1表示查询id=1的，首先在参数中加上@PathVariable然后在路径中修改为/users/{id}
	
	关于接收参数，我们学过三个注解@RequestBody、@RequestParam、@PathVariable ,这三个注解之间的区别和应用分别是什么?
	区别
	@RequestParam用于接收url地址传参或表单传参
	@RequestBody用于接收json数据
	@PathVariable用于接收路径参数，使用{参数名称}描述路径参数
	应用
	后期开发中，发送请求参数超过1个时，以json格式为主，@RequestBody应用较广
	如果发送非json格式数据，选用@RequestParam接收请求参数
	采用RESTful进行开发，当参数数量较少时，例如1个，可以采用@PathVariable接收请求路径变量，通常用于传递id值
	
	快速开发
	①把共同有的前缀直接写在类上加注解@RequestMapping的方式
	②如果所有方法都带@ResponseBody，直接在类上面写，并且把@Controller和@ResponseBody合成了@ResController
	③把对应增删改查，原来的@RequestMapping修改为对应的注解@DeleteMapping("/{id}")等等



8.SSM整合
	流程分析
	(1) 创建工程
		创建一个Maven的web工程
		pom.xml添加SSM需要的依赖jar包
		编写Web项目的入口配置类，实现AbstractAnnotationConfigDispatcherServletInitializer
		重写以下方法
			getRootConfigClasses() ：返回Spring的配置类->需要SpringConfig配置类
			getServletConfigClasses() ：返回SpringMVC的配置类->需要SpringMvcConfig配
			置类
			getServletMappings() : 设置SpringMVC请求拦截路径规则
			getServletFilters() ：设置过滤器，解决POST请求中文乱码问题
	(2)SSM整合[重点是各个配置的编写]
		SpringConfig
			标识该类为配置类 @Configuration
			扫描Service所在的包 @ComponentScan
			在Service层要管理事务 @EnableTransactionManagement
			读取外部的properties配置文件 @PropertySource
			整合Mybatis需要引入Mybatis相关配置类 @Import
				第三方数据源配置类 JdbcConfig
					构建DataSource数据源，DruidDataSouroce,需要注入数据库连接四要素， @Bean @Value
					构建平台事务管理器，DataSourceTransactionManager,@Bean
				Mybatis配置类 MybatisConfig
					构建SqlSessionFactoryBean并设置别名扫描与数据源，@Bean
					构建MapperScannerConfigurer并设置DAO层的包扫描
		SpringMvcConfig
			标识该类为配置类 @Configuration
			扫描Controller所在的包 @ComponentScan
			开启SpringMVC注解支持 @EnableWebMvc
	(3)功能模块[与具体的业务模块有关]
		创建数据库表
		根据数据库表创建对应的模型类
		通过Dao层完成数据库表的增删改查(接口+自动代理)
		编写Service层[Service接口+实现类]
			@Service
			@Transactional
			整合Junit对业务层进行单元测试
				@RunWith
				@ContextConfiguration
				@Test
		编写Controller层
			接收请求 @RequestMapping @GetMapping @PostMapping @PutMapping
			@DeleteMapping
			接收数据 简单、POJO、嵌套POJO、集合、数组、JSON数据类型
				@RequestParam
				@PathVariable
				@RequestBody
			转发业务层
				@Autowired
			响应结果
				@ResponseBody

	表现层数据封装
	可以将Result类放在控制层当中
	思路：
		为了封装返回的结果数据:创建结果模型类，封装数据到data属性中
		为了封装返回的数据是何种操作及是否操作成功:封装操作结果到code属性中
		操作失败后为了封装返回的错误信息:封装特殊消息到message(msg)属性中
	在添加完Result和Code类以后，去控制类中进行对方法的改进，把返回改为一个Result对象，采用三目运算符进行判断即可。
	
	异常处理
	异常经常出现的原因于种类：
		框架内部抛出的异常：因使用不合规导致
		数据层抛出的异常：因外部服务器故障导致（例如：服务器访问超时）
		业务层抛出的异常：因业务逻辑书写错误导致（例如：遍历业务书写操作，导致索引异常等）
		表现层抛出的异常：因数据收集、校验等规则导致（例如：不匹配的数据类型间导致异常）
		工具类抛出的异常：因工具类书写不严谨不够健壮导致（例如：必要释放的连接长期未释放等）
	问题：
		1. 各个层级均出现异常，异常处理代码书写在哪一层?
			所有的异常均抛出到表现层进行处理
		2. 异常的种类很多，表现层如何将所有的异常都处理到呢?
			异常分类
		3. 表现层处理异常，每个方法中单独书写，代码书写量巨大且意义不强，如何解决?
			AOP
	SpringMVC给的有异常处理器
	在controller表现层写异常处理类
	使用：①在类上写上注解@RestControllerAdvice
		②并且在方法上面添加注解@ExceptionHandler(Exception.class)表示拦截所有异常，然后可以直接返回Result类型的给前端页面展示。
	
	注意：得让SpringMVC扫描到他，
		@RestControllerAdvice此注解自带@ResponseBody注解与@Component注解，具备对应的功能
		@ExceptionHandler注解的此类方法可以根据处理的异常不同，制作多个方法分别处理对应的异常
	
	项目异常处理
	思路:
		1.先通过自定义异常，完成BusinessException和SystemException的定义
		2.将其他异常包装成自定义异常类型
		3.在异常处理器类中对不同的异常进行处理
	
	使用：①首先创建一个exception包
		②在里面创建自定义异常类，并继承RuntimeException类，并且重写父类的构造方法。
		③然后将其他异常包成自定义异常，可以用throw或者try catch抛
		④同时在Code类中补充相应的返回信息
		⑤然后去表现层拦截异常类进行处理自定义异常
	说明:
		让自定义异常类继承RuntimeException的好处是，后期在抛出这两个异常的时候，就不用在
		try...catch...或throws了
		自定义异常类中添加code属性的原因是为了更好的区分异常是来自哪个业务的
	
	与前端连接
	注意：因为添加了静态资源，SpringMVC会拦截，所有需要在SpringConfig的配置类中将静态资源进行放行。
		在配置类中写一个类SpringMvcSupport继承WebMvcConfigurationSupport类，并且重写addResourceHandlers方法，在里面写放行资源的路径，如registry.addResourceHandler("/pages/**").addResourceLocations("/pages/");
	
	SSM整合标准开发
	①自定义项目系统级异常
	axios.get("/books").then((res)=>{});
	axios.post("/books",this.formData).then((res)=>{});
	axios.delete("/books"+row.id).then((res)=>{});
	axios.put("/books",this.formData).then((res)=>{});
	axios.get("/books"+row.id).then((res)=>{});
	
	当出现js等无法显示，是因为被SpringMVC拦截了，可以进行放行
		@Configuration
		public class SpringMvcSupport extends WebMvcConfigurationSupport {
		    @Override
		    protected void addResourceHandlers(ResourceHandlerRegistry registry) {
		    	//表示当访问右边那个的资源的时候，进行放行
		        registry.addResourceHandler("/pages/**").addResourceLocations("/pages/");
		        registry.addResourceHandler("/css/**").addResourceLocations("/css/");
		        registry.addResourceHandler("/js/**").addResourceLocations("/js/");
		        registry.addResourceHandler("/plugins/**").addResourceLocations("/plugins/");
		    }
		}


9.拦截器
	流程：
		(1)浏览器发送一个请求会先到Tomcat的web服务器
		(2)Tomcat服务器接收到请求以后，会去判断请求的是静态资源还是动态资源
		(3)如果是静态资源，会直接到Tomcat的项目部署目录下去直接访问
		(4)如果是动态资源，就需要交给项目的后台代码进行处理
		(5)在找到具体的方法之前，我们可以去配置过滤器(可以配置多个)，按照顺序进行执行
		(6)然后进入到到中央处理器(SpringMVC中的内容)，SpringMVC会根据配置的规则进行拦截
		(7)如果满足规则，则进行处理，找到其对应的controller类中的方法进行执行,完成后返回结果
		(8)如果不满足规则，则不进行处理
		(9)这个时候，如果我们需要在每个Controller方法执行的前后添加业务，具体该如何来实现?
		这个就是拦截器要做的事。
	拦截器（Interceptor）是一种动态拦截方法调用的机制，在SpringMVC中动态拦截控制器方法的执行
	作用:
		在指定的方法调用前后执行预先设定的代码
		阻止原始方法的执行
	总结：拦截器就是用来做增强
	拦截器和过滤器之间的区别是什么?
	归属不同：Filter属于Servlet技术，Interceptor属于SpringMVC技术
	拦截内容不同：Filter对所有访问进行增强，Interceptor仅针对SpringMVC的访问进行增强

	入门案例
	步骤：
		①首先在表现层创建出拦截器层interceptor包，创建拦截类并实现HandlerInterceptor接口，在他里面有preHandle方法，postHandle方法，afterCompletion方法，分别代表原始操作进行前(当返回false进行拦截)，原始操作之后和完成之后的动作
	 	②定义配置类，也就是那个放行类，继承WebMvcConfigurationSupport，实现addInterceptor(扫描加载配置)
	 		@Configuration
			public class SpringMvcSupport extends WebMvcConfigurationSupport {
			    @Autowired
			    private ProjectInterceptor projectInterceptor;
	
			    @Override
			    protected void addResourceHandlers(ResourceHandlerRegistry registry) {
			        registry.addResourceHandler("/pages/**").addResourceLocations("/pages/");
			    }
	
			    @Override
			    protected void addInterceptors(InterceptorRegistry registry) {
					//配置拦截器
			        registry.addInterceptor(projectInterceptor).addPathPatterns("/books","/books/*" );
			    }
			}
		③:SpringMVC添加SpringMvcSupport包扫描
	
	 注意：定义拦截器类，实现HandlerInterceptor接口
	 	  注意当前类必须受Spring容器控制，加上注解@Component
	
	简化开发
	可以把SpringMvcSupport合并到SpringMvcConfig里面
	步骤：①SpringMvcConfig实现WebMvcConfiguer接口
		  ②把拦截器容器ProjectInterceptor注入进去，并自动装配
		  ③然后直接重写addInterceptors方法即可
	
	拦截器参数
	request:请求对象
	response:响应对象
	handler:被调用的处理器对象，本质上是一个方法对象，对反射中的Method对象进行了再包装
	modelAndView:如果处理器执行完成具有返回结果，可以读取到对应数据与页面信息，并进行调整
	注意：因为咱们现在都是返回json数据，所以该参数的使用率不高。
	ex:如果处理器执行过程中出现异常对象，可以针对异常情况进行单独处理
	注意：因为我们现在已经有全局异常处理器类，所以该参数的使用率也不高。
	总结：这三个方法中，最常用的是preHandle,在这个方法中可以通过返回值来决定是否要进行放行，我们可以把业务逻辑放在该方法中，如果满足业务则返回true放行，不满足则返回false拦截。
	
	拦截器链
	结论：拦截器执行的顺序是和配置顺序有关。先进后出。
		当配置多个拦截器时，形成拦截器链
		拦截器链的运行顺序参照拦截器添加顺序为准
		当拦截器中出现对原始处理器的拦截，后面的拦截器均终止运行
		当拦截器运行中断，仅运行配置在前面的拦截器的afterCompletion操作
		preHandle：与配置顺序相同，必定运行
		postHandle:与配置顺序相反，可能不运行
		afterCompletion:与配置顺序相反，可能不运行。
		这个顺序不太好记，最终只需要把握住一个原则即可:以最终的运行结果为准
