---
title: spring知识点
date: 2022-10-12 21:35:01
cover: ./img/spring.png
tags: Java
categories: java
description: 经典Spring！
---



## Spring

#### 1.概述：

​	Spring其实指的是Spring Framework。

#### 2.系统架构：

​	(1)核心层
​		Core Container:核心容器，这个模块是Spring最核心的模块，其他的都需要依赖该模块
​	(2)AOP层
​		AOP:面向切面编程，它依赖核心层容器，目的是在不改变原有代码的前提下对其进行功能增强
​		Aspects:AOP是思想,Aspects是对AOP思想的具体实现
​	(3)数据层
​		Data Access:数据访问，Spring全家桶中有对数据访问的具体实现技术
​		Data Integration:数据集成，Spring支持整合其他的数据层解决方案，比如Mybatis
​		Transactions:事务，Spring中事务管理是Spring AOP的一个具体实现，也是后期学习的
​		重点内容
​	(4)Web层
​		这一层的内容将在SpringMVC框架具体学习
​	(5)Test层
​		Spring主要整合了Junit来完成单元测试和集成测试

#### 3.核心概念

​	代码书写：使用对象时，不要主动去new传生对象，转换由外部提供对象，要追求高内聚低耦合

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

#### 4.IoC入门案例

​	①导入spring坐标：<dependency>
​				      <groupId>org.springframework</groupId>
​				      <artifactId>spring-context</artifactId>
​				      <version>5.2.10.RELEASE</version>
​				   </dependency>
​	②在resoures下创建xml配置文件，进行配置bean
​		<!--bean标签标示配置bean id属性标示给bean起名字 class属性表示给bean定义类型-->
​		<bean id="bookDao" class="com.itheima.dao.impl.BookDaoImpl"/>
​		<bean id="bookService" class="com.itheima.service.impl.BookServiceImpl"/>
​	③在service层创建类，获取IoC容器，获取bean
​		获取IOC容器  ApplicationContext ctx = new ClassPathXmlApplicationContext("applicationContext.xml");
​		也可以用绝对路径获取容器	        ApplicationContext ctx = new FileSystemXmlApplicationContext("D:/applicationContext.xml");

	④调用方法
		BookService bookService = (BookService) ctx.getBean("bookService");
		bookService.save();

#### 5.DI入门案例

​	基于IoC案例上进行改善
​	①在BookServiceImpl类中，删除业务层中使用new的方式创建的dao对象
​	②为(本来new的那个对象)属性提供set+名字方法
​	③修改配置完成注入
​		<bean id="bookDao" class="com.itheima.dao.impl.BookDaoImpl"/>
​		<bean id="bookService" class="com.itheima.service.impl.BookServiceImpl">
​			<!--配置server与dao的关系-->
​			<!--property标签表示配置当前bean的属性 name属性表示配置哪一个具体的属性 ref属性表示参照哪一个bean-->
​			<property name="bookDao" ref="bookDao"/>
​		</bean>
​	注意:配置中的两个bookDao的含义是不一样的
​		name="bookDao"中bookDao的作用是让Spring的IOC容器在获取到名称后，将首字母大写，前面加set找对应的setBookDao()方法进行对象注入
​		ref="bookDao"中bookDao的作用是让Spring能在IOC容器中找到id为bookDao的Bean对象给bookService进行注入

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

#### 7.bean实例化

​	bean是如何创建的？
​	①构造方法
​		bean本质上是对象，创建bean使用无参构造方法，然后在xml配置bean来完成
​	②使用静态工厂创建(了解)
​		先写出一个工厂类，写出静态方法返回对象，然后在配置文件里面配置bean，并且给出factory-method，class给出工厂名
​	③使用实例工厂 
​		写出一个工厂类，写出一个非静态方法，在实现类里面new工厂类调用方法，然在配置文件里面配置bean，先给出bean指向工厂类，然后下面写第二个bean，不写class，给出factory-method属性，同时给出factory-bean指向上面给出的那个bean名字
​	④实例工厂的改良(常用)
​		创建工厂类且类名加上Bean，并且实现FactoryBean<要实现的dao>接口，重写其中三个方法getObject()和getObjectType()和isSingleton()，其中第一个返回实现接口的实现类对象，第二个返回实现接口的.class，第三个然后true表示创造单例对象，false表示非单例，然后配置bean，class指向工厂类即可

#### 8.bean的生命周期

