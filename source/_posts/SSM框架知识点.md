---
title: SSM三个框架的知识点
date: 2022-09-18 22:39:16
cover: https://pic.imgdb.cn/item/66ebf119f21886ccc0d25705.jpg
tags: 
  - SSM
  - Java
categories: 技术记录
description: 关于SSM的一些知识。
---











### 一、小白时记录

dao层(数据层)一般写的是接口，给的是相应的业务方法，写对应的数据层注解
domain层一般写的是用户的类，给的属性和相应的set和get方法
cofig层一般写的是配置类
controller一般写的是控制类就是表现层，也就是访问资源路径等
service层里面还有impl实现层，外层写的是接口，impl写的是实现业务方法
pojo层一般表示的是实体类

通过Ioc和AOP(面向切面编程)来简化开发













### 二、Spring

1.概述：Spring其实指的是Spring Framework。

2.系统架构：
	(1)核心层
		Core Container:核心容器，这个模块是Spring最核心的模块，其他的都需要依赖该模块
	(2)AOP层
		AOP:面向切面编程，它依赖核心层容器，目的是在不改变原有代码的前提下对其进行功能增强
		Aspects:AOP是思想,Aspects是对AOP思想的具体实现
	(3)数据层
		Data Access:数据访问，Spring全家桶中有对数据访问的具体实现技术
		Data Integration:数据集成，Spring支持整合其他的数据层解决方案，比如Mybatis
		Transactions:事务，Spring中事务管理是Spring AOP的一个具体实现，也是后期学习的
		重点内容
	(4)Web层
		这一层的内容将在SpringMVC框架具体学习
	(5)Test层
		Spring主要整合了Junit来完成单元测试和集成测试

3.核心概念
	代码书写：使用对象时，不要主动去new传生对象，转换由外部提供对象，要追求高内聚低耦合

	IOC（Inversion of Control）控制反转
		概念：对象的创建控制权由程序转移到外部，这种思想叫控制反转
	(1)Spring和IOC之间的关系是什么呢?
		Spring技术对IOC思想进行了实现
		Spring提供了一个容器，称为IOC容器，用来充当IOC思想中的"外部"
		IOC思想中的别人[外部]指的就是Spring的IOC容器
	(2)IOC容器的作用以及内部存放的是什么?
		IOC容器负责对象的创建、初始化等一系列工作，其中包含了数据层和业务层的类对象
		被创建或被管理的对象在IOC容器中统称为Bean
		IOC容器中放的就是一个个的Bean对象
	(3)当IOC容器中创建好service和dao对象后，程序能正确执行么?
		不行，因为service运行需要依赖dao对象
		IOC容器中虽然有service和dao对象
		但是service对象和dao对象没有任何关系
		需要把dao对象交给service,也就是说要绑定service和dao对象之间的关系
	
	DI（Dependency Injection）依赖注入
	(1)什么是依赖注入呢?
		在容器中建立bean与bean之间的依赖关系的整个过程，称为依赖注入
		业务层要用数据层的类对象，以前是自己new的
		现在自己不new了，靠别人[外部其实指的就是IOC容器]来给注入进来
		这种思想就是依赖注入
	(2)IOC容器中哪些bean之间要建立依赖关系呢?
		这个需要程序员根据业务需求提前建立好关系，如业务层需要依赖数据层，service就要和dao建立依赖关系
	小结：介绍完Spring的IOC和DI的概念后，我们会发现这两个概念的最终目标就是:充分解耦，具体实现靠:
		使用IOC容器管理bean（IOC)
		在IOC容器内将有依赖关系的bean进行关系绑定（DI）
		最终结果为:使用对象时不仅可以直接从IOC容器中获取，并且获取到的bean已经绑定了所有的依赖关系.

4.IoC入门案例
	①导入spring坐标：<dependency>
				      <groupId>org.springframework</groupId>
				      <artifactId>spring-context</artifactId>
				      <version>5.2.10.RELEASE</version>
				   </dependency>
	②在resoures下创建xml配置文件，进行配置bean
		<!--bean标签标示配置bean id属性标示给bean起名字 class属性表示给bean定义类型-->
		<bean id="bookDao" class="com.itheima.dao.impl.BookDaoImpl"/>
		<bean id="bookService" class="com.itheima.service.impl.BookServiceImpl"/>
	③在service层创建类，获取IoC容器，获取bean
		获取IOC容器  ApplicationContext ctx = new ClassPathXmlApplicationContext("applicationContext.xml");
		也可以用绝对路径获取容器	        ApplicationContext ctx = new FileSystemXmlApplicationContext("D:/applicationContext.xml");

	④调用方法
		BookService bookService = (BookService) ctx.getBean("bookService");
		bookService.save();

5.DI入门案例
	基于IoC案例上进行改善
	①在BookServiceImpl类中，删除业务层中使用new的方式创建的dao对象
	②为(本来new的那个对象)属性提供set+名字方法
	③修改配置完成注入
		<bean id="bookDao" class="com.itheima.dao.impl.BookDaoImpl"/>
		<bean id="bookService" class="com.itheima.service.impl.BookServiceImpl">
			<!--配置server与dao的关系-->
			<!--property标签表示配置当前bean的属性 name属性表示配置哪一个具体的属性 ref属性表示参照哪一个bean-->
			<property name="bookDao" ref="bookDao"/>
		</bean>
	注意:配置中的两个bookDao的含义是不一样的
		name="bookDao"中bookDao的作用是让Spring的IOC容器在获取到名称后，将首字母大写，前面加set找对应的setBookDao()方法进行对象注入
		ref="bookDao"中bookDao的作用是让Spring能在IOC容器中找到id为bookDao的Bean对象给bookService进行注入

6.bean配置
	别名：在resources里面xml配置文件里面bean标签加个name属性，中间用空格或者逗号或者分号隔开表示多个名字

	bean的作用范围
	  spring默认给的是单个bean，意思是默认只给一个对象(多个对象同一个地址)，可以在bean的属性中给出scope，并且值给出prototype(表示非单例)
	
	思考问题：
	①为什么bean默认为单例?
		bean为单例的意思是在Spring的IOC容器中只会有该类的一个对象
		bean对象只有一个就避免了对象的频繁创建与销毁，达到了bean对象的复用，性能高
	②bean在容器中是单例的，会不会产生线程安全问题?
		如果对象是有状态对象，即该对象有成员变量可以用来存储数据的，
		因为所有请求线程共用一个bean对象，所以会存在线程安全问题。
		如果对象是无状态对象，即该对象没有成员变量没有进行数据存储的，
		因方法中的局部变量在方法调用完成后会被销毁，所以不会存在线程安全问题。
	
	③哪些bean对象适合交给容器进行管理?
		①表现层对象
		②业务层对象
		③数据层对象
		④工具对象
	④哪些bean对象不适合交给容器进行管理?
		①封装实例的域对象，因为会引发线程安全问题，所以不适合。

7.bean实例化
	bean是如何创建的？
	①构造方法
		bean本质上是对象，创建bean使用无参构造方法，然后在xml配置bean来完成
	②使用静态工厂创建(了解)
		先写出一个工厂类，写出静态方法返回对象，然后在配置文件里面配置bean，并且给出factory-method，class给出工厂名
	③使用实例工厂 
		写出一个工厂类，写出一个非静态方法，在实现类里面new工厂类调用方法，然在配置文件里面配置bean，先给出bean指向工厂类，然后下面写第二个bean，不写class，给出factory-method属性，同时给出factory-bean指向上面给出的那个bean名字
	④实例工厂的改良(常用)
		创建工厂类且类名加上Bean，并且实现FactoryBean<要实现的dao>接口，重写其中三个方法getObject()和getObjectType()和isSingleton()，其中第一个返回实现接口的实现类对象，第二个返回实现接口的.class，第三个然后true表示创造单例对象，false表示非单例，然后配置bean，class指向工厂类即可

8.bean的生命周期
	②Spring提供了两个接口来完成生命周期的控制，好处是可以不用再进行配置init-method和destroy-method
		修改BookServiceImpl类，添加两个接口InitializingBean， DisposableBean并实现接口中的两个方法afterPropertiesSet和destroy
	(1)关于Spring中对bean生命周期控制提供了两种方式:
		在配置文件中的bean标签中添加init-method和destroy-method属性
		类实现InitializingBean与DisposableBean接口，这种方式了解下即可。
	(2)对于bean的生命周期控制在bean的整个生命周期中所处的位置如下:
		初始化容器
			1.创建对象(内存分配)
			2.执行构造方法
			3.执行属性注入(set操作)
			4.执行bean初始化方法
		使用bean
			1.执行业务操作
		关闭/销毁容器
			1.执行bean销毁方法
	(3)关闭容器的两种方式:
		ConfigurableApplicationContext是ApplicationContext的子类
		close()方法
		registerShutdownHook()方法

