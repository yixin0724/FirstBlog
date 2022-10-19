---
title: javaweb知识点
date: 2022-10-19 22:27:04
cover: ./img/javaweb.png
tags: Java
categories: java
description: 当时学的真是头疼呀，各种杂七杂八的。
---











## JDBC

用shift右键，点击powershell窗口，用java -jar来启动jar包	如sql.jar	java -jar .\sql.jar
1.JDBC(java数据库连接)
概念：JDBC就是使用java操作关系型数据库的一套API，就是定义了一套接口，每个关系数据库都有对应的类(即驱动)来接收
2.步骤：①创建工程，导入驱动jar包	
②注册驱动
Class.forName("")
③获取连接
DriverManager.getConnection()返回的是对象，需要有Connection   conn来接收
④定义SQL语句
⑤获取执行SQL的对象
Statement  stmt  = conn.createStatement()
⑥执行SQl
⑦处理结果
⑧释放资源
3.API详解
①DriverManager
	作用：注册驱动，获取数据库连接
	获取连接：url语法：jdbc:mysql://ip地址(域名):端口号/数据库名称?参数值对1&参数键值对2....
	细节：(1)如果连接是本机的mysql，并且端口为3306，则url可以写"jdbc:mysql:///db1"
(2)配置useSSl=false参数，禁用安全连接方式，解决警告提示
②Connection
	作用：获取执行SQL的对象
(1)普通执行SQL对象		Statement  createStatement()
(2)预编译SQL的执行对象：防止SQL注入		PreparedStatement  prepareStatement(sql)
(3)执行存储过程的对象	CallableStatement  prepareCall(sql)
	事务管理：用Connection接口定义3给个方法对进行事务管理
(1)setAutoCommit(boolean autoCommit)：true为自动提交事务，false为手动提交事务，即开启事务
(2)commit()		#在执行SQL前开启事务，执行结束后提交事务
(3)rollback()回滚事务	#在java中可以用try  catch来捕获异常，如果有异常则在catch中回滚
③Statement
	作用：执行SQL语句
	int  executeUpdate(sql)：执行DML，DDL语句，返回值：DML语句受影响的行数，或者DDL语句执行后，成功也可能是0
	ResultSet  executeQuery(sql)：执行DQL语句，返回值：ResultSet结果集对象
④ResultSet
	作用：封装了DQL查询语句的结果
	ResultSet  stmt.executeQuery(sql)：执行DQL语句，返回这个对象
	获取查询结果
	Boolean  next()：(1)将光标从当前位置向下移动一行 (2)判断当前行是否为有效行
	返回值：true，有效行，当前行有数据，false则反之
	xxx    getXxx(参数)：获取数据
	xxx：数据类型;如ing getint(参数);String  getString(参数);    参数：int，列的编号，从1开始;	String：列的名称
	使用步骤：①游标向下移动一行，并判断该行是否有数据：next()