​	②Spring提供了两个接口来完成生命周期的控制，好处是可以不用再进行配置init-method和destroy-method
​		修改BookServiceImpl类，添加两个接口InitializingBean， DisposableBean并实现接口中的两个方法afterPropertiesSet和destroy
​	(1)关于Spring中对bean生命周期控制提供了两种方式:
​		在配置文件中的bean标签中添加init-method和destroy-method属性
​		类实现InitializingBean与DisposableBean接口，这种方式了解下即可。
​	(2)对于bean的生命周期控制在bean的整个生命周期中所处的位置如下:
​		初始化容器
​			1.创建对象(内存分配)
​			2.执行构造方法
​			3.执行属性注入(set操作)
​			4.执行bean初始化方法
​		使用bean
​			1.执行业务操作
​		关闭/销毁容器
​			1.执行bean销毁方法
​	(3)关闭容器的两种方式:
​		ConfigurableApplicationContext是ApplicationContext的子类
​		close()方法
​		registerShutdownHook()方法

#### 9.依赖注入方式

​	思考：(1)向一个类中传递数据的方式有几种？
​				①普通方法(set方法)
​				②构造方法
​		  (2)依赖注入描述了在容器中建立bean与bean之间依赖关系的过程，如果bean运行需要的是数字或字符串呢？
​		  		①引用类型
​		  		②简单类型(基本数据类型与String)
​	依赖注入方式
​	①setter注入(推荐使用)
​		(1)引用类型
​			①在bean中定义引用类型属性，并提供可访问的set方法
​				public class BookServiceImpl implements BookService {
​					private BookDao bookDao;
​					public void setBookDao(BookDao bookDao) {
​					this.bookDao = bookDao;
​					}
​				}
​			②配置中使用property标签ref属性注入引用类型对象
​				<bean id="bookService" class="com.itheima.service.impl.BookServiceImpl">
​					<property name="bookDao" ref="bookDao"/>
​				</bean>
​				<bean id="bookDao" class="com.itheima.dao.imipl.BookDaoImpl"/>
​		(2)简单类型
​			①在bean中定义引用类型属性并提供可访问的set方法
​			②配置中使用property标签value属性注入简单类型数据
​	②构造方法
​		(1)引用类型
​			①在bean中定义引用类型属性并提供可访问的构造方法
​			②配置中使用constructor-arg标签ref属性注入引用类型对象，其中该标签name指的的是形参名字
​		(2)简单类型
​			①在bean中定义引用类型属性并提供可访问的构造方法
​			②配置中使用constructor-arg标签value属性注入简单类型数据
​	依赖注入方式选择
​		1. 强制依赖使用构造器进行，使用setter注入有概率不进行注入导致null对象出现强制依赖指对象在创建的过程中必须要注入指定的参数
​		2. 可选依赖使用setter注入进行，灵活性强可选依赖指对象在创建过程中注入的参数可有可无
​		3. Spring框架倡导使用构造器，第三方框架内部大多数采用构造器注入的形式进行数据初始化，相对严谨
​		4. 如果有必要可以两者同时使用，使用构造器注入完成强制依赖的注入，使用setter注入完成可选依赖的注入
​		5. 实际开发过程中还要根据实际情况分析，如果受控对象没有提供setter方法就必须使用构造器注入
​		6. 自己开发的模块推荐使用setter注入

#### 10.依赖自动装配

​	概述：IoC容器根据bean所依赖的资源在容器中自动查找并注入到bean中的过程称为自动装配
​	自动装配方式有哪些?
​		①按类型（常用）
​			直接在配置中bean标签提供autowire属性，要求类型匹配是唯一的，意思就是定义的属性如name，必须唯一只有一个实现类
​		注意事项:
​			需要注入属性的类中对应属性的setter方法不能省略
​			被注入的对象必须要被Spring的IOC容器管理
​		②按名称
​		③按构造方法
​		④不启用自动装配
​	注意：
​		1. 自动装配用于引用类型依赖注入，不能对简单类型进行操作
​		2. 使用按类型装配时（byType）必须保障容器中相同类型的bean唯一，推荐使用
​		3. 使用按名称装配时（byName）必须保障容器中具有指定名称的bean，因变量名与配置耦合，不推荐使用
​		4. 自动装配优先级低于setter注入与构造器注入，同时出现时自动装配配置失效

#### 11.集合注入

​	使用：①先在配置文件中bean指向要注入集合的类
​		②根据集合的类型进行相关的注入方法
​	说明：property标签表示setter方式注入，构造方式注入constructor-arg标签内部也可以写<array>、<list>、<set>、<map>、<props>标签
​		List的底层也是通过数组实现的，所以<list>和<array>标签是可以混用
​		集合中要添加引用类型，只需要把<value>标签改成<ref>标签，这种方式用的比较少