9.依赖注入方式
	思考：(1)向一个类中传递数据的方式有几种？
				①普通方法(set方法)
				②构造方法
		  (2)依赖注入描述了在容器中建立bean与bean之间依赖关系的过程，如果bean运行需要的是数字或字符串呢？
		  		①引用类型
		  		②简单类型(基本数据类型与String)
	依赖注入方式
	①setter注入(推荐使用)
		(1)引用类型
			①在bean中定义引用类型属性，并提供可访问的set方法
				public class BookServiceImpl implements BookService {
					private BookDao bookDao;
					public void setBookDao(BookDao bookDao) {
					this.bookDao = bookDao;
					}
				}
			②配置中使用property标签ref属性注入引用类型对象
				<bean id="bookService" class="com.itheima.service.impl.BookServiceImpl">
					<property name="bookDao" ref="bookDao"/>
				</bean>
				<bean id="bookDao" class="com.itheima.dao.imipl.BookDaoImpl"/>
		(2)简单类型
			①在bean中定义引用类型属性并提供可访问的set方法
			②配置中使用property标签value属性注入简单类型数据
	②构造方法
		(1)引用类型
			①在bean中定义引用类型属性并提供可访问的构造方法
			②配置中使用constructor-arg标签ref属性注入引用类型对象，其中该标签name指的的是形参名字
		(2)简单类型
			①在bean中定义引用类型属性并提供可访问的构造方法
			②配置中使用constructor-arg标签value属性注入简单类型数据
	依赖注入方式选择
		1. 强制依赖使用构造器进行，使用setter注入有概率不进行注入导致null对象出现强制依赖指对象在创建的过程中必须要注入指定的参数
		2. 可选依赖使用setter注入进行，灵活性强可选依赖指对象在创建过程中注入的参数可有可无
		3. Spring框架倡导使用构造器，第三方框架内部大多数采用构造器注入的形式进行数据初始化，相对严谨
		4. 如果有必要可以两者同时使用，使用构造器注入完成强制依赖的注入，使用setter注入完成可选依赖的注入
		5. 实际开发过程中还要根据实际情况分析，如果受控对象没有提供setter方法就必须使用构造器注入
		6. 自己开发的模块推荐使用setter注入

10.依赖自动装配
	概述：IoC容器根据bean所依赖的资源在容器中自动查找并注入到bean中的过程称为自动装配
	自动装配方式有哪些?
		①按类型（常用）
			直接在配置中bean标签提供autowire属性，要求类型匹配是唯一的，意思就是定义的属性如name，必须唯一只有一个实现类
		注意事项:
			需要注入属性的类中对应属性的setter方法不能省略
			被注入的对象必须要被Spring的IOC容器管理
		②按名称
		③按构造方法
		④不启用自动装配
	注意：
		1. 自动装配用于引用类型依赖注入，不能对简单类型进行操作
		2. 使用按类型装配时（byType）必须保障容器中相同类型的bean唯一，推荐使用
		3. 使用按名称装配时（byName）必须保障容器中具有指定名称的bean，因变量名与配置耦合，不推荐使用
		4. 自动装配优先级低于setter注入与构造器注入，同时出现时自动装配配置失效


11.集合注入
	使用：①先在配置文件中bean指向要注入集合的类
		②根据集合的类型进行相关的注入方法
	说明：property标签表示setter方式注入，构造方式注入constructor-arg标签内部也可以写<array>、<list>、<set>、<map>、<props>标签
		List的底层也是通过数组实现的，所以<list>和<array>标签是可以混用
		集合中要添加引用类型，只需要把<value>标签改成<ref>标签，这种方式用的比较少

12.spring读取properties文件
	使用：①在resources下创建一个properties文件，并添加对应属性的键值对
		  ②开启context命名空间
		  		<?xml version="1.0" encoding="UTF-8"?>
				<beans xmlns="http://www.springframework.org/schema/beans"
					xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
					xmlns:context="http://www.springframework.org/schema/context"
					xsi:schemaLocation="
						http://www.springframework.org/schema/beans
						http://www.springframework.org/schema/beans/spring-beans.xsd
						http://www.springframework.org/schema/context
						http://www.springframework.org/schema/context/springcontext.xsd">
				</beans>
			③加载properties配置文件
				在配置文件中使用context命名空间下的标签来加载properties配置文件
				<context:property-placeholder location="jdbc.properties"/>
				在这里推荐使用：<context:property-placeholder location="classpath:*.properties"/>
				这个只能读取当前工程的文件，classpath*：.properties是表示读取所有
			④使用属性占位符${}完成属性注入
				使用${key}来读取properties配置文件中的内容并完成属性注入
					<context:property-placeholder location="jdbc.properties"/>
					<bean id="dataSource" class="com.alibaba.druid.pool.DruidDataSource">
						<property name="driverClassName" value="${jdbc.driver}"/>
						<property name="url" value="${jdbc.url}"/>
						<property name="username" value="${jdbc.username}"/>
						<property name="password" value="${jdbc.password}"/>
					</bean>


13.容器
	容器的创建方式：
		①基本的：ApplicationContext ctx = new ClassPathXmlApplicationContext("applicationContext.xml");
		②还有用绝对路径获取的：ApplicationContext ctx = new FileSystemXmlApplicationContext("D:/applicationContext.xml");
	Bean的获取方式：
		①BookDao bookDao = (BookDao) ctx.getBean("bookDao");
		注意：这种方式存在的问题是每次获取的时候都需要进行类型转换，有没有更简单的方式呢?
		②BookDao bookDao = ctx.getBean("bookDao"，BookDao.class);
		注意：这种方式可以解决类型强转问题，但是参数又多加了一个，相对来说没有简化多少。
		③BookDao bookDao = ctx.getBean(BookDao.class);
		注意：这种方式就类似我们之前所学习依赖注入中的按类型注入。必须要确保IOC容器中该类型对应的bean对象只能有一个。	

	小结
	①BeanFactory是IoC容器的顶层接口，初始化BeanFactory对象时，加载的bean延迟加载
	②ApplicationContext接口是Spring容器的核心接口，初始化时bean立即加载
	③ApplicationContext接口提供基础的bean操作相关方法，通过其他接口扩展其功能
	④ApplicationContext接口常用初始化类
		ClassPathXmlApplicationContext(常用)
		FileSystemXmlApplicationContext
	bean的配置
	①id：bean的id
	②class：bean的类型
	③scope：控制bean的实例数量
	④init-method：生命周期初始化方法
	⑤destory-method：销毁方法