②获取数据：getXxx(参数)
while(rs.next()){	//循环判断游标是否是最后一行末尾
	//获取数据
	rs.getXxx(参数);
⑤PreparedStatement
	概念：一个接口，继承自Statement，作用：预编译SQL语句并执行，预防SQL注入问题
	SQL注入：SQL注入是通过操作来修改事先定义好的SQL语句，用以达到执行代码对服务器进行攻击的方法
使用步骤：(1)获取PreparedStatement对象
//SQL语句中的参数值，使用？占位符替代
String sql = "select  *  from  user  where  usename=?  and  password=?";
//通过Connection对象获取，并传入对应的sql语句
PreparedStatement  pstmt = conn.PreparedStatement(sql);
(2)设置参数值
//上面的sql语句中参数使用 ? 进行占位，在之前之前肯定要设置这些 ? 的值。
PreparedStatement对象：setXxx(参数1，参数2)：给 ? 赋值
Xxx：数据类型 ； 如 setInt (参数1，参数2)
参数1： ？的位置编号，从1 开始
参数2： ？的值
(3)执行SQL语句
executeUpdate(); 执行DDL语句和DML语句
executeQuery(); 执行DQL语句
==注意：==
调用这两个方法时不需要传递SQL语句，因为获取SQL语句执行对象时已经对SQL语句进行预编译了。
useServerPrepStmts=true	//开启预编译功能   		
//在代码中编写url时需要加上以下参数。而我们之前根本就没有开启预编译功能，只是解决了SQL注入漏洞。
配置MySQL执行日志（重启mysql服务后生效）
在mysql配置文件（my.ini）中添加如下配置
log-output=FILE
general-log=1
general_log_file="D:\mysql.log"
slow-query-log=1
slow_query_log_file="D:\mysql_slow.log"
long_query_time=2
4.数据库连接池
概念：数据库连接池是个容器，负责分配、管理数据库连接(Connection)，它允许应用程序重复使用一个现有的数据库连接，而不是再重新建立一个；
释放空闲时间超过最大空闲时间的数据库连接来避免因为没有释放数据库连接而引起的数据库连接遗漏
好处：资源重用，提升系统响应速度，避免数据库连接遗漏

连接池的实现：
	标准接口：DataSource
	官方(SUN) 提供的数据库连接池标准接口，由第三方组织实现此接口。
	该接口的功能：获取连接		Connection getConnection()
	//那么以后就不需要通过 DriverManager 对象获取 Connection对象，而是通过连接池（DataSource）获取 Connection 对象。
	常见的数据库连接池DBCP，C3P0，Druid
	我们现在使用更多的是Druid，它的性能比其他两个会好一些。
	提示：输出System.getProperty("user.dir");输出当前的路径
	Druid使用步骤：①导入jar包 druid-1.1.12.jar
	②定义配置文件
	③加载配置文件
	Properties prop = new Properties();
	        prop.load(new FileInputStream("jdbc-demo/src/druid.properties"));
	④获取数据库连接池对象
	DataSource dataSource = DruidDataSourceFactory.createDataSource(prop);
	⑤获取连接
	Connection connection = dataSource.getConnection();
	        System.out.println(connection);
	//修改数据库的编码格式alter database db1 character set utf8;
	show variables like 'char%';







## Mybatis

1.Mybatis概念
	Mybatis基础坏境搭建：
	Mybatis-config.xml    要放在resource资源目录下
	BrandMapper.xml       要在resource创建同样路径的文件下放进去
	BrandMapper接口

MyBatis 是一款优秀的持久层框架，用于简化 JDBC 开发
	持久层：
	负责将数据到保存到数据库的那一层代码。
	以后开发我们会将操作数据库的Java代码作为持久层。而Mybatis就是对jdbc代码进行了封装。
JavaEE三层架构：表现层、业务层、持久层
	框架：
	框架就是一个半成品软件，是一套可重用的、通用的、软件基础代码模型
	在框架的基础之上构建软件编写更加高效、规范、通用、可扩展
2.Mapper代理
	通过上面的描述可以看出 Mapper 代理方式的目的：
	解决原生方式中的硬编码
	简化后期执行SQL

3.mybatisx插件可以很方便的操作执行语句
	蓝色的小鸟代表usermapper接口
	安装 MyBatisX 插件
	MybatisX 是一款基于 IDEA 的快速开发插件，为效率而生。
	主要功能
	XML映射配置文件 和 接口方法 间相互跳转
	根据接口方法生成 statement
	红色头绳的表示映射配置文件，蓝色头绳的表示mapper接口。在mapper接口点击红色头绳的小鸟图标会自动跳转
	到对应的映射配置文件，在映射配置文件中点击蓝色头绳的小鸟图标会自动跳转到对应的mapper接口。也可以在
	mapper接口中定义方法，自动生成映射配置文件中的 statement
4.一般第一步为编写接口方法：Mapper接口；
	第二部然后看是否需要参数，和返回结果类型是什么，在这里是List<Brand>
	第三步编写SQL语句：SQLxml映射文件
	第四步执行方法，进行测试


5.步骤
	//1. 获取SqlSessionFactory
	String resource = "mybatis-config.xml";
	InputStream inputStream = Resources.getResourceAsStream(resource);
	SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);
	//2. 获取SqlSession对象
	SqlSession sqlSession = sqlSessionFactory.openSession();
	//3. 获取Mapper接口的代理对象
	BrandMapper brandMapper = sqlSession.getMapper(BrandMapper.class);
	//4. 执行方法
	List<Brand> brands = brandMapper.selectAll();
	System.out.println(brands);
	//5. 释放资源
	sqlSession.close();

6.当出现数据库里面的属性为下划线，而idea中是驼峰命名
	这个问题可以通过两种方式进行解决：
	①给字段起别名：
	使用resultMap定义字段和属性的映射关系
	②SQL片段：
	将需要复用的SQL片段抽取到 sql 标签中
	id属性值是唯一标识，引用时也是通过该值进行引用。
	在原sql语句中进行引用
	使用 include 标签引用上述的 SQL 片段，而 refid 指定上述 SQL 片段的id值。
	上面这两个都不灵活
	③所以我们可以使用resultMap解决上述问题
	起别名 + sql片段的方式可以解决上述问题，但是它也存在问题。如果还有功能只需要查询部分字段，而不是查询所有字段，
	那么我们就需要再定义一个 SQL 片段，这就显得不是那么灵活。
	那么我们也可以使用resultMap来定义字段和属性的映射关系的方式解决上述问题。
	在映射配置文件中使用resultMap定义 字段 和 属性 的映射关系
	id：完成主键字段的映射
	column：表的列名
	property：实体类的属性名
	result：完成一般字段的映射
	column：表的列名
	property：实体类的属性名


7.条件查询
    /**
     *条件查询
     *  参数接收
     *  3种方式
     *      1.散装参数：如果方法中有多个参数,使用 @Param("参数名称") 标记每一个参数，在映射配置文件中就需要使用 #{参数名称} 进行占位
     *      2.对象参数:将多个参数封装成一个 实体对象 ，将该实体对象作为接口的方法参数。该方式要求在映射配置文件的SQL中使用 #{内
     * 容} 时，里面的内容必须和实体类属性名保持一致。
     *      3.map集合的参数
     */
8.动态条件查询
	概述：上述功能实现存在很大的问题。用户在输入条件时，肯定不会所有的条件都填写，这个时候我们的SQL语句就不能那样写的
	针对上述的需要，Mybatis对动态SQL有很强大的支撑：
	if
	choose (when, otherwise)
	trim (where, set)
	foreach
	我们先学习 if 标签和 where 标签：
	if 标签：条件判断
	test 属性：逻辑表达式
	        问题：如果不查询第一个Status，那么后面会直接查询第二个会出现sql语法错误，where and语法错误
	        解决办法：
	                1.可以在第一个前面加and，然后在where后面加入恒等式
	                2.用<where> 替换 where	注意：需要给每个条件前都加上 and 关键字。
	choose：从多个条件选择一个，类似于switch