#### 12.spring读取properties文件

​	使用：①在resources下创建一个properties文件，并添加对应属性的键值对
​		  ②开启context命名空间
​		  		<?xml version="1.0" encoding="UTF-8"?>
​				<beans xmlns="http://www.springframework.org/schema/beans"
​					xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
​					xmlns:context="http://www.springframework.org/schema/context"
​					xsi:schemaLocation="
​						http://www.springframework.org/schema/beans
​						http://www.springframework.org/schema/beans/spring-beans.xsd
​						http://www.springframework.org/schema/context
​						http://www.springframework.org/schema/context/springcontext.xsd">
​				</beans>
​			③加载properties配置文件
​				在配置文件中使用context命名空间下的标签来加载properties配置文件
​				<context:property-placeholder location="jdbc.properties"/>
​				在这里推荐使用：<context:property-placeholder location="classpath:*.properties"/>
​				这个只能读取当前工程的文件，classpath*：.properties是表示读取所有
​			④使用属性占位符${}完成属性注入
​				使用${key}来读取properties配置文件中的内容并完成属性注入
​					<context:property-placeholder location="jdbc.properties"/>
​					<bean id="dataSource" class="com.alibaba.druid.pool.DruidDataSource">
​						<property name="driverClassName" value="${jdbc.driver}"/>
​						<property name="url" value="${jdbc.url}"/>
​						<property name="username" value="${jdbc.username}"/>
​						<property name="password" value="${jdbc.password}"/>
​					</bean>

#### 13.容器

​	容器的创建方式：
​		①基本的：ApplicationContext ctx = new ClassPathXmlApplicationContext("applicationContext.xml");
​		②还有用绝对路径获取的：ApplicationContext ctx = new FileSystemXmlApplicationContext("D:/applicationContext.xml");
​	Bean的获取方式：
​		①BookDao bookDao = (BookDao) ctx.getBean("bookDao");
​		注意：这种方式存在的问题是每次获取的时候都需要进行类型转换，有没有更简单的方式呢?
​		②BookDao bookDao = ctx.getBean("bookDao"，BookDao.class);
​		注意：这种方式可以解决类型强转问题，但是参数又多加了一个，相对来说没有简化多少。
​		③BookDao bookDao = ctx.getBean(BookDao.class);
​		注意：这种方式就类似我们之前所学习依赖注入中的按类型注入。必须要确保IOC容器中该类型对应的bean对象只能有一个。	

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

#### 14.注解开发

​	使用@Component注解定义bean
​		①首先在需要bean管理的类上面加上注解
​		②在配置文件中开辟context空间，然后进行contxt组件扫描加载bean
​			<context:component-scan base-package="com.itheima"/>
​	注意：在service层使用@Service，在数据层impl中使用@Repository，在表现层使用@Controller，用法和Component一样

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

#### 15.注解开发管理第三方bean

​	使用步骤：
​		①导入对应的jar包
​			在这里我们导的是Druid的
​		②在配置类中添加一个方法
​		③在方法上添加@Bean注解
​			注意:
​				不能使用DataSource ds = new DruidDataSource()
​				在一个方法的上面加上@Bean代表这方法返回的是一个bean类型
​		④从IOC容器中获取对象并打印

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

#### 16.Spring整合MyBatis

​	spring技术要使用数据库，首先导入spring-jdbc，然后使用mybatis要导入mybatis-spring，它属于mybatis

#### 17.Spring整合Junit测试

​	首先在main下创建一个test文件夹，然后把要创建要测试的类，在类上面加上注解@RunWith(SpringJUnit4ClassRunner.class)和注解@ContextConfiguration(classes = SpringConfig.class)，然后对私人变量进行自动装配@Autowired，然后哪个方法测试哪个加上@Test即可

#### 18.AOP

​	概述：AOP(Aspect Oriented Programming)面向切面编程，一种编程范式，指导开发者如何组织程序结构。
​		切面编程里面有个通知类里面的方法叫通知，测试方法的叫连接点，中间有个切入点(允许被代理的点)，通知通过切面来切入进行连接
​		  OOP(Object Oriented Programming)面向对象编程
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

#### 19.AOP入门案例