14.注解开发
	使用@Component注解定义bean
		①首先在需要bean管理的类上面加上注解
		②在配置文件中开辟context空间，然后进行contxt组件扫描加载bean
			<context:component-scan base-package="com.itheima"/>
	注意：在service层使用@Service，在数据层impl中使用@Repository，在表现层使用@Controller，用法和Component一样

	纯注解开发
	步骤：①首先创建一个新的包config，创建一个配置类，
	②在上面注解@Configuration表示Spring配置文件，
	③在写个注解@ComponentScan("com.itheima")表示扫描com.itheima包下的bean
	④需要重写容器获取方法
		ApplicationContext ctx = new AnnotationConfigApplicationContext(SpringConfig.class);
	注意：①@Configuration注解用于设定当前类为配置类
		  ②@ComponentScan注解用于设定扫描路径，此注解只能添加一次，多个数据请用数组格式
		  	如@ComponentScan({com.itheima.service","com.itheima.dao"})
	将创建bean的单例修改，只需要加上注解@Scope("prototype")
	控制生命周期，只需要在对应的方法上添加@PostConstruct(表示init)和@PreDestroy(表示destroy)注解即可。
	注意:@PostConstruct和@PreDestroy注解如果找不到，需要导入下面的jar包
			<dependency>
				<groupId>javax.annotation</groupId>
				<artifactId>javax.annotation-api</artifactId>
				<version>1.3.2</version>
			</dependency>
	
	注解实现依赖注入
		自动装配引用型类型：
		添加@Autowired注解
		注意:@Autowired可以写在属性上，也可也写在setter方法上，最简单的处理方式是写在属性上并将setter方法删除掉
			(1)为什么setter方法可以删除呢?
			自动装配基于反射设计创建对象并通过暴力反射为私有属性进行设值
			普通反射只能获取public修饰的内容
			暴力反射除了获取public修饰的内容还可以获取private修改的内容
			所以此处无需提供setter方法
			(2)@Autowired是按照类型注入，如果有多个，这个时候需要用@Qualifier注解后的值就是需要注入的bean的名称。
			注意:@Qualifier不能独立使用，必须和@Autowired一起使用
	
		注入简单类型
		两种方式：①可以直接在变量的上面加注解@Value(值)
				②当要加载外部的properties文件时，使用@PropertySource注解加载，这个注解写在配置类当中，然后使用@Value读取配置文件中的内容，如@Value("${name}")
		注意:如果读取的properties配置文件有多个，可以使用@PropertySource的属性来指定多个
			@PropertySource注解属性中不支持使用通配符*,运行会报错
			@PropertySource注解属性中可以把classpath:加上,代表从当前项目的根路径找文件

15.注解开发管理第三方bean
	使用步骤：
		①导入对应的jar包
			在这里我们导的是Druid的
		②在配置类中添加一个方法
		③在方法上添加@Bean注解
			注意:
				不能使用DataSource ds = new DruidDataSource()
				在一个方法的上面加上@Bean代表这方法返回的是一个bean类型
		④从IOC容器中获取对象并打印

	引入外部配置类
	把代码都写到SpringConfig类中不美观我们需要另写一个jdbcConfig配置类
	导入外部配置类的两种方法：
		①在新添加的配置类中也加上配置类的注解@Configuration，同时在SpringConfig里面添加包扫描@ComponentScan("com.itheima.config")，这种不推荐
		②使用@Improt导入，在Spring配置类上使用@Import注解手动引入需要加载的配置类，如@Import({JdbcConfig.class})
	注意：扫描注解可以移除
		  @Import参数需要的是一个数组，可以引入多个配置类。
		  @Import注解在配置类中只能写一次
	
	为第三方bean添加资源
		简单类型注入
			将原本方法里面直接赋值的改为创建私人变量，并且添加@Value进行注入值
		引用类型注入
			只需要为bean定义方法设置形参，就可以按照类型自动装配

16.Spring整合MyBatis
	spring技术要使用数据库，首先导入spring-jdbc，然后使用mybatis要导入mybatis-spring，它属于mybatis

17.Spring整合Junit测试
	首先在main下创建一个test文件夹，然后把要创建要测试的类，在类上面加上注解@RunWith(SpringJUnit4ClassRunner.class)和注解@ContextConfiguration(classes = SpringConfig.class)，然后对私人变量进行自动装配@Autowired，然后哪个方法测试哪个加上@Test即可

18.AOP
	概述：AOP(Aspect Oriented Programming)面向切面编程，一种编程范式，指导开发者如何组织程序结构。
		切面编程里面有个通知类里面的方法叫通知，测试方法的叫连接点，中间有个切入点(允许被代理的点)，通知通过切面来切入进行连接
		  OOP(Object Oriented Programming)面向对象编程
 	作用:在不惊动原始设计的基础上为其进行功能增强，前面咱们有技术就可以实现这样的功能即代理模式

 	核心概念
 		①连接点(JoinPoint)：程序执行过程中的任意位置，粒度为执行方法、抛出异常、设置变量等
 			(1)在SpringAOP中，理解为方法的执行
 		②切入点(Pointcut):匹配连接点的式子
 			(1)在SpringAOP中，一个切入点可以描述一个具体方法，也可也匹配多个方法
 				一个具体的方法:如com.itheima.dao包下的BookDao接口中的无形参无返回值的save方法
 				匹配多个方法:所有的save方法，所有的get开头的方法，所有以Dao结尾的接口中的任意方法，所有带有一个参数的方法
 			(2)连接点范围要比切入点范围大，是切入点的方法也一定是连接点，但是是连接点的方法就不一定要被增强，所以可能不是切入点。
 		③通知(Advice):在切入点处执行的操作，也就是共性功能
 			(1)在SpringAOP中，功能最终以方法的形式呈现
 		④通知类：定义通知的类
 		⑤切面(Aspect):描述通知与切入点的对应关系。

19.AOP入门案例
	思路分析：①我们采用注解的形式或者xml，这里选择注解
			 ②导入坐标(pom.xml)
			 ③制作连接点(原始操作，Dao接口与实现类)
			 ④制作共性功能(通知类与通知)
			 ⑤定义切入点
			 ⑥绑定切入点与通知关系(切面)
	实现步骤：①导入坐标
				<groupId>org.aspectj</groupId>
				<artifactId>aspectjweaver</artifactId>
				<version>1.9.4</version>
			 ②dao层接口和impl实现类，和执行类都没有影响
			 ③在配置类里面加上注解@EnableAspectJAutoProxy表示用了注解AOP
			 ④创建一个AOP包，里面创建通知类(MyAdvice)
			 ⑤在通知类里创建切入点pt()并加上注解@Pointcut("execution(void com.itheima.dao.BookDao.update())")
			 ⑥创建通知方法(即需要增强什么功能)，并且加上注解@Before("pt()")与切入点绑定
		注意：切入点必须是一个私人的，无返回值的，无参的，并且没有内容。

	AOP的工作流程
		①Spring容器启动
			容器启动就需要去加载bean,哪些类需要被加载呢?
			需要被增强的类，如:BookServiceImpl
			通知类，如:MyAdvice
			注意此时bean对象还没有创建成功
		②读取所有切面配置中的切入点
		③初始化bean
			。判定bean对应的类中的方法是否匹配到任意切入点
				。注意第1步在容器启动的时候，bean对象还没有被创建成功。
				。要被实例化bean对象的类中的方法和切入点进行匹配
					。匹配失败，创建原始对象,如UserDao
						。匹配失败说明不需要增强，直接调用原始对象的方法即可。
					。匹配成功，创建原始对象（目标对象）的代理对象,如: BookDao
						。匹配成功说明需要对其进行增强
						。对哪个类做增强，这个类对应的对象就叫做目标对象
						。因为要对目标对象进行功能增强，而采用的技术是动态代理，所以会为其创建一个代理对象
						。最终运行的是代理对象的方法，在该方法中会对原始方法进行功能增强
		④获取bean执行方法
			获取的bean是原始对象时，调用方法并执行，完成操作
			获取的bean是代理对象时，根据代理对象的运行模式运行原始方法与增强的内容，完成操作
	
		新添加的概念：
		目标对象(Target)：原始功能去掉共性功能对应的类产生的对象，这种对象是无法直接完成最终工作的
		代理(Proxy)：目标对象无法直接完成工作，需要对其进行功能回填，通过原始对象的代理对象实现
		SpringAOP的本质或者可以说底层实现是通过代理模式。


20.AOP切入点表达式
	切入点：要进行增强的方法
	切入点表达式：要进行增强的方法的描述方法
	切入点表达式标准格式：动作关键字(访问修饰符 返回值 包名.类/接口名.方法名(参数) 异常名）
	如execution(public User com.itheima.service.UserService.findById(int))
		execution：动作关键字，描述切入点的行为动作，例如execution表示执行到指定切入点
		public:访问修饰符,还可以是public，private等，可以省略
		User：返回值，写返回值类型
		com.itheima.service：包名，多级包使用点连接
		UserService:类/接口名称
		findById：方法名
		int:参数，直接写参数的类型，多个类型用逗号隔开
		异常名：方法定义中抛出指定异常，可以省略

	通配符
		①*：单个独立的任意符号，可以独立出现，也可以作为前缀或者后缀的匹配符出现
			execution（public * com.itheima.*.UserService.find*(*))
			匹配com.itheima包下的任意包中的UserService类或接口中所有find开头的带有一个参数的方法
		②..：多个连续的任意符号，可以独立出现，常用于简化包名与参数的书写
			execution（public User com..UserService.findById(..))
			匹配com包下的任意包中的UserService类或接口中所有名称为findById的方法
		③+：专用于匹配子类类型
			execution(* *..*Service+.*(..))
			这个使用率较低，描述子类的，咱们做JavaEE开发，继承机会就一次，使用都很慎重，所以很少用它。*Service+，表示所有以Service结尾的接口的子类。
	书写技巧
		所有代码按照标准规范开发，否则以下技巧全部失效
		描述切入点通常描述接口，而不描述实现类,如果描述到实现类，就出现紧耦合了
		访问控制修饰符针对接口开发均采用public描述（可省略访问控制修饰符描述）
		返回值类型对于增删改类使用精准类型加速匹配，对于查询类使用*通配快速描述
		包名书写尽量不使用..匹配，效率过低，常用*做单个包描述匹配，或精准匹配
		接口名/类名书写名称与模块相关的采用*匹配，例如UserService书写成*Service，绑定业务
		层接口名
		方法名书写以动词进行精准匹配，名词采用匹配，例如getById书写成getBy,selectAll书写成
		selectAll
		参数规则较为复杂，根据业务方法灵活调整
		通常不使用异常作为匹配规则


21.AOP通知类型
	五种类型：前置通知@Before
			 后置通知@After
			 环绕通知(重点)@Around
			 返回后通知(了解)@AfterReturning
			 抛出异常后通知(了解)@AfterThrowing
	关于环绕通知
		①需要在参数里面加上ProceedingJoinPoint pjp，
		②如果是有参数的，首先通知方法需要改为Object类型，同时需要加上返回值，可以接收原始操作的返回值并且返回出去

	其中pip.proceed()表示对原始操作的调用
	pip.proceed(Object[] args)：可以返回参数值
	
	其中pip.getSignature可以获取执行前面信息
	Signature signature = pjp.getSignature();
	
	通过签名获取执行操作名称(接口名)
	String className = signature.getDeclaringTypeName();
	
	通过签名获取执行操作名称(方法名)
	String methodName = signature.getName();
	可以通过这个得到各个方法的执行效率

22.AOP通知获取数据
	分析：
		获取切入点方法的参数，所有的通知类型都可以获取参数
			JoinPoint：适用于前置、后置、返回后、抛出异常后通知
			ProceedingJoinPoint：适用于环绕通知
			如jp.getArgs()
		获取切入点方法返回值，前置和抛出异常后通知是没有返回值，后置通知可有可无，所以不做研究
			返回后通知
			环绕通知
		获取切入点方法运行异常信息，前置和返回后通知是不会有，后置通知可有可无，所以不做研究
			抛出异常后通知
			环绕通知

	String.trim()：可以对参数进行去空格处理

23.Spring事务
	事务作用：在数据层保障一系列的数据库操作同成功同失败
	Spring事务作用：在数据层或业务层保障一系列的数据库操作同成功同失败
	Spring通过平台事务管理器PlatformTransactionManager接口来管理事务
	其中有两个方法，commit是用来提交事务，rollback是用来回滚事务。
	他有一个实现类DataSourceTransactionManager
	使用步骤：
		①在需要被事务管理(业务层impl)的方法上添加注解@Transactional
		②在配置类JdbcConfig类中配置事务管理器
			加上注解@Bean，然后创建事务管理器接口，并且在方法中传入数据对象的形参，
			@Bean
			public PlatformTransactionManager transactionManager(DataSourcedataSource){
				DataSourceTransactionManager transactionManager = new DataSourceTransactionManager();
				transactionManager.setDataSource(dataSource);
				return transactionManager;
				}
			}
		注意：事务管理器要根据使用技术进行选择，Mybatis框架使用的是JDBC事务，可以直接使用DataSourceTransactionManager
		③开启事务注解
			在SpringConfig的配置类中开启，告诉Spring我用了注解开启事务@EnableTransactionManagement

	注意：
		@Transactional可以写在接口类上、接口方法上、实现类上和实现类方法上
		写在接口类上，该接口的所有实现类的所有方法都会有事务
		写在接口方法上，该接口的所有实现类的该方法都会有事务
		写在实现类上，该类中的所有方法都会有事务
		写在实现类方法上，该方法上有事务
		建议写在实现类或实现类的方法上
	
	事务@Transactional属性
		rollbackFor：设置事务回滚异常(class),把该异常也加入到回滚当中
		Propagation：设置事务传播行为，当值设置为Propagation.REQUIRES_NEW意思为设置新的事务
	
	事务传播行为：事务协调员对事务管理员所携带事务的处理态度。
	
	注意：当同为一个事务的时候，才可以一起进行回滚
	
	事务角色：
		事务管理员表示为在业务层进行处理业务的哪个方法
		事务协调员表示为在数据层接口中的接口方法
		开启Spring的事务管理后，Spring默认会把事务协调员都变成和事务管理员一个事务
		小结：
			事务管理员：发起事务方，在Spring中通常指代业务层开启事务的方法
			事务协调员：加入事务方，在Spring中通常指代数据层方法，也可以是业务层方法