9.批量删除
	在 BrandMapper.xml 映射配置文件中编写删除多条数据的 statement 。
	编写SQL时需要遍历数组来拼接SQL语句。Mybatis 提供了 foreach 标签供我们使用
	foreach 标签
	用来迭代任何可迭代的对象（如数组，集合）。
	collection 属性：
	mybatis会将数组参数，封装为一个Map集合。
	默认：array = 数组
	使用@Param注解改变map集合的默认key的名称
	item 属性：本次迭代获取到的元素。
	separator 属性：集合项迭代之间的分隔符。 foreach 标签不会错误地添加多余的分隔符。也就是最后一次迭代不会加
	分隔符。
	open 属性：该属性值是在拼接SQL语句之前拼接的语句，只会拼接一次
	close 属性：该属性值是在拼接SQL语句拼接后拼接的语句，只会拼接一次

10.注解开发
	使用注解开发会比配置文件开发更加方便。如下就是使用注解进行开发
	@Select(value = "select * from tb_user where id = #{id}")
	public User select(int id);
	#注解是用来替换映射配置文件方式配置的，所以使用了注解，就不需要再映射配置文件中书写对应的 statement
	Mybatis 针对 CURD 操作都提供了对应的注解，已经做到见名知意。如下：
	查询 ：@Select
	添加 ：@Insert
	修改 ：@Update
	删除 ：@Delete
	#注解完成简单功能，配置文件完成复杂功能。


tomcat知识点
1.项目部署：就是把war包或者文件夹直接复制到目录workapps下就好，他会自动解压，这时在浏览器访问localhost:8080/hello/a.html
2.创建Maven Web项目
创建方式有两种:使用骨架和不使用骨架
**使用骨架**
> 具体的步骤包含:
> 1.创建Maven项目
> 2.选择使用Web项目骨架
> 3.输入Maven项目坐标创建项目
> 4.确认Maven相关的配置信息后，完成项目创建
> 5.删除pom.xml中多余内容
> 6.补齐Maven Web项目缺失的目录结构

**不使用骨架**
>具体的步骤包含:
>1.创建Maven项目
>2.选择不使用Web项目骨架
>3.输入Maven项目坐标创建项目
>4.在pom.xml设置打包方式为war
>5.补齐Maven Web项目缺失webapp的目录结构
>6.补齐Maven Web项目缺失WEB-INF/web.xml的目录结构

3. Tomcat Maven插件
	在IDEA中使用本地Tomcat进行项目部署，相对来说步骤比较繁琐，所以我们需要一种更简便的方式来替换它，那就是直接使用Maven中的Tomcat插件来部署项目，具体的实现步骤，只需要两步，分别是:

	1. 在pom.xml中添加Tomcat插件

	   ```xml
	   <build>
	       <plugins>
	       	<!--Tomcat插件 -->
	           <plugin>
	               <groupId>org.apache.tomcat.maven</groupId>
	               <artifactId>tomcat7-maven-plugin</artifactId>
	               <version>2.2</version>
	           </plugin>
	       </plugins>
	   </build>



## Servlet

servlet知识点
1.* Servlet是JavaWeb最为核心的内容，它是Java提供的一门==动态==web资源开发技术。

* 使用Servlet就可以实现，根据不同的登录用户在页面上动态显示不同内容。
* Servlet是JavaEE规范之一，其实就是一个接口，将来我们需要定义Servlet类实现Servlet接口，并由web服务器运行Servlet

2. 执行流程
* 浏览器发出`http://localhost:8080/web-demo/demo1`请求，从请求中可以解析出三部分内容，分别是`localhost:8080`、`web-demo`、`demo1`
  * 根据`localhost:8080`可以找到要访问的Tomcat Web服务器
  * 根据`web-demo`可以找到部署在Tomcat服务器上的web-demo项目
  * 根据`demo1`可以找到要访问的是项目中的哪个Servlet类，根据@WebServlet后面的值进行匹配
* 找到ServletDemo1这个类后，Tomcat Web服务器就会为ServletDemo1这个类创建一个对象，然后调用对象中的service方法
  * ServletDemo1实现了Servlet接口，所以类中必然会重写service方法供Tomcat Web服务器进行调用
  * service方法中有ServletRequest和ServletResponse两个参数，ServletRequest封装的是请求数据，ServletResponse封装的是响应数据，后期我们可以通过这两个参数实现前后端的数据交互

  Servlet由web服务器创建，Servlet方法由web服务器调用

  因为我们自定义的Servlet,必须实现Servlet接口并复写其方法，而Servlet接口中有service方法