​	思路分析：①我们采用注解的形式或者xml，这里选择注解
​			 ②导入坐标(pom.xml)
​			 ③制作连接点(原始操作，Dao接口与实现类)
​			 ④制作共性功能(通知类与通知)
​			 ⑤定义切入点
​			 ⑥绑定切入点与通知关系(切面)
​	实现步骤：①导入坐标
​				<groupId>org.aspectj</groupId>
​				<artifactId>aspectjweaver</artifactId>
​				<version>1.9.4</version>
​			 ②dao层接口和impl实现类，和执行类都没有影响
​			 ③在配置类里面加上注解@EnableAspectJAutoProxy表示用了注解AOP
​			 ④创建一个AOP包，里面创建通知类(MyAdvice)
​			 ⑤在通知类里创建切入点pt()并加上注解@Pointcut("execution(void com.itheima.dao.BookDao.update())")
​			 ⑥创建通知方法(即需要增强什么功能)，并且加上注解@Before("pt()")与切入点绑定
​		注意：切入点必须是一个私人的，无返回值的，无参的，并且没有内容。

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

#### 20.AOP切入点表达式

​	切入点：要进行增强的方法
​	切入点表达式：要进行增强的方法的描述方法
​	切入点表达式标准格式：动作关键字(访问修饰符 返回值 包名.类/接口名.方法名(参数) 异常名）
​	如execution(public User com.itheima.service.UserService.findById(int))
​		execution：动作关键字，描述切入点的行为动作，例如execution表示执行到指定切入点
​		public:访问修饰符,还可以是public，private等，可以省略
​		User：返回值，写返回值类型
​		com.itheima.service：包名，多级包使用点连接
​		UserService:类/接口名称
​		findById：方法名
​		int:参数，直接写参数的类型，多个类型用逗号隔开
​		异常名：方法定义中抛出指定异常，可以省略

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

#### 21.AOP通知类型

​	五种类型：前置通知@Before
​			 后置通知@After
​			 环绕通知(重点)@Around
​			 返回后通知(了解)@AfterReturning
​			 抛出异常后通知(了解)@AfterThrowing
​	关于环绕通知
​		①需要在参数里面加上ProceedingJoinPoint pjp，
​		②如果是有参数的，首先通知方法需要改为Object类型，同时需要加上返回值，可以接收原始操作的返回值并且返回出去

	其中pip.proceed()表示对原始操作的调用
	pip.proceed(Object[] args)：可以返回参数值
	
	其中pip.getSignature可以获取执行前面信息
	Signature signature = pjp.getSignature();
	
	通过签名获取执行操作名称(接口名)
	String className = signature.getDeclaringTypeName();
	
	通过签名获取执行操作名称(方法名)
	String methodName = signature.getName();
	可以通过这个得到各个方法的执行效率

#### 22.AOP通知获取数据

​	分析：
​		获取切入点方法的参数，所有的通知类型都可以获取参数
​			JoinPoint：适用于前置、后置、返回后、抛出异常后通知
​			ProceedingJoinPoint：适用于环绕通知
​			如jp.getArgs()
​		获取切入点方法返回值，前置和抛出异常后通知是没有返回值，后置通知可有可无，所以不做研究
​			返回后通知
​			环绕通知
​		获取切入点方法运行异常信息，前置和返回后通知是不会有，后置通知可有可无，所以不做研究
​			抛出异常后通知
​			环绕通知

	String.trim()：可以对参数进行去空格处理

#### 23.Spring事务

​	事务作用：在数据层保障一系列的数据库操作同成功同失败
​	Spring事务作用：在数据层或业务层保障一系列的数据库操作同成功同失败
​	Spring通过平台事务管理器PlatformTransactionManager接口来管理事务
​	其中有两个方法，commit是用来提交事务，rollback是用来回滚事务。
​	他有一个实现类DataSourceTransactionManager
​	使用步骤：
​		①在需要被事务管理(业务层impl)的方法上添加注解@Transactional
​		②在配置类JdbcConfig类中配置事务管理器
​			加上注解@Bean，然后创建事务管理器接口，并且在方法中传入数据对象的形参，
​			@Bean
​			public PlatformTransactionManager transactionManager(DataSourcedataSource){
​				DataSourceTransactionManager transactionManager = new DataSourceTransactionManager();
​				transactionManager.setDataSource(dataSource);
​				return transactionManager;
​				}
​			}
​		注意：事务管理器要根据使用技术进行选择，Mybatis框架使用的是JDBC事务，可以直接使用DataSourceTransactionManager
​		③开启事务注解
​			在SpringConfig的配置类中开启，告诉Spring我用了注解开启事务@EnableTransactionManagement

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