### 三、SpringMVC

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













### 四、Maven进阶

1.分模块开发
	步骤：
		①在该模块pom依赖里面引入另一个模块的坐标(在pom里面有)
		②然后对该模块进行install打包，把他打包到本地仓库(因为idea能找不到坐标，但是本地仓库没有)


2.依赖管理
	依赖传递		
	依赖具有传递性，可间接传递也可也直接传递，但是会出现冲突
	依赖冲突问题
		路径优先：当依赖中出现相同的资源时，层级越深，优先级越低，层级越浅，优先级越高
		声明优先：当资源在相同层级被依赖时，配置顺序靠前的覆盖配置顺序靠后的
		特殊优先：当同级配置了相同资源的不同版本，后配置的覆盖先配置的
	可选依赖
	概述：可选依赖指对外隐藏当前所依赖的资源---不透明
		在依赖里面加一个属性optional，设置为true表示隐藏
		可选依赖是隐藏当前工程所依赖的资源，隐藏后对应资源将不具有依赖传递-
	排除依赖
		在主要模块pom里面的依赖添加属性<exclusions>  <exclusion>,并把不需要的依赖坐标写里面


3.聚合与继承
	所谓聚合:将多个模块组织成一个整体，同时进行项目构建的过程称为聚合
	聚合工程：通常是一个不具有业务功能的"空"工程（有且仅有一个pom文件）
	作用：使用聚合工程可以将多个工程编组，通过对聚合工程进行构建，实现对所包含的模块进行同步构建
	当工程中某个模块发生更新（变更）时，必须保障工程中与已更新模块关联的模块同步更新，此时可以使用聚合工程来解决批量模块同步构建的问题。

	聚合工程创建：①创建一个maven模块，然后设置打包类型为pom
				②设置当前聚合工程所包含的子模块名称
					<moudles>
						<moudle>../maven_ssm</module>
					</modules>
	注意：顺序不用管，他自己会按照依赖进行顺序构建
	
	继承
	所谓继承:描述的是两个工程间的关系，与java中的继承相似，子工程可以继承父工程中的配置信息，常见于依赖关系的继承。
	作用：
		简化配置
		减少版本冲突
	
	配置关系
	在pom文件里面用<parent>标签输入组，id，版本，指明他的父类，并指出路径
	
	子类不用定义依赖，可以直接用父类的，并且跟父类是同步的，方便统一管理
	同时可以在下面定义依赖管理<dependencyManagement>，在这个标签里面定义可选依赖的坐标，子类如果想用需要直接在依赖导入可选依赖坐标即可，但是不能配置版本，如果配了就不属于父类管理了
	
	聚合和继承的区别
	两种之间的作用:
		聚合用于快速构建项目，对项目进行管理
		继承用于快速配置和管理子项目中所使用jar包的版本
	聚合和继承的相同点:
		聚合与继承的pom.xml文件打包方式均为pom，可以将两种关系制作到同一个pom文件中
		聚合与继承均属于设计型模块，并无实际的模块内容
	聚合和继承的不同点:
		聚合是在当前模块中配置关系，聚合可以感知到参与聚合的模块有哪些
		继承是在子模块中配置关系，父模块无法感知哪些子模块继承了自己


4.属性
	定义属性
	在pom文件里面定义标签<properties>然后直接定义属性即可用<属性>值</属性>，直接给版本号用属性代替，方便统一管理，取值用${属性名}即可

	资源引用属性
	如果要把数据库的properties文件的属性也放到pom里面统一管理
	步骤：①先把jdbc.url等属性定义到pom里面
		  ②配置文件中引用属性，就是把值换成取值符合那种形式${}
		  ③然后开启资源文件目录加载属性的过滤器
		  	<build>
				<resources>
					<!--设置资源目录-->
					<resource>
						<directory>${project.basedir}/src/main/resources</directory>
						<!--设置能够解析${}，默认是false -->
						<filtering>true</filtering>
					</resource>
				</resources>
			</build>
		注意：${project.basedir}: 当前项目所在目录,子项目继承了父项目，相当于所有的子项目都添加了资源目录的过滤
	
		此时打包发现出现错误，需要web.xml文件才能打包
		解决方法一：直接加一个web.xml
		解决方法二：在bulld里面加一个插件属性重新编辑maven-war-plugin，并且给出配置属性<configuration>定义<failOnMissingWebXml>为false
	
		maven还有他内置的系统属性，${project.basedir}就属于内置的


5.多环境开发
	定义多坏境<profiles>，<profile>定义各个环境，并给出id即为名字然后可以设置属性值，同时可以用<activation>里面设置默认环境，不过一般还是在idea右边的maven里面点击m图标，输入mvn install -P id进行用该环境打包


6.跳过测试
	方法一点击maven，里面有个蓝色的M图标，点击即可跳过所有测试
	方法二还是插件的方法，编辑maven-surefire-plugin设置配置属性即可
	方法三指令mvn package -D skipTests


7.私服
	仓库分三大类
	①宿主仓库hosted
		作用：保存无法从中央仓库获取的资源
		自主研发，第三方非开源项目,比如Oracle,因为是付费产品，所以中央仓库没有，关联上传操作
	②代理仓库proxy
		作用：代理远程仓库，通过nexus访问其他公共仓库，例如中央仓库，关联下载操作
	③仓库组group
		作用：将若干个仓库组成一个群组，简化配置，仓库组不能保存资源，属于设计型仓库

	资源上传与下载
	在工程中配置私服的位置
	①<distributionManagement>该标签配置私服
	②然后配置<repository>表示正式版本,<snapshotRepository>表示快照版本
	③然后配置对应的id和地址即可
	④然后用deploy指令