3.生命周期
  * 生命周期: 对象的生命周期指一个对象从被创建到被销毁的整个过程。
  1. ==加载和实例化==：默认情况下，当Servlet第一次被访问时，由容器创建Servlet对象

  默认情况，Servlet会在第一次访问被容器创建，但是如果创建Servlet比较耗时的话，那么第一个访问的人等待的时间就比较长，用户的体验就比较差，那么我们能不能把Servlet的创建放到服务器启动的时候来创建，具体如何来配置?
  @WebServlet(urlPatterns = "/demo1",loadOnStartup = 1)
  loadOnstartup的取值有两类情况
  	（1）负整数:第一次访问时创建Servlet对象
  	（2）0或正整数:服务器启动时创建Servlet对象，数字越小优先级越高

  2. ==初始化==：在Servlet实例化之后，容器将调用Servlet的==init()==方法初始化这个对象，完成一些如加载配置文件、创建连接等初始化的工作。该方法只==调用一次==
  3. ==请求处理==：==每次==请求Servlet时，Servlet容器都会调用Servlet的==service()==方法对请求进行处理
  4. ==服务终止==：当需要释放内存或者容器关闭时，容器就会调用Servlet实例的==destroy()==方法完成资源的释放。在destroy()方法调用之后，容器会释放这个Servlet实例，该实例随后会被Java的垃圾收集器所回收
	* 初始化方法，在Servlet被创建时执行，只执行一次
	void init(ServletConfig config) 
	* 提供服务方法， 每次Servlet被访问，都会调用该方法
	void service(ServletRequest req, ServletResponse res)
	* 销毁方法，当Servlet被销毁时，调用该方法。在内存释放或服务器关闭时销毁Servlet
	void destroy() 
	剩下的两个方法是:
	* 获取Servlet信息
	String getServletInfo() 
	//该方法用来返回Servlet的相关信息，没有什么太大的用处，一般我们返回一个空字符串即可
	public String getServletInfo() {
	    return "";
	}
	* 获取ServletConfig对象
	ServletConfig getServletConfig()
	ServletConfig对象，在init方法的参数中有，而Tomcat Web服务器在创建Servlet对象的时候会调用init方法，必定会传入一个ServletConfig对象，我们只需要将服务器传过来的ServletConfig进行返回即可。具体如何操作?
	1. HttpServlet的使用步骤

	> 继承HttpServlet
	>
	> 重写doGet和doPost方法

	2. HttpServlet原理

	> 获取请求方式，并根据不同的请求方式，调用不同的doXxx方法
	4.urlPattern配置
	* 一个Servlet,可以配置多个urlPattern

5.Request（请求）&Response（响应）
	概念：
	request:获取请求数据
	浏览器会发送HTTP请求到后台服务器[Tomcat]
	HTTP的请求中会包含很多请求数据[请求行+请求头+请求体]
	后台服务器[Tomcat]会对HTTP请求中的数据进行解析并把解析结果存入到一个对象中
	所存入的对象即为request对象，所以我们可以从request对象中获取请求的相关参数
	获取到数据后就可以继续后续的业务，比如获取用户名和密码就可以实现登录操作的相关业务
	response:设置响应数据
	业务处理完后，后台就需要给前端返回业务处理的结果即响应数据
	把响应数据封装到response对象中
	后台服务器[Tomcat]会解析response对象,按照[响应行+响应头+响应体]格式拼接结果
	浏览器最终解析结果，把内容展示在浏览器给用户浏览
	小结：request对象是用来封装请求数据的对象
	response对象是用来封装响应数据的对象

6.Requst继承体系
	Request的继承体系为ServletRequest-->HttpServletRequest-->RequestFacade
	①Tomcat需要解析请求数据，封装为request对象,并且创建request对象传递到service方法
	httpservlet是一个类需要继承使用
	②使用request对象，可以查阅JavaEE API文档的HttpServletRequest接口中方法说明
7.Request获取请求数据
	请求数据分为3部分
	1.请求行：
	获取请求方式: GET
	String getMethod()
	动态获取虚拟目录(项目访问路径): /request-demo
	String getContextPath()
	获取URL(统一资源定位符): http://localhost:8080/request-demo/req1
	StringBuffer getRequestURL()
	获取URI(统一资源标识符): /request-demo/req1
	String getRequestURI()
	获取请求参数(GET方式): username=zhangsan&password=123
	String getQueryString()
	2.请求头：
	getHeader(String name)根据请求头名称获取其对应的值
	3.请求体：
	注意: 浏览器发送的POST请求才有请求体
	如果是纯文本数据:getReader()
	如果是字节数据如文件数据:getInputStream()

​	request通用方式请求参数
​	方法：
​	获取所有参数Map集合
​	Map<String,String[]> getParameterMap()
​	根据名称获取参数值（数组）
​	String[] getParameterValues(String name)
​	根据名称获取参数值(单个值)
​	 String getParameter(String name)

​	 req.getParameter()方法使用的频率会比较高,获取单个参数

​	 解决post请求乱码问题
​	 分析出现中文乱码的原因：
​	POST的请求参数是通过request的getReader()来获取流中的数据
​	TOMCAT在获取流的时候采用的编码是ISO-8859-1
​	ISO-8859-1编码是不支持中文的，所以会出现乱码
​	解决方案：
​	页面设置的编码格式为UTF-8
​	把TOMCAT在获取流数据之前的编码设置为UTF-8
​	通过request.setCharacterEncoding("UTF-8")设置编码,UTF-8也可以写成小写

​	解决get请求中文乱码问题