### 五、Springboot

概述：SpringBoot 是由 Pivotal 团队提供的全新框架，其设计目的是用来简化 Spring 应用的初始搭建以及开发过程。
1.入门案例
方法一：用官方或者阿里云
	①创建一个新的工程，选择Spring Initializr进行创建，选择服务地址https://start.aliyun.com/，然后选择java8进行创建，选住Web里面的Spring Web即可
	注意：默认的服务地址为https://start.spring.io/
	②不用创建Web3.0那个config，也不用Springmvc那个config，直接创建控制层，给出访问资源路径
	注意：@GetMapping注解可以声明该访问是get请求，@RestController表示这是一个rest风格的访问路径
	③先运行Application类，运行tomcat等
	④直接访问资源即可
	注意：可以直接在pom文件里面修改botot版本
方法二：在spring官方创建
	也可也在Spring官方直接创建一个boot工程，然后会下载到本地，用ide导入模块即可
方法三：手工方法
	①创建一个干净的maven的工程，然后导入相应boot工程的依赖包，然后创建引导类和引导注解

	小知识：每次创建一个boot工程会有许多暂时用不到的，可以删除也可以在idea设置中的editor中的File types添加进去即可。

2.快速启动
	直接打包，然后用cmd，输入java -jar 名字即可运行
	注意：jar支持命令行启动需要依赖maven插件支持，请确认打包时是否具有SpringBoot对应的maven插件


3.起步依赖
	SpringBoot优点：
		①自动配置。这个是用来解决 Spring 程序配置繁琐的问题
		②起步依赖。这个是用来解决 Spring 程序依赖设置繁琐的问题
		③辅助功能（内置服务器,...）。我们在启动 SpringBoot 程序时既没有使用本地的 tomcat 也没有使用 tomcat 插件，而是使用 SpringBoot 内置的服务器。

	starter：
		起步依赖定义了当前项目使用的所有项目坐标，以达到减少依赖配置的目的，起步依赖通过starter来识别
	parent：
		所有SpringBott项目要继承的项目，定义了许多坐标版本号(依赖管理，而非依赖)，达到减少依赖冲突的目的
	实际开发：
		使用任意坐标的时候，只写G和A，版本由SprignBoot提供
		如发生坐标错误，在指定version(小心版本冲突)
		Boot项目一般都是jar包
	
	在boot项目开发时，如果某个不想用，直接用排除依赖，如把tomcat服务器换成jetty服务器，只需要现在starter-web里面添加排除依赖，然后另外在外面在添加jetty依赖即可


4.配置文件
	Boot提供了3种方式：
		①直接在resources下的application.properties文件进行修改server.port=8080端口即可
		②在resources下创建application.yml文件，然后根据格式修改即可
			server:
				port: 81	#切记后面有空格
		③同样的位置创键yaml文件，和第二种方法一样
	注意：如果没有出提示，那么在项目结构中点进去Facets中，点击Spring那个，然后点击大炮那个图标，把新建的配置文件填进去即可
	logging：level：root：info 是设置日志的等级

	实际开发：
	用的最多的是yml文件配置形式，当3个都存在的时候，那么properties为主启动文件，他们的优先性为properties>yml>yaml


​	
5.yaml格式
​	概述：YAML（YAML Ain't Markup Language），一种数据序列化格式
​	扩展形式：yml(主流)，yaml等
​	内容如下：enterprise:
​				name: itcast
​				age: 16
​				tel: 4006184000
​			subject：
​			 - 1
​			 - 2		#用-表示的是一个数组
​	优点：①容易阅读
​		②yaml 类型的配置文件比 xml 类型的配置文件更容易阅读，结构更加清晰
​		③容易与脚本语言交互
​		④以数据为核心，重数据轻格式
​		yaml 更注重数据，而 xml 更注重格式

	语法规则：
		①大小写敏感
		②属性层级关系使用多行描述，每行结尾使用冒号结束
		③使用缩进表示层级关系，同层级左侧对齐，只允许使用空格（不允许使用Tab键）
		④空格的个数并不重要，只要保证同层级的左侧对齐即可。
		⑤属性值前面添加空格（属性名与属性值之间使用冒号+空格作为分隔）
		⑥# 表示注释
	核心规则：数据前面要加空格与冒号隔开
	
	yaml读取数据方式
	方式一：在类中定义一个私有变量，并表上注解@Value("${名字}")取即可，层级之间用.即可，数组用[]取
	方法二：直接全部取走，用Environment environment取走，并表上注解@Autowired，然后用该API的属性来取即可
	方法三：在domin层创建对应的类，给出get set toSring方法，然后加上注解@Component受Spring控制，然后再加注解@ConfigurationProperties(prefix = "名字")，意思是读取哪一个写哪一个名字，然后在表现层想要使用直接创建对象自动装配即可，这个最重要
	注意：出现自定义对象封装数据警告解决方法，配置依赖
		<groupId>org.springframework.boot</groupId>
		<artifactId>spring-boot-configuration-processor</artifactId>
		<optional>true</optional>





6.多坏境开发配置
	在yml配置多环境
	①首先在yml配置文件中将各个环境之间用---隔开
	②然后在坏境里面给出Spring：profiles：名字，分别对应dev，pro，test
	③在最上面指定使用哪个环境，spring：profiles：active：名字即可

	在properties(了解即可)
	①创建主配置application.properties，然后spring.profiles.active=dev，指明用哪个
	②创建其他环境配置如application-dev.properties
	
	命令行启动多坏境
	①先去setting里面给encoding里面全部设置成utf8
	②打包前，先进行clean，然后打包
	③ java –jar xxx.jar –-spring.profiles.active=test即可
	也能临时改端口在后面--server.port=88等


	maven与boot多坏境兼容问题
	一般是让boot遵从maven来
	①首先在maven多环境中给出profile下的properties下的profile.active标签给出用哪个环境
	②编辑maven-resources-plugin插件把范围改为全局
		给出<configuration>标签修改<useDefaulDelimiters>为true，同时给出encoding为utf-8即可
	③在yml文件里面active修改为${profile.active}


	当测试临时需要设置的属性太多的时候，可以在resources下直接创建一个config目录，创建一个yml文件，给出所需的临时属性
	在这里优先级config目录>jar包同一层级的yml文件>大于工程内部的配置
	结论：类路径下的config下的配置文件优先于类路径下的配置文件。
		 file：config下的配置文件优先于类路径下的配置文件。
	
	注意：boot2.5版本有一个bug，就是config必须要有子目录，给他一个就好



7.Boot整合JUnit
	①创建boot工程，不需要添加web，因为这里用不到
	②直接在测试类里面先写上注解(SpringBoot)，然后注入要测试的，然后写方法即可

	注意：在这里以前的配置其实现在的引导类已经帮我们全部做好了，所以他的扫描范围是com.yixin及其以下所有子包，因此test里面也需要在com.yixin下
	如果不满足这个要求的话，就需要在使用 @SpringBootTest 注解时，使用classes属性指定引导类的字节码对象。如@SpringBootTest(classes = Springboot07TestApplication.class)
	
	Spring和SpringMVC不用整合，因为不存在


8.Boot整合Mybatis
	①创建工程选上MyBatis Framework和MySQL Driver
	②创建对应的domin和dao数据层
	③在application.yml配置数据库的信息
		spring:
			datasource:
			driver-class-name: com.mysql.cj.jdbc.Driver
			url: jdbc:mysql://localhost:3306/ssm_db
			username: root
			password: root
	④这个时候运行会报错，Spring扫描不上BookDao，因为他做了代理，需要给他做个注解@Mapper
	⑤如果要用druid数据源，先加依赖，然后在配置加type定义数据源

	注意：如果选用boot2.4.2下面的低版本，需要设时区，在url后面加上?serverTimezone=UTC即可



### 六、MyBatisPlus

1.入门案例
	①创建boot工程，把MySQL server和MyBatisPlus Framework加进去
	②以前在dao层会写注解标注查询，现在直接在类上写注解@Mapper，并且继承与BaseMapper<User>
	③配置yml，选择druid数据源，给出时区等