​	request的请求转发
​	实现方法：req.getRequestDispatcher("/req6").forward(req,resp)
​	请求转发资源间共享数据:使用Request对象
​	①存储数据到request域[范围,数据是存储在request对象]中：
​	void setAttribute(String name键,Object o值);
​	②根据key获取值：
​	Object getAttribute(String name);
​	③根据key删除该键值对：
​	void removeAttribute(String name);
​	请求转发的特点：
​	①浏览器地址栏路径不发生变化
​	②只能转发到当前服务器的内部资源，不能从一个服务器通过转发访问另一台服务器
​	③一次请求，可以在转发资源间使用request共享数据，虽然后台从/req5转发到/req6，但是这个只有一次请求

8.Response对象
	响应数据分为响应行，响应头，响应体
	①响应行 
	设置响应状态码:void setStatus(int sc);
	②响应头
	设置响应头键值对：void setHeader(String name,String value);
	③响应体
	获取字符输出流:PrintWriter getWriter();
	获取字节输出流：ServletOutputStream getOutputStream();

​	Response完成重定向
​	重定向：一种资源跳转方式
​	重定向实现的两种方式:
​	①resp.setStatus(302);
​	resp.setHeader("location（固定的)","资源B的访问路径");
​	②resposne.sendRedirect("/request-demo/resp2")
​	重定向的特点：
​	浏览器地址栏路径发送变化
​	可以重定向到任何位置的资源(服务内容、外部均可)
​	两次请求，不能在多个资源使用request共享数据，因为浏览器发送了两次请求，是两个不同的request对象，就无法通过request对象进行共享数据

​	路径问题：
​	浏览器发起使用:需要加虚拟目录(项目访问路径)
​	服务端发起使用:不需要加虚拟目录

​	Response响应字符数据
​	步骤实现：
​	设置流的编码:response.setContentType("text/html;charset=utf-8");
​	①通过Response对象获取字符输出流： PrintWriter writer = resp.getWriter();
​	②通过字符输出流写数据: writer.write("aaa");
​	注意:一次请求响应结束后，response对象就会被销毁掉，所以不要手动关闭流。

​	Response响应字节数据
​	实现步骤：
​	①通过Response对象获取字节输出流：ServletOutputStream outputStream =resp.getOutputStream();
​	②通过字节输出流写数据: outputStream.write(字节数据);

​        

### JSP

JSP知识点
1.JSP概述
	JSP（全称：Java Server Pages）：Java 服务端页面。
	是一种动态的网页技术，其中既可以定义 HTML、JS、CSS等静态内容，还可以定义 Java代码的动态内容，
	也就是 JSP = HTML + Java 
2.快速入门
	①导入坐标
	<dependency>
	<groupId>javax.servlet.jsp</groupId>
	<artifactId>jsp-api</artifactId>
	<version>2.2</version>
	<scope>provided</scope>
	</dependency>
	该依赖的 scope 必须设置为 provided ，因为 tomcat 中有这个jar包了，所以在打包时我们是不希望将该依赖打进到我们工程的war包中。
	②建立文件，在webapp下建立jsp文件
	③写代码
3.jsp原理
	他本质上就是一个Servlet

	1. 浏览器第一次访问 hello.jsp 页面
	2. tomcat 会将 hello.jsp 转换为名为 hello_jsp.java 的一个 Servlet
	3. tomcat 再将转换的 servlet 编译成字节码文件 hello_jsp.class
	4. tomcat 会执行该字节码文件，向外提供服务

4.jsp脚本
	JSP 脚本有如下三个分类：
	<%...%>：内容会直接放到_jspService()方法之中
	<%=…%>：内容会放到out.print()中，作为out.print()的参数
	<%!…%>：内容会放到_jspService()方法之外，被类直接包含

jsp虽然逐渐被淘汰但还是要会，以后开发更多的是使用 HTML + Ajax 来替代，但是目前可以采用不在jsp里写java代码，，使用servlet进行逻辑处理，封装数据，然后用jsp获取数据，遍历展现数据



### EL

EL表达式
1.概述
	EL（全称Expression Language ）表达式语言，用于简化 JSP 页面内的 Java 代码。
	EL 表达式的主要作用是 获取数据。其实就是从域对象中获取数据，然后将数据展示在页面上。
	而 EL 表达式的语法也比较简单，${expression} 。

2.域对象
	JavaWeb中有四大域对象，分别是：
	page：当前页面有效
	request：当前请求有效
	session：当前会话有效
	application：当前应用有效
	el 表达式获取数据，会依次从这4个域中寻找，直到找到为止
3.JSTL标签
	概述：JSP标准标签库(Jsp Standarded Tag Library) ，使用标签取代JSP页面上的Java代码。
	常用的标签：
	①if标签：<c:if> ：相当于 if 判断
	属性：test，用于定义条件表达式
	②forEach 标签
	<c:forEach> ：相当于 for 循环。java中有增强for循环和普通for循环，JSTL 中的 <c:forEach> 也有两种用法
	用法一
	类似于 Java 中的增强for循环。涉及到的 <c:forEach> 中的属性如下
	items：被遍历的容器
	var：遍历产生的临时变量
	varStatus：遍历状态对象
	如下代码，是从域对象中获取名为 brands 数据，该数据是一个集合；遍历遍历，并给该集合中的每一个元素起名为brand ，是 Brand对象。在循环里面使用 EL表达式获取每一个Brand对象的属性值
	<c:forEach items="${brands}" var="brand">
	<tr align="center">
	<td>${brand.id}</td>
	<td>${brand.brandName}</td>
	<td>${brand.companyName}</td>
	<td>${brand.description}</td>
	</tr>
	</c:forEach>
	2 用法二
	类似于 Java 中的普通for循环。涉及到的 <c:forEach> 中的属性如下
	begin：开始数
	end：结束数
	step：步长
	实例：从0循环到10，变量名是 i ，每次自增1
	<c:forEach begin="0" end="10" step="1" var="i">
	${i}
	</c:forEach>

​	jstl的使用步骤：
​	①导入坐标
​	<dependency>
​	<groupId>jstl</groupId>
​	<artifactId>jstl</artifactId>
​	<version>1.2</version>
​	</dependency>
​	<dependency>
​	<groupId>taglibs</groupId>
​	<artifactId>standard</artifactId>
​	<version>1.1.2</version>
​	</dependency>
​	②在JSP页面上引入JSTL标签库
​	<%@ taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core" %>

4.MVC模式
	MVC 是一种分层开发的模式，其中：
	M：Model，业务模型，处理业务
	V：View，视图，界面展示
	C：Controller，控制器，处理请求，调用模型和视图
	控制器（serlvlet）用来接收浏览器发送过来的请求，控制器调用模型（JavaBean）来获取数据，比如从数据库查询数据；控制器获取到数据后再交由视图（JSP）进行数据展示。
	JavaBean 是一种JAVA语言写成的可重用组件。为写成JavaBean，类必须是具体的和公共的，并且具有无参数的构造器。JavaBean 通过提供符合一致性设计模式的公共方法将内部域暴露成员属性，set和get方法获取。众所周知，属性名称符合这种模式，其他Java 类可以通过自省机制(反射机制)发现和操作这些JavaBean 的属性。
5.三层架构
	数据访问层：对数据库的CRUD基本操作
	业务逻辑层：对业务逻辑进行封装，组合数据访问层层中基本功能，形成复杂的业务逻辑功能。例如 注册业务功能 ，我
	们会先调用 数据访问层 的 selectByName() 方法判断该用户名是否存在，如果不存在再调用 数据访问层 的 insert()
	方法进行数据的添加操作
	表现层：接收请求，封装数据，调用业务逻辑层，响应数据
	而整个流程是，浏览器发送请求，表现层的Servlet接收请求并调用业务逻辑层的方法进行业务逻辑处理，而业务逻辑层方法调用数据访问层方法进行数据的操作，依次返回到serlvet，然后servlet将数据交由 JSP 进行展示。
	每一层都有特有的包名：
	表现层： com.itheima.controller 或者 com.itheima.web
	业务逻辑层： com.itheima.service
	数据访问层： com.itheima.dao 或者 com.itheima.mapper





### 会话跟踪技术

1.概述：
	会话:用户打开浏览器，访问web服务器的资源，会话建立，直到有一方断开连接，会话结束。在一次会话中可以包含多次请求和响应。

​	会话跟踪:一种维护浏览器状态的方法，服务器需要识别多次请求是否来自于同一浏览器，以便在同一次会话的多次请求间共享数据。
2.实现方式：
​	①客户端会话跟踪技术：Cookie
​	②服务端会话跟踪技术：Session
3.Cookie
​	Cookie：客户端会话技术，将数据保存到客户端，以后每次请求都携带Cookie数据进行访问。

​	Cookie的基本使用：
​	①创建Cookie对象，并设置数据
​	Cookie cookie = new Cookie("key","value");
​	②发送Cookie到客户端：使用response对象
​	response.addCookie(cookie);
​	Cookie的获取：
​	③获取客户端携带的所有Cookie，使用request对象
​	Cookie[] cookies = request.getCookies();
​	④遍历数组，获取每一个Cookie对象：for
​	⑤使用Cookie对象方法获取数据
​	cookie.getName();
​	cookie.getValue();

4.Cookie的使用细节
	Cookie存活时间
	①默认情况下，Cookie存储在浏览器内存中，当浏览器关闭，内存释放，则Cookie被销毁
	②可以设置Cookie存活时间，用setMaxAge(int seconds)
	参数值为:
	1.正数：将Cookie写入浏览器所在电脑的硬盘，持久化存储。到时间自动删除
	2.负数：默认值，Cookie在当前浏览器内存中，当浏览器关闭，则Cookie被销毁
	3.零：删除对应Cookie

​	Cookie存储中文(tomcat8以后就没了)
​	步骤：
​	1.在AServlet中对中文进行URL编码，采用URLEncoder.encode()，将编码后的值存入Cookie中
​	2.在BServlet中获取Cookie中的值,获取的值为URL编码后的值
​	3.将获取的值在进行URL解码,采用URLDecoder.decode()，就可以获取到对应的中文值

5.Session
	Session：服务端会话跟踪技术：将数据保存到服务端。

​	Session的基本使用：
​	在JavaEE中提供了HttpSession接口，来实现一次会话的多次请求之间数据共享功能。
​	使用：
​	①获取Session对象,使用的是request对象
​	HttpSession session = request.getSession();