2.简介
	概述：MybatisPlus(简称MP)是基于MyBatis框架基础上开发的增强型工具，旨在简化开发、提供效率。
	优点：
		①无侵入：只做增强不做改变，不会对现有工程产生影响
		②强大的 CRUD 操作：内置通用 ③Mapper，少量配置即可实现单表CRUD 操作
		④支持 Lambda：编写查询条件无需担心字段写错
		⑤支持主键自动生成
		⑥内置分页插件


	MP配置
	在MP中有大量配置，其中有一部分是原生的Mybatis的配置，一部分是MP的
	
	基本配置
		①configLocation
			MyBatis配置文件位置放在资源目录下，将其路径配置到boot中的application中。
			mybatis-plus.config-location = classpath:mybatis-config.xml
		②mapperLocations
			在资源目录下创建mapper文件，里面放xml等配置文件，并在boot配置中写上mybatis-plus.mapper-locations = classpath*:mybatis/*.xml
		③typeAliasesPackage
			MyBaits 别名包扫描路径，通过该属性可以给包中的类注册别名，注册后在 Mapper 对应的 XML 文件中可以直接使用类名，而不用使用全限定的类名（即 XML 中调用的时候不用包含包名）。
			mybatis-plus.type-aliases-package = cn.itcast.mp.pojo
	
	进阶配置
	本部分（Configuration）的配置大都为 MyBatis 原生支持的配置，这意味着您可以通过 MyBatis XML 配置文件的形式进行配置。
	①mapUnderscoreToCamelCase
		概述：是否开启自动驼峰命名规则（camel case）映射，即从经典数据库列名 A_COLUMN（下划线命名） 到经典 Java 属性名 aColumn（驼峰命名） 的类似映射。
		类型： boolean
		默认值： true
		注意：
			此属性在 MyBatis 中原默认值为 false，在 MyBatis-Plus 中，此属性也将用于生成最终的 SQL 的 select body
			如果您的数据库命名符合规则无需使用 @TableField 注解指定数据库字段名
		代码：#关闭自动驼峰映射，该参数不能和mybatis-plus.config-location同时存在
		mybatis-plus.configuration.map-underscore-to-camel-case=false
	②cacheEnabled
		类型： boolean
		默认值： true
		全局地开启或关闭配置文件中的所有映射器已经配置的任何缓存，默认为 true。
		mybatis-plus.configuration.cache-enabled=false
	
	DB 策略配置
	①idType
		类型： com.baomidou.mybatisplus.annotation.IdType
		默认值： ID_WORKER
		全局默认主键类型，设置后，即可省略实体对象中的@TableId(type = IdType.AUTO)配置。
		mybatis-plus.global-config.db-config.id-type=auto
	②tablePrefix
		类型： String
		默认值： null
		表名前缀，全局配置后可省略@TableName()配置。
		mybatis-plus.global-config.db-config.table-prefix=tb_


	条件构造器
	allEq方法
	使用：还是先创造一个QueryWrapper集合对象.然后创建一个map集合，put进几个属性，然后使用allEq方法设置条件，调用selectList方法查询即可
		①allEq(Map<R, V> params)
		②allEq(Map<R, V> params, boolean null2IsNull)
		③allEq(boolean condition, Map<R, V> params, boolean null2IsNull)
	参数说明：
		个别参数说明: params : key 为数据库字段名, value 为字段值 null2IsNull : 为 true 则在 map 的 value 为null 时调用 isNull 方法,为 false 时则忽略 value 为 null 的
		例1: allEq({id:1,name:"老王",age:null}) ---> id = 1 and name = '老王' and age is null
		例2: allEq({id:1,name:"老王",age:null}, false) ---> id = 1 and name = '老王'
	进阶方法：
		allEq(BiPredicate<R, V> filter, Map<R, V> params)
		allEq(BiPredicate<R, V> filter, Map<R, V> params, boolean null2IsNull)
		allEq(boolean condition, BiPredicate<R, V> filter, Map<R, V> params, boolean null2IsNull)
	个别参数说明: filter : 过滤函数,是否允许字段传入比对条件中 params 与 null2IsNull : 同上
		例1: allEq((k,v) -> k.indexOf("a") > 0, {id:1,name:"老王",age:null}) ---> name = '老王' and age is null
		例2: allEq((k,v) -> k.indexOf("a") > 0, {id:1,name:"老王",age:null}, false) ---> name = '老王'



3.标准数据层开发
	继承BaseMapper以后，它里面提供有增删改查方法，用的时候直接用对象调用即可。

	分页查询：IPage<T> selectPage(IPage<T> page)
	使用步骤：
		①先配置拦截增强分页器，创建config层，创建配置类
			@Configuration
			public class MybatisPlusConfig {
				@Bean
				public MybatisPlusInterceptor mybatisPlusInterceptor(){
					//1 创建MybatisPlusInterceptor拦截器对象
					MybatisPlusInterceptor mpInterceptor=new MybatisPlusInterceptor();
					//2 添加分页拦截器
					mpInterceptor.addInnerInterceptor(new PaginationInnerInterceptor());
					return mpInterceptor;
				}
			}
	
		②调用方法传入参数进行封装并返回
			先创建IPage分页对象，设置分页参数，1为当前页码，3为每页显示的记录数
			IPage<User> page=new Page<>(1,3);
			执行分页查询
			userDao.selectPage(page,null);
		③然后内容都存到了page里面，调用page方法获取各个信息
			page.getRecords()是当前页数据
		注意：可以在yml开启MP日志输出到控制台
			mybatis-plus:
				configuration:
					log-impl: org.apache.ibatis.logging.stdout.StdOutImpl 
	
	条件查询：IPage<T> selectPage(Wrapper<T> queryWrapper)
	Wrapper对象是专门用来写条件的
	方法一：按条件查询
		①先创建Wrapper对象
			QueryWrapper qw = new QueryWrapper();
		②lt: 小于(<)，qw.lt("age",18);表示age小于18的信息
		③调用查询userDao.selectList(qw);输出即可
	方法二：用LambdaQueryWrapper查询
		①创建对象
			LambdaQueryWrapper<User> lqw = new LambdaQueryWrapper<User>();
		②调用条件方法返回结果
			lqw.lt(User::getAge, 10);
		③调用查询方法，将wrapper对象的结果放入参数里面，输出即可
	注意：lt: 小于(<)，gt：大于(>)，多个条件，可以依次写即可，也可也通过.依次连接，这些都是并关系，想写或者在第一个条件写完加上.or()后面再继续链式连接
	
	条件查询null时判断
	如lqw.lt(null!=uq.getAge2(),User::getAge, uq.getAge2());他表示如果前面哪个值不为空，连接两个条件即并
	
	条件查询某些字段
	如果是方法一字符串那种，格式qw.select("id","name");然后用selectlist查询输出即可
	如果是方法二lamda式，格式lqw.select(User::getId,User::getName);然后同上
	当select("count(*)")时，不能用selectlist，要用selectMaps，返回一个Map集合，输出即可
	
	分组
	lqw.groupBy("字段")
	
	查询条件设置
	lt：小于，le：小于等于
	gt：大于，ge：大于等于
	eq：等于
	between：之间，前面是小于，后面是大于
	like：含有该字母即符合，likeRight,likeleft
	groupBy：按照字段分组
	
	增删改查CRUD
	直接调用mapper类的继承自BaseMapper的方法即可
	更新：①根据id更新直接调用方法即可
		  ②条件更新
		  	方法一：用QueryWrapper的对象调用方法eq(String column，String value)根据字段更新
		  	方法二：用UpdateWrapper的对象调用方法set(String column,String value)后面可以继续接set，set表示要更新的字段，eq表示根据这个字段更新，用这个调的时候update写(null,wrapper)即可
	删除：①根据id直接删除即可deleteById
		  ②调用deleteByMap(map)方法，先创建一个map集合，然后put几个值，直接把map写进去即可，他们关系是and
		  ③调用delete(wrapper)方法
		  	用法一：先创建QueryWrapper集合对象，根据eq方法的条件进行删除，中间关系也是and
		  	用法二：直接创建一个实体类对象，然后修改对象的属性，然后将实体类对象传入到QueryWrapper集合的对象里面，再把QueryWrapper传进去delete方法即可，这个推荐使用
		  ④批量删除，用deleteBatchIds(Arrays.asList(直接写要删除的id即可)) 
	查询：①根据id查询用selectById()查询即可
		  ②批量查询selectBatchIds(Araays.asList(写id))，返回的是List集合，然后遍历一下
		  ③selectOne()方法，先创建QueryWrapper集合对象，然后用eq方法设置条件，然后调用selectOne(wrapper)返回一个对象，数据不存在返回null，数据有多条会抛出异常
		  ④selectCount()方法，先创建QueryWrapper集合对象，然后用gt设置大于的条件，然后把wrapper写入方法返回一个整数
		  ⑤selectList()方法返回List集合，先创建QueryWrapper集合对象，然后用like()设置条件，调入方法，遍历输出
		  ⑥selectPage()分页查询，先导入插件，在config层写入配置类，标上注解@Configuration表示配置类，然后写返回值为PaginationInterceptor的方法并直接返回new对象，并配置bean注解，然后在mapper层写方法，先创建Page集合对象(构造方法直接设置，查询当前页和每一页的页数)和QueryWrapper集合对象，同时设置查询条件，再调用selectPage(page,wrapper)方法他返回IPage集合,其中Ipage里面还有getTotal，getPages所有页数,getCurrent当前页等方法，还有getRecords()返回所有记录的list集合


	模糊查询
	使用都是调用QueryWrapper的对象使用
	like
		LIKE '%值%'
		例: like("name", "王") ---> name like '%王%'
	notLike
		NOT LIKE '%值%'
		例: notLike("name", "王") ---> name not like '%王%'
	likeLeft
		LIKE '%值'
		例: likeLeft("name", "王") ---> name like '%王'
	likeRight
		LIKE '值%'
		例: likeRight("name", "王") ---> name like '王%'
	
	排序
	orderBy
		排序：ORDER BY 字段, ...
		例: orderBy(true, true, "id", "name") ---> order by id ASC,name ASC
	orderByAsc
		排序：ORDER BY 字段, ... ASC
		例: orderByAsc("id", "name") ---> order by id ASC,name ASC
	orderByDesc
		排序：ORDER BY 字段, ... DESC
		例: orderByDesc("id", "name") ---> order by id DESC,name DESC
	
	逻辑查询
	or
		拼接 OR
		主动调用 or 表示紧接着下一个方法不是用 and 连接!(不调用 or 则默认为使用 and 连接)
	and
		AND 嵌套
		例: and(i -> i.eq("name", "李白").ne("status", "活着")) ---> and (name = '李白' and status <> '活着')
	
	select字段
	在MP查询中，默认查询所有的字段，如果有需要也可以通过select方法进行指定字段



	小知识：Lombok可以帮助快速开发实体类
	依赖坐标：
		<groupId>org.projectlombok</groupId>
		<artifactId>lombok</artifactId>
		<version>1.18.12</version>
		<scope>provided</scope>
	boot不用给出后两行
	
	Lombok常见的注解有:
		@Setter:为模型类的属性提供setter方法
		@Getter:为模型类的属性提供getter方法
		@ToString:为模型类的属性提供toString方法
		@EqualsAndHashCode:为模型类的属性提供equals和hashcode方法
		@Data:是个组合注解，包含上面的注解的功能
		@NoArgsConstructor:提供一个无参构造函数
		@AllArgsConstructor:提供一个包含所有参数的构造函数


4.字段映射与表明映射
	当出现数据库的字段和后端实体类名字不一样时
	直接在私人变量上面加注解@TableField(value="数据库字段")

	当编码中添加了数据库中没有的
	加注解@TableField的exist属性为false表示数据库没有
	
	当密码等不让他展示
	给注解设置属性select=false，表示不查询他
	
	当数据库名和实体类不一样
	加上注解@TableName("数据库名")



5.id生成策略控制
	会出现不同情况的id自增
	在id上面加上注解@TableId(IdType.AUTO)
	相关属性 value(默认)：设置数据库表主键名称
	type:设置主键属性的生成策略，值查照IdType的枚举值

	结论：
		①NONE: 不设置id生成策略，MP不自动生成，约等于INPUT,所以这两种方式都需要用户手动设置，但是手动设置第一个问题是容易出现相同的ID造成主键冲突，为了保证主键不冲突就需要做很
		多判定，实现起来比较复杂
		②AUTO:数据库ID自增,这种策略适合在数据库服务器只有1台的情况下使用,不可作为分布式ID使用
		③ASSIGN_UUID:可以在分布式的情况下使用，而且能够保证唯一，但是生成的主键是32位的字符串，长度过长占用空间而且还不能排序，查询性能也慢
		④ASSIGN_ID:可以在分布式的情况下使用，生成的是Long类型的数字，可以排序性能也高，但是
		生成的策略和服务器时间有关，如果修改了系统时间就有可能导致出现重复主键
		⑤Input：用户自己必须上传id
	注意：也可也在yml文件直接设置所有的id形式，打个id-type就出来了


6.多记录操作
	他用BaseMapper里面的deletBatchIds(List<T> list)
	把需要删的放到这个集合就行
	selectBatchIds也是



7.逻辑删除
	概述：为数据设置是否可用状态字段，删除时设置状态字段为不可用状态，数据保留在数据库中，执行的是update操作
	执行步骤：①修改数据库表添加deleted列，给出默认值
			②实体类添加属性，并给上注解@TableLogic(value="默认的值",delval="删除数据的值")
	注意：设置完以后也会影响select，他会不查询哪些进行了逻辑删除的
	他也可以在yml即boot的配置文件里面全局配置，如：
		# 逻辑已删除值(默认为 1)
		mybatis-plus.global-config.db-config.logic-delete-value=1
		# 逻辑未删除值(默认为 0)
		mybatis-plus.global-config.db-config.logic-not-delete-value=0


8.乐观锁
	他是针对比如说秒杀等，解决这种同步问题的
	简单来说，乐观锁主要解决的问题是当要更新一条记录的时候，希望这条记录没有被别人更新。
	这种是针对小型的，超过2000的不行
	步骤：
		①数据库表添加version列，并给出默认值1
		②在实体类中添加对应的属性，并加上注解@Version
		③添加乐观锁的拦截器
			@Configuration
			public class MpConfig {
				@Bean
				public MybatisPlusInterceptor mpInterceptor() {
					//1.定义Mp拦截器
					MybatisPlusInterceptor mpInterceptor = new MybatisPlusInterceptor();
					//2.添加乐观锁拦截器
					mpInterceptor.addInnerInterceptor(new OptimisticLockerInnerInterceptor());
					return mpInterceptor;
				}
			}
		④执行更新操作，并且一定要带上version
	注意：他是实现其实就是每一次使用都对version进行了加1，如果下一个操作的时候version与现在的version不等就无法进行
	说明:
		①支持的数据类型只有:int,Integer,long,Long,Date,Timestamp,LocalDateTime
		②整数类型下 newVersion = oldVersion + 1
		③newVersion 会回写到 entity 中
		④仅支持 updateById(id) 与 update(entity, wrapper) 方法
		⑤在 update(entity, wrapper) 方法下, wrapper 不能复用!!!


9.插件
	MyBatis 允许你在已映射语句执行过程中的某一点进行拦截调用。
	默认情况下，MyBatis 允许使用插件来拦截的方法调用包括：
		1. Executor (update, query, flushStatements, commit, rollback, getTransaction, close, isClosed)
		2. ParameterHandler (getParameterObject, setParameters)
		3. ResultSetHandler (handleResultSets, handleOutputParameters)
		4. StatementHandler (prepare, parameterize, batch, update, query)
	我们看到了可以拦截Executor接口的部分方法，比如update，query，commit，rollback等方法，还有其他接口的一些方法等。
	总体概括为：
		1. 拦截执行器的方法
		2. 拦截参数的处理
		3. 拦截结果集的处理
		4. 拦截Sql语法构建的处理


	执行分析插件
	在MP中提供了对SQL执行的分析的插件，可用作阻断全表更新、删除的操作，注意：该插件仅适用于开发环境，不适用于生产环境。
	SpringBoot配置：
		@Bean
		public SqlExplainInterceptor sqlExplainInterceptor(){
			SqlExplainInterceptor sqlExplainInterceptor = new SqlExplainInterceptor();
			List<ISqlParser> sqlParserList = new ArrayList<>();
			// 攻击 SQL 阻断解析器、加入解析链
			sqlParserList.add(new BlockAttackSqlParser());
			sqlExplainInterceptor.setSqlParserList(sqlParserList);
			return sqlExplainInterceptor;
		}
	结果：当执行全表更新时，会抛出异常，这样有效防止了一些误操作。


10.Sql注入器
	前言：我们已经知道，在MP中，通过AbstractSqlInjector将BaseMapper中的方法注入到了Mybatis容器，这样这些方法才可以正常执行。
	需求：当我们需要扩充BaseMapper中的方法，并且让后续所有的mapper都拥有这个方法的时候使用这个
	步骤：
		①编写MyBaseMapper，让他继承自BaseMapper，并且让原来的mapper继承自MyBaseMapper即可
			public interface MyBaseMapper<T> extends BaseMapper<T> {
				List<T> findAll();
			}
		其他的mapper
			public interface UserMapper extends MyBaseMapper<User> {
				User findById(Long id);
			}
		②创建一个injector的包，创建你要注入的方法的类和MySqlInjector类
		注意：如果直接继承AbstractSqlInjector的话，原有的BaseMapper中的方法将失效，所以我们选择继承DefaultSqlInjector进行扩展。
			public class MySqlInjector extends DefaultSqlInjector {
				@Override
				public List<AbstractMethod> getMethodList() {
					List<AbstractMethod> methodList = super.getMethodList();
					methodList.add(new FindAll());
					// 再扩充自定义的方法
					list.add(new FindAll());
					return methodList;
					}
				}
		③编写FindAll方法
			public class FindAll extends AbstractMethod {
				@Override
				public MappedStatement injectMappedStatement(Class<?> mapperClass, Class<?>
				modelClass, TableInfo tableInfo) {
					String sqlMethod = "findAll";
					String sql = "select * from " + tableInfo.getTableName();
					SqlSource sqlSource = languageDriver.createSqlSource(configuration, sql,
				modelClass);
					return this.addSelectMappedStatement(mapperClass, sqlMethod, sqlSource,
				modelClass, tableInfo);
					}
				}
		④注册到Spring容器，在MPconfig中
			@Bean
			public MySqlInjector mySqlInjector(){
				return new MySqlInjector();
			}


11.自动填充功能
	需求：有些时候我们可能会有这样的需求，插入或者更新数据时，希望有些字段可以自动填充数据，比如密码、version等。在MP中提供了这样的功能，可以实现自动填充。
	步骤：
		①添加@TableField注解，并设置fill=FieldFill.值
		②创建handler包，并创建MyMetaObjectHandler类
			@Component
			public class MyMetaObjectHandler implements MetaObjectHandler {
				@Override
				public void insertFill(MetaObject metaObject) {
				//先获取到password的值，在进行判断，如果为空，就进行填充，如果不为空，就不做处理
					Object password = getFieldValByName("password", metaObject);
					if(null == password){
					//字段为空，可以进行填充
					setFieldValByName("password", "123456", metaObject);
					}
				}
				@Override
				public void updateFill(MetaObject metaObject) {
				}
			}


12.通用枚举







13.ActiveRecord
	概述：
		ActiveRecord也属于ORM（对象关系映射）层，由Rails最早提出，遵循标准的ORM模型：表映射到记录，记录映射到对象，字段映射到对象属性。配合遵循的命名和配置惯例，能够很大程度的快速实现模型的操作，而且简洁易懂。
	ActiveRecord的主要思想是：
		①每一个数据库表对应创建一个类，类的每一个对象实例对应于数据库中表的一行记录；通常表的每个字段在类中都有相应的Field；
		②ActiveRecord同时负责把自己持久化，在ActiveRecord中封装了对数据库的访问，即CURD;；
		③ActiveRecord是一种领域模型(Domain Model)，封装了部分业务逻辑；

	上手
	在MP中，开启AR非常简单，只需要将实体对象继承Model，泛型为实体类即可。
	根据主键查询，直接在测试类(RunWith和SpringBootTest注解即可测试)中，直接创建一个实体对象，设置setId，直接调用selectById方法即可



14.回忆mybatis
	步骤：
		①创建数据库表和创建maven工程
		②导入坐标依赖，分别有druid连接池，lombok，junit，slf4j-log4j12，mysql-connector-java，mybatis-plus依赖和maven-compiler-plugin插件
		③设置log4j的日志配置文件放在资源目录下
		④编写mybatis-config.xml配置文件
			<configuration>
				<environments default="development">
				<environment id="development">
				<transactionManager type="JDBC"/>
				<dataSource type="POOLED">
				<property name="driver" value="com.mysql.jdbc.Driver"/>
				<property name="url" value="jdbc:mysql://127.0.0.1:3306/mp?
				useUnicode=true&amp;characterEncoding=utf8&amp;autoReconnect=true&amp;allowMultiQuerie
				s=true&amp;useSSL=false"/>
				<property name="username" value="root"/>
				<property name="password" value="root"/>
				</dataSource>
				</environment>
				</environments>
				<mappers>
				<mapper resource="UserMapper.xml"/>
				</mappers>
			</configuration>
		⑤编写User实体对象（这里使用lombok进行了进化bean操作）
		⑥编写UserMapper接口
		⑦编写UserMapper.xml文件
			<mapper namespace="cn.itcast.mp.simple.mapper.UserMapper">
				<select id="findAll" resultType="cn.itcast.mp.simple.pojo.User">
				select * from tb_user
				</select>
			</mapper>

		⑧编写test测试用例
			public class TestMybatis {
				@Test
				public void testUserList() throws Exception{
					String resource = "mybatis-config.xml";
					InputStream inputStream = Resources.getResourceAsStream(resource);
					SqlSessionFactory sqlSessionFactory = new
				SqlSessionFactoryBuilder().build(inputStream);
					SqlSession sqlSession = sqlSessionFactory.openSession();
					UserMapper userMapper = sqlSession.getMapper(UserMapper.class);
					List<User> list = userMapper.findAll();
					for (User user : list) {
						System.out.println(user);
						}
					}
				}





15.回忆mybatis和mp整合
	步骤：
		①将UserMapper继承自BaseMapper<实体类>
		②使用MP中的MybatisSqlSessionFactoryBuilder进程构建
		③在User对象中添加@TableName，指定数据库表名


16.回忆Spring + Mybatis + MP
	步骤：
		①导入坐标spring-webmvc，spring-jdbc，spring-test
		②在资源目录编写jdbc.properties
		③编写spring配置文件applicationContext.xml
		④编写User对象以及UserMapper接口
		⑤编写测试用例

17.回忆SpringBoot + Mybatis + MP
	步骤：
		①导入boot等的相应坐标
		②开启log4j日志，编写log4j.properties
		③boot的配置文件application.properties
		④编写pojo和mapper
		⑤编写启动类，然后进行测试




18.代码生成器
	步骤：①首先导入坐标
		<!--velocity模板引擎-->
		<dependency>
			<groupId>org.apache.velocity</groupId>
			<artifactId>velocity-engine-core</artifactId>
			<version>2.3</version>
		</dependency>
		<!--代码生成器-->
		<dependency>
			<groupId>com.baomidou</groupId>
			<artifactId>mybatis-plus-generator</artifactId>
			<version>3.4.1</version>
		</dependency>


	代码生成器类
	package com.itheima;
	
	import com.baomidou.mybatisplus.annotation.IdType;
	import com.baomidou.mybatisplus.generator.AutoGenerator;
	import com.baomidou.mybatisplus.generator.config.DataSourceConfig;
	import com.baomidou.mybatisplus.generator.config.GlobalConfig;
	import com.baomidou.mybatisplus.generator.config.PackageConfig;
	import com.baomidou.mybatisplus.generator.config.StrategyConfig;
	
	public class CodeGenerator {
	    public static void main(String[] args) {
	        //1.获取代码生成器的对象
	        AutoGenerator autoGenerator = new AutoGenerator();
	
	        //设置数据库相关配置
	        DataSourceConfig dataSource = new DataSourceConfig();
	        dataSource.setDriverName("com.mysql.cj.jdbc.Driver");
	        dataSource.setUrl("jdbc:mysql://localhost:3306/mybatisplus_db?serverTimezone=UTC");
	        dataSource.setUsername("root");
	        dataSource.setPassword("root");
	        autoGenerator.setDataSource(dataSource);
	
	        //设置全局配置
	        GlobalConfig globalConfig = new GlobalConfig();
	        globalConfig.setOutputDir(System.getProperty("user.dir")+"/mybatisplus_04_generator/src/main/java");    //设置代码生成位置
	        globalConfig.setOpen(false);    //设置生成完毕后是否打开生成代码所在的目录
	        globalConfig.setAuthor("黑马程序员");    //设置作者
	        globalConfig.setFileOverride(true);     //设置是否覆盖原始生成的文件
	        globalConfig.setMapperName("%sDao");    //设置数据层接口名，%s为占位符，指代模块名称
	        globalConfig.setIdType(IdType.ASSIGN_ID);   //设置Id生成策略
	        autoGenerator.setGlobalConfig(globalConfig);
	
	        //设置包名相关配置
	        PackageConfig packageInfo = new PackageConfig();
	        packageInfo.setParent("com.aaa");   //设置生成的包名，与代码所在位置不冲突，二者叠加组成完整路径
	        packageInfo.setEntity("domain");    //设置实体类包名
	        packageInfo.setMapper("dao");   //设置数据层包名
	        autoGenerator.setPackageInfo(packageInfo);
	
	        //策略设置
	        StrategyConfig strategyConfig = new StrategyConfig();
	        strategyConfig.setInclude("tbl_user");  //设置当前参与生成的表名，参数为可变参数
	        strategyConfig.setTablePrefix("tbl_");  //设置数据库表的前缀名称，模块名 = 数据库表名 - 前缀名  例如： User = tbl_user - tbl_
	        strategyConfig.setRestControllerStyle(true);    //设置是否启用Rest风格
	        strategyConfig.setVersionFieldName("version");  //设置乐观锁字段名
	        strategyConfig.setLogicDeleteFieldName("deleted");  //设置逻辑删除字段名
	        strategyConfig.setEntityLombokModel(true);  //设置是否启用lombok
	        autoGenerator.setStrategy(strategyConfig);
	        //2.执行生成操作
	        autoGenerator.execute();
	    }
	}



19.MybatisX 快速开发插件
	功能：①Java 与 XML 调回跳转
		 ②Mapper 方法自动生成 XML