​	Session对象提供的功能:
​	①存储数据到 session 域中
​	void setAttribute(String name, Object o)
​	②根据 key，获取值
​	Object getAttribute(String name)
​	③根据 key，删除该键值对
​	void removeAttribute(String name)

6.Session的使用细节
	Session的实现是居于Cookie的

​	Sesion钝化和活化
​	钝化：在服务器正常关闭后，Tomcat会自动将Session数据写入硬盘的文件中
​	钝化的数据路径为:项目目录\target\tomcat\work\Tomcat\localhost\项目名称\SESSIONS.ser
​	活化：再次启动服务器后，从文件中加载数据到Session中数据加载到Session中后，路径中的SESSIONS.ser文件会被删除掉
​	小结
​	Session的钝化和活化介绍完后，需要我们注意的是:
​	session数据存储在服务端，服务器重启后，session数据会被保存
​	浏览器被关闭启动后，重新建立的连接就已经是一个全新的会话，获取的session数据也是一个新的对象
​	session的数据要想共享，浏览器不能关闭，所以session数据不能长期保存数据
​	cookie是存储在客户端，是可以长期保存

​	Session的销毁
​	session的销毁会有两种方式:
​	①默认情况下，无操作，30分钟自动销毁，对于这个失效时间，是可以通过配置进行修改的
​	在项目的web.xml中配置：
​	<session-config>
​	<session-timeout>100</session-timeout>
​	</session-config>
​	②调用Session对象的invalidate()进行销毁

​	小结：如果需要登录后成功进行保存相关数据用session(安全)，长时间保存用cookie(不安全)





### Filter知识点

1.概述：
	Filter 表示过滤器，是 JavaWeb 三大组件(Servlet、Filter、Listener)之一。
	功能：
	①过滤器可以把对资源的请求拦截下来，从而实现一些特殊的功能。
	②过滤器一般完成一些通用的操作。
	③是权限控制，以后我们还会进行细粒度权限控制。过滤器还可以做 统一编码处理 、 敏感字符处理 等
2.Filter快速入门
	①定义类，实现Filter接口，并重写其所有方法
	②配置Filter拦截资源的路径：在类上定义 @WebFilter 注解。而注解的 value 属性值 /* 表示拦截所有的资源
	③在doFilter方法中输出一句话，并放行
	上述代码中的 chain.doFilter(request,response); 就是放行，也就是让其访问本该访问的资源。

​	访问逻辑：先执行放行前逻辑，然后放行，然后访问资源，然后执行放行后逻辑

3.Filter使用细节
	拦截路径有如下四种配置方式：
	①拦截具体的资源：/index.jsp：只有访问index.jsp时才会被拦截
	②目录拦截：/user/*：访问/user下的所有资源，都会被拦截
	③后缀名拦截：*.jsp：访问后缀名为jsp的资源，都会被拦截
	④拦截所有：/*：访问所有资源，都会被拦截

​	过滤器链
​	过滤器链是指在一个Web应用，可以配置多个过滤器，这多个过滤器称为过滤器链。
​	规则：我们现在使用的是注解配置Filter，而这种配置方式的优先级是按照过滤器类名(字符串)的自然排序。

Listener(不常用)
1.概述：
	Listener 表示监听器，是 JavaWeb 三大组件(Servlet、Filter、Listener)之一。
	监听器可以监听就是在 application ， session ， request 三个对象创建、销毁或者往其中添加修改删除属性时自动执行代码的功能组件。
2.使用
	这里面只有 ServletContextListener 这个监听器后期我们会接触到， ServletContextListener 是用来监听
	ServletContext 对象的创建和销毁。
	ServletContextListener 接口中有以下两个方法
	void contextInitialized(ServletContextEvent sce) ： ServletContext 对象被创建了会自动执行的方法
	void contextDestroyed(ServletContextEvent sce) ： ServletContext 对象被销毁时会自动执行的方法
	
	实现步骤：
	①定义类，实现ServletContextListener接口
	②在类上加@WebListener注解

​	ServletContextListener 在web项目启动时，tomcat会自动帮你生成对象



### AJAX知识点

1.概述：
	AJAX (Asynchronous JavaScript And XML)：异步的 JavaScript 和 XML。
2.作用：

	1. 与服务器进行数据交换：通过AJAX可以给服务器发送请求，服务器将数据直接响应回给浏览器。
	2. 异步交互：可以在不重新加载整个页面的情况下，与服务器交换数据并更新部分网页的技术，
	3.同步和异步
	同步一般是需要先请求服务器，并且等待响应，然后会重新刷新页面加载资源，他的操作是不连续的
	而异步是不需要请求服务器，而是直接加载，并且不会刷新页面，他的操作是同步的
	异步需要写全路径

4.快速入门
	步骤：
	①编写AjaxServlet，并使用response输出字符串
	②创建XMLHttpRequest对象：用于和服务器交换数据
	③向服务器发送请求
	④获取服务器响应数据

Axios
1.概述：
	Axios 对原生的AJAX进行封装，简化书写。
	Axios官网是： https://www.axios-http.cn
2.使用步骤：
	①引入axios的js文件：
	<script src="js/axios-0.18.0.js"></script>
​	②使用axios 发送请求，并获取响应结果
​	发送get请求
​	axios({
​	method:"get",
​	url:"http://localhost:8080/ajax-demo1/aJAXDemo1?username=zhangsan"
​	}).then(function (resp){
​	alert(resp.data);
​	})
​	发送post请求
​	axios({
​	method:"post",
​	url:"http://localhost:8080/ajax-demo1/aJAXDemo1",
​	data:"username=zhangsan"
​	}).then(function (resp){
​	alert(resp.data);
​	});

​	axios() 是用来发送异步请求的，小括号中使用 js 对象传递请求相关的参数：
​	method 属性：用来设置请求方式的。取值为 get 或者 post 。
​	url 属性：用来书写请求的资源路径。如果是 get 请求，需要将请求参数拼接到路径的后面，格式为： url?参数名=参
​	数值&参数名2=参数值2 。
​	data 属性：作为请求体被发送的数据。也就是说如果是 post 请求的话，数据需要作为 data 属性的值。
​	then() 需要传递一个匿名函数。我们将 then() 中传递的匿名函数称为 回调函数，意思是该匿名函数在发送请求时不会被
​	调用，而是在成功响应后调用的函数。而该回调函数中的 resp 参数是对响应的数据进行封装的对象，通过 resp.data 可
​	以获取到响应的数据。
3.Axios异步框架
​	请求方法别名
​	get 请求 ： axios.get(url[,config])
​	post 请求： axios.post(url[,data[,config])

Json知识点
1.概念：
	 JavaScript Object Notation 。JavaScript 对象表示法.
	  json 格式中的键要求必须使用双引号括起来
2.使用
	定义格式：var 变量名 = '{"key":value,"key":value,...}';
	JSON 串的键要求必须使用双引号括起来，而值根据要表示的类型确定。value 的数据类型分为如下：
	①数字（整数或浮点数）
	②字符串（使用双引号括起来）
	③逻辑值（true或者false）
	④数组（在方括号中）
	⑤对象（在花括号中）
	⑥null
	获取数据：变量名.key    json.name

Json数据和java对象转换
1.Fastjson 概述：
	Fastjson 是阿里巴巴提供的一个Java语言编写的高性能功能完善的 JSON 库，是目前Java语言中最快的 JSON 库，可以实现 Java 对象和 JSON 字符串的相互转换。
2.Fastjson使用
	①导入坐标
	<dependency>
	<groupId>com.alibaba</groupId>
	<artifactId>fastjson</artifactId>
	<version>1.2.62</version>
	</dependency>
	②Java对象转JSON
	String jsonStr = JSON.toJSONString(obj);
	③JSON字符串转Java对象
	User user = JSON.parseObject(jsonStr, User.class);



### vue知识点

1.概念：
	Vue 是一套前端框架，免除原生JavaScript中的DOM操作，简化书写。
2.快速入门
	步骤：①新建 HTML 页面，引入 Vue.js文件

	 <script src="js/vue.js"></script>
​	② 在JS代码区域，创建Vue核心对象，进行数据绑定
​	new Vue({
​		el: "#app",
​		data() {
​			return {
​				username: ""
​			}
​		}
​	});
​	创建 Vue 对象时，需要传递一个 js 对象，而该对象中需要如下属性：
​	el ： 用来指定哪儿些标签受 Vue 管理。 该属性取值 #app 中的 app 需要是受管理的标签的id属性值
​	data ：用来定义数据模型
​	methods ：用来定义函数。这个我们在后面就会用到
​	③. 编写视图
	<div id="app">
	<input name="username" v-model="username" >
	{{username}}
	</div>
​	注意：{{}} 是 Vue 中定义的 插值表达式 ，在里面写数据模型，到时候会将该模型的数据值展示在这个位置。

3.常用指令
	指令：HTML 标签上带有 v- 前缀的特殊属性，不同指令具有不同含义。例如：v-if，v-for…
	常用的指令：①v-bind：为HTML标签绑定属性值，如设置 href , css样式等
	②v-model：在表单元素上创建双向数据绑定
	③v-on 为HTML标签绑定事件
	④v-if 条件性的渲染某元素，判定为true时渲染,否则不渲染
	⑤v-else
	⑥v-else-if
	⑦v-show 根据条件展示某元素，区别在于切换的是display属性的值
	⑧v-for 列表渲染，遍历容器的元素或者对象的属性

4.生命周期
	mounted ：挂载完成，Vue初始化成功，HTML页面渲染成功。而以后我们会在该方法中发送异步请求，加载数据。



Element知识点
网址：https://element.eleme.cn/#/zh-CN
1.概念：一套基于 Vue 的网站组件库，用于快速构建网页。
2.快速入门：
	①将资源 04-资料\02-element 下的 element-ui 文件夹直接拷贝到项目的 webapp 下
	②创建页面，并在页面引入Element 的css、js文件 和 Vue.js
	<script src="vue.js"></script>
	<script src="element-ui/lib/index.js"></script>
​	<link rel="stylesheet" href="element-ui/lib/theme-chalk/index.css">
​	③创建Vue核心对象
​	Element 是基于 Vue 的，所以使用Element时必须要创建 Vue 对象

	<script>
		new Vue({
			el:"#app"
		})
	</script>
​	④官网复制Element组件代码

3.Element 布局
	①Layout 布局
	通过基础的 24 分栏，迅速简便地创建布局。也就是默认将一行分为 24 栏，根据页面要求给每一列设置所占的栏数。
	②Container 布局容器
