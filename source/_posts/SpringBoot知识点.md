---
title: SpringBoot知识点
date: 2022-10-12 21:36:11
cover: ./img/springBoot.png
tags: Java
categories: java
description: 学习一下boot框架的强大
---





#### 入门

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

	小知识：每次创建一个boot工程会有许多暂时用不到的，可以删除也可以在idea设置中的editor中的File types添加进去进行隐藏即可。

2.快速启动
	直接打包，然后用cmd，输入java -jar 名字即可运行
	注意：jar支持命令行启动需要依赖maven插件支持，请确认打包时是否具有SpringBoot对应的maven插件


3.起步依赖
	SpringBoot优点：
		①自动配置。这个是用来解决 Spring 程序配置繁琐的问题
		②起步依赖。这个是用来解决 Spring 程序依赖设置繁琐的问题
		③辅助功能（内置服务器,...）。我们在启动 SpringBoot 程序时既没有使用本地的 tomcat 也没有使用 tomcat 插件，而是使用 SpringBoot 内置的服务器。

	parent：
		所有SpringBoot项目要继承的项目，定义了许多坐标版本号(依赖管理，而非依赖)，达到减少依赖冲突的目的
	注意：也可以不继承，比如阿里采用引入依赖的方式实现
	
	starter：
		起步依赖定义了当前项目使用的所有项目坐标，以达到减少依赖配置的目的，起步依赖通过starter来识别
	
	实际开发：
		①使用任意坐标的时候，只写G和A，版本由SprignBoot提供
		②如果发生坐标错误，在指定version(小心版本冲突)
		③Boot项目一般都是jar包
	
	小结：在boot项目开发时，如果某个不想用，直接用排除依赖，如把tomcat服务器换成jetty服务器，只需要现在starter-web里面添加排除依赖，然后另外在外面在添加jetty依赖即可，还有undertow服务器
	注意：内嵌Tomcat工作原理是将Tomcat服务器作为对象运行，并将该对象交给Spring容器管理
	
	内置服务器
		tomcat，apache出品，粉丝多，应用面广，负载了若干较重的组件
		jetty，更轻量级，负载性能远不及tomcat
		undertow，undertow，负载性能勉强跑赢tomcat
	
	配置完以后，要依靠引导类启动
	思考：不管什么框架写，最终都会产生一个spring容器的对象，他们都是以bean的形式被spring管理，springboot的引导类的run方法也是产生了一个ApplicationContext容器对象，也可以获取bean
	注意：引导类默认会扫描他的包下以及子包的类

4.REST风格
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
	如果在访问地址后面添加1表示查询id=1的，首先在参数中加上@PathVariable然后在路径中修改为/users/{id}
	注意：如果规定了请求方式，则只能用这种方式访问
	注解学习：①@RequestMapping，value表示访问路径，类上面的表示所有访问都加上此路径，method表示请求方法
			 ②@PathVariable主要让后端能够接收访问地址的参数
	
	关于接收参数，我们学过三个注解@RequestBody、@RequestParam、@PathVariable ,这三个注解之间的区别和应用分别是什么?
	区别
	@RequestParam用于接收url地址传参或表单传参
	@RequestBody用于接收json数据
	@PathVariable用于接收路径参数，使用{参数名称}描述路径参数
	应用
	①后期开发中，发送请求参数超过1个时，以json格式为主，@RequestBody应用较广
	②如果发送非json格式数据，选用@RequestParam接收请求参数
	③采用RESTful进行开发，当参数数量较少时，例如1个，可以采用@PathVariable接收请求路径变量，通常用于传递id值
	
	快速开发
	①把共同有的前缀直接写在类上加注解@RequestMapping的方式
	②如果所有方法都带@ResponseBody，直接在类上面写，并且@Controller和@ResponseBody可以合成@ResController
	③增删改查本来需要写@RequestMapping的method属性值，可以用@对应的请求方式注解代替原来的@RequestMapping
		如@DeleteMapping("/{id}")，@PostMapping等等


5.基础配置

	复制模块
		可以创建一个boot模板工程，只保留src和pom文件即可，如果要创建一个新的工程，只需要复制一个新的文件，然后给对应的名，并去pom文件修改工程名即可
	注意：再pom文件中那个name指的是maven那边显示的名字，一般都会删除


6.配置文件
	3种方式修改端口：
		①直接在resources下的application.properties文件进行修改server.port=8080端口即可
		②在resources下创建application.yml文件，然后根据格式修改即可
			server:
				port: 81	#切记后面有空格
		③同样的位置创键yaml文件，和第二种方法一样
	注意：如果在写yml配置文件没有出提示，那么在项目结构中点进去Facets中，点击Spring那个，然后点击大炮那个图标，把新建的配置文件填进去即可，如果没法添加，可以先把默认的加上在重新添加

	spring.main.banner-mode=off意思为关闭boot图标的输出
	spring.banner.image.location=图片的名字,可以识别图片并输出到控制台
	
	logging：level：root：info 是设置日志的等级(yml)
	logging.level.root=debug同上(默认)
	logging.level.包名=info为包设置日志等级
	注意：只有你导入了对应配置的依赖，它对应在配置文件中的配置才能起作用
	
	实际开发：
	用的最多的是yml文件配置形式，当3个都存在的时候，那么properties为主启动文件，他们的优先性为properties>yml>yaml
	注意：不同配置文件中相同配置按照加载优先级相互覆盖，不同配置文件中不同配置全部保留
	
	yaml格式
	概述：YAML（YAML Ain't Markup Language），一种数据序列化格式
	扩展形式：yml(主流)，yaml等
	内容如下：enterprise:
				name: itcast
				age: 16
				tel: 4006184000
			subject：
			 - 1
			 - 2	#用-表示的是一个数组，也可以用[]表示，也能用[{},{}]这种
	优点：①容易阅读
		②yaml 类型的配置文件比 xml 类型的配置文件更容易阅读，结构更加清晰
		③容易与脚本语言交互
		④以数据为核心，重数据轻格式
		yaml 更注重数据，而 xml 更注重格式
	
	语法规则：
		①大小写敏感
		②属性层级关系使用多行描述，每行结尾使用冒号结束
		③使用缩进表示层级关系，同层级左侧对齐，只允许使用空格（不允许使用Tab键）
		④空格的个数并不重要，只要保证同层级的左侧对齐即可。
		⑤属性值前面添加空格（属性名与属性值之间使用冒号+空格作为分隔）
		⑥# 表示注释
	核心规则：数据前面要加空格与冒号隔开
	
	yaml读取数据方式
	方式一：SpEl表达式
		在类中定义一个私有变量，并表上注解@Value("${名字}")取即可，层级之间用.即可，如user.name，数组用[]取
		注意：如果出现多个重复的，可以先定义一个如baseDir: C：\windows，然后在配置文件用${baseDir}调用即可。并且他读取默认不会转义，如果变为字符串会转义
	方式二：全部取走封装他里面
		用定义private Environment environment取走，并表上注解@Autowired，然后用该API的属性来取即可
	方式三：	部分取走然后封装
		在domin层创建对应的类，给出get set toSring方法，然后加上注解@Component受Spring控制，然后再加注解@ConfigurationProperties(prefix = "配置文件中的名字")，意思是读取哪一个写哪一个名字，然后在表现层想要使用直接创建对象自动装配即可，这个最重要
	注意：出现自定义对象封装数据警告解决方法，配置依赖
		<groupId>org.springframework.boot</groupId>
		<artifactId>spring-boot-configuration-processor</artifactId>
		<optional>true</optional>


7.Boot整合JUnit
	boot默认带有测试
	①创建boot工程，不需要添加web，因为这里用不到
	②直接在测试类里面先写上注解(SpringBootTest)，然后注入要测试的方法对应的对象，然后写方法并标上@Test即可

	在这里如果我们移动了测试类的位置，发现报错了，我们可以通过@SpringBootTest指明引导类的字节码对象也可以通过@ContextConfiguration来标注指明引导类，
	注意：在这里以前的配置其实现在的引导类已经帮我们全部做好了，所以他的扫描范围是com.yixin及其以下所有子包，因此test里面也需要在com.yixin下
	如果不满足这个要求的话，就需要在使用 @SpringBootTest 注解时，使用classes属性指定引导类的字节码对象。如@SpringBootTest(classes = Springboot07TestApplication.class)
	
	在这里我们使用spring正好Juint的时候，需要在类上标注@RunWith(设置运行器)，和@ContextConfiguration(指明配置类或者引导类的字节码)
	
	Spring和SpringMVC不用整合，因为不存在

8.Boot整合Mybatis
	思考要整合什么：
		①核心配置：数据库连接相关信息（连什么？连谁？什么权限）
		②映射配置：SQL映射（XML/注解）
	步骤：
		①创建工程选上MyBatis Framework和MySQL Driver
		②创建对应的domin(实体类)和dao数据层，并用注解形式@Mapper标在类上面
		③在application.yml配置数据库的信息
			spring:
				datasource:
				driver-class-name: com.mysql.cj.jdbc.Driver
				url: jdbc:mysql://localhost:3306/ssm_db
				username: root
				password: he020724
	注意：
		①如果不给数据层接口标注@Mapper注解，这个时候运行会报错，Spring扫描不上BookDao，因为他做了代理，需要给他做个注解@Mapper
		②如果选用boot2.4.2下面的低版本，需要设时区，在url后面加上?serverTimezone=UTC即可
		③如果要用druid数据源，先加依赖，然后在配置加type定义数据源

9.Boot整合MybatisPlus
	步骤：①添加MP的坐标或者用阿里云的boot直接创建即可
			<dependency>
				<groupId>com.baomidou</groupId>
				<artifactId>mybatis-plus-boot-starter</artifactId>
				<version>3.4.3</version>
			</dependency>
		 ②定义数据层接口与映射配置，继承BaseMapper，就算让dao层接口继承

10.Boot整合Druid
	坐标：
		<dependency>
			<groupId>com.alibaba</groupId>
			<artifactId>druid-spring-boot-starter</artifactId>
			<version>1.2.6</version>
		</dependency>
	方法一：直接在boot配置文件在数据库里面新加type属性输入type: com.alibaba.druid.pool.DruidDataSource即可
	方法二：使用druid专用配置格式
			spring:
			  datasource:
				druid:
				  driver-class-name: com.mysql.cj.jdbc.Driver
				  url: jdbc:mysql://localhost:3306/ssm_db?serverTimezone=UTC
				  username: root
			 	  password: he020724

11.Lobmbok加快开发
	依赖坐标：
		<groupId>org.projectlombok</groupId>
		<artifactId>lombok</artifactId>
		<version>1.18.12</version>
		<scope>provided</scope>
	注意：boot不用给出后两行

	Lombok常见的注解有:
		@Setter:为模型类的属性提供setter方法
		@Getter:为模型类的属性提供getter方法
		@ToString:为模型类的属性提供toString方法
		@EqualsAndHashCode:为模型类的属性提供equals和hashcode方法
		@Data:是个组合注解，包含上面的注解的功能
		@NoArgsConstructor:提供一个无参构造函数
		@AllArgsConstructor:提供一个包含所有参数的构造函数

12.开启MP的运行日志
	步骤：在boot配置文件
		mybatis-plus:
			configuration:
				log-impl: org.apache.ibatis.logging.stdout.StdOutImpl
	注意：StdOutImpl是标准输出


13.SSMP整合案例
	案例实现方案分析
		 实体类开发————使用Lombok快速制作实体类
		 Dao开发————整合MyBatisPlus，制作数据层测试类
		 Service开发————基于MyBatisPlus进行增量开发，制作业务层测试类
		 Controller开发————基于Restful开发，使用PostMan测试接口功能
		 Controller开发————前后端开发协议制作
		 页面开发————基于VUE+ElementUI制作，前后端联调，页面数据处理，页面消息处理
		 列表、新增、修改、删除、分页、查询
		 项目异常处理
		 按条件查询————页面功能调整、Controller修正功能、Service修正功能
	细节问题：
		①记得要在Service实现类加上@Service注解，接口不需要添加注解
		②记得在controller层标上rest风格@RestController，和各个方法的请求方式注解，还有参数的注解，如请求体参数@RequestBody或者@PathVariable
		③要保证表现层和业务层的方法参数一样
		④要注意让表现层的输出形式尽量保持一致，如统一为json格式，用data存数据，如果存在就返回数据，如果是增删改就返回true，没有就返回null
		⑤对异常也要做统一处理

	表现层消息一致性处理
	设计表现层返回结果的模型类，用于后端与前端进行数据格式统一，也称为前后端数据协议
	针对这次案例：
		①我们在工具层utils创建一个类，给出你想要返回数据设置的值如Boolean flag和Object data。给出对应的构造getset方法
		②然后在表现层修改，让他们都返回这个类的类型，然后可以直接返回new R(bookService.对应的方法),Object类型的值不给默认为null
	注意：在这里括号里面直接返回这个方法是因为他的返回值是Boolean，并且R中给出了对应的构造方法。


	业务层快速开发
	让业务层接口继承IService<Book>，同时让实现类继承ServiceImpl<BookDao,Book>和实现 IBookService接口
	它里面的方法：
		①removeById
		②list()：查询所有
		③updateById()
		④getById()
	注意：他里面封装了一些基本的增删改查的方法，可以直接调用，如果有一些其他业务里面没有，还是需要手工的去添加，回归到原始写法就好了，我们可以通过注解@Override是否报错来查看他提供的方法名重复了没有


	前端制作
	前后端分离结构设计中页面归属前端服务器
	单体工程中页面放置在resources目录下的static目录中（建议执行clean）
	
	细节：①关于钩子函数created(){},，他是随着页面加载自动执行的
		②关于axios异步请求，当他get方法完以后请求就已经发出去了，然后用then((res)=>{});去获取里面的数据
		③查询所有：在本课程里面通过页面代码发现datalist是展示数据的，因此需要在then里面进行复制this.datalist=res.data.data，通过res.data获取的数据,再通过控制台输出找到是在data里的data。
		④添加：关于新建数据，通过根据上面模型绑定找到对应的属性然后设置this.dialogFormVisible = true;打开窗口，再去找到确定按钮绑定的添加模型，然后发送post异步请求axios.post("/books",this.formData)，formData是通过上面找到确定对应的是他，然后判断是否成功做出相应的操作，最后添加完记得清楚数据
		this.$message.success("提示信息")
		⑤删除：删除要记得添加提示框this.$confirm("此操作是删除，是否继续"，"提示",{type:"info"}).then(()=>{"点确定执行这个"}).catch(()=>{点取消执行这个})
		⑥更新： 更新操作，先进行编辑窗口弹出进行回显
				    axios.get("/books/" + row.id).then((res) => {
	                //这里使用&&data不等于null是针对更新失败时的情况，就是两个人同时操作，一个已经删除了另一个点击编辑，他的flag是true，但是内容为null
	                if(res.data.flag && res.data.data != null){
	                    this.dialogFormVisible4Edit = true;
	                    //这里FormData是表示表单数据
	                    this.formData = res.data.data;
	                }
	    ⑦分页制作：
	    	1.页面使用el分页组件添加分页功能，先去查看对应的属性
	    	2.定义分页组件需要使用的数据并将数据绑定到分页组件
	    	3.替换查询全部功能为分页功能
	    	4.分页查询(顺便修改在最后一页删除数据后的显示bug)
	    		@GetMapping("/{currentPage}/{pageSize}")
				public R getAll(@PathVariable Integer currentPage,@PathVariable Integer pageSize){
					IPage<Book> pageBook = bookService.getPage(currentPage, pageSize);
				return new R(null != pageBook ,pageBook);
				}
			注意：使用路径参数传递分页数据或封装对象传递数据
				5.加载分页数据，也就是在html中写getall
				6.分页页码值切换
					this.pagination.currentPage = currentPage;
					this.getAll();
		⑧条件查询：进去查看el的以后，发现他们没有绑定数据模型，这时有两种方法，一是定义数据模型，二是在原有的模型加上去，这里选的是后者，同时绑定数据模型，然后重新去写getAll
			步骤：1.加入数据，页面数据模型绑定
				 2.组织数据成为get请求发送的数据
				 3.Controller接收参数(直接用book接收)
				 4.业务层接口功能开发，先在接口创建对应的参数的方法，然后在实现类重新编写条件
				 5.Controller调用业务层分页条件查询接口
				 6.页面回显数据
	
	注意：在axios中和try catch作用一样叫做.finally(()=>{});
		scope是整个行对象，scope.row是指这一行的数据


	对异常做统一处理
	①创建类，在SpringMVC中给出了统一异常处理的，springmvc属于表现层的，所有这个处理类就放在表现层的utils中，如创建ProjectExceptionAdvice，
	②并标上@ControllerAdvice注解或者@RestControllerAdvice，
	③创建拦截所有异常的方法，且在R中添加一个msg属性表示消息
			@ExceptionHandler(Exception.class)
			public R doOtherException(Exception ex){
			//记录日志
			//发送消息给运维
			//发送邮件给开发人员,ex对象发送给开发人员
			ex.printStackTrace();
			return new R(false,null,"系统错误，请稍后再试！");
			}
	注意：@ExceptionHandler(Exception.class)是可以指定不同异常的
	
	这个时候会发现消息的发送既有前端的也有后端的，在这里我们使用都在后端发送
		直接在对应的表现层进行修改，使用一个三元运算符
		boolean flag = bookService.save(book);
	    return new R(flag,flag ? "添加成功^_^！" : "添加失败了-_-！");



#### SpringBoot运维实用篇

1.  ①Windows打包与运行
		boot中，打包是直接在maven中的lifecycle生命周期中先进行clean，然后双击package打包即可(他会执行test)，然后他会生成一个tagfet文件，里面会有jar包，进入cmd，输入java -jar 名字进行运行。
		我们可以进行跳过测试，点击maven那个闪电的图标即可
	注意：黑框执行jar时，一定要在pom文件确认对springboot对应的maven插件依赖

	在黑框执行命令启动jar包时，有时会出现显示没有主清单属性报错。
	原因：如果没有用boot插件依赖，打包中MANIFEST.MF里面会少东西
		①Main-Class: org.springframework.boot.loader.JarLauncher
		②Start-Class: com.yixin.SSMPApplication
	Windonws端口被占用(黑框)
		# 查询端口
		netstat -ano
		# 查询指定端口
		netstat -ano |findstr "端口号"
		# 根据进程PID查询进程名称
		tasklist |findstr "进程PID号"
		# 根据PID杀死任务
		taskkill /F /PID "进程PID号"
		# 根据进程名称杀死任务
		taskkill -f -t -im "进程名称"

	②Linux打包与运行(重要)
	环境：
		基于Linux（CenterOS7）
		安装JDK和Mysql，且版本不低于打包时使用的JDK版本
		安装包保存在/usr/local/自定义目录中或$HOME下
	一般文件都会放在usr下的local目录下，在这里我们创建app目录，并附上权限，将jar包传进去，然后java -jar 文件名字启动即可。
	注意：记得把端口填入白名单，并且防火墙要关闭，并且开启mysql的远程连接

	实际开发
	在实际中，他一般都是后台启动，命令为nohup java -jar 名字 > server.log 2>&1 &即可
	关闭：ps -ef | grep "java -jar"查看pid
		然后kill -9 pid杀掉即可
		用cat server.log查看


2.临时属性的设置
	在使用java -jar的命令启动时，在后面用--加临时属性，如--server.port=80
	在这里我们使用properties的方式写，可以用--打多个临时属性

	在idea中(不建议)
	我们可以带属性启动SpringBoot程序，为程序添加运行属性，在启动哪里，对启动进行配置Program arguments即可，他和引导类中args的参数有关系，他生效的原因就是因为临时属性会放在args中，可以用toString(args)进行输出查看，也可以不带args进行运行


	当测试临时需要设置的属性太多的时候，可以在resources下直接创建一个config目录，创建一个yml文件，给出所需的临时属性，并且他们是合作，不重复的会合并，重复的遵循优先级
	在这里优先级config目录>jar包同一层级的yml文件>大于工程内部的配置
	结论：类(resource)路径下的config下的配置文件优先于类路径下的配置文件。
		 file：config下的配置文件优先于类路径下的配置文件。
	
	自定义配置文件
	方法一：在idea中进行临时属性配置：--spring.config.name=配置文件名字(不带后缀)指明用哪个配置文件，--spring.config.location=classpath:/ebank.yml这是使用路径，当配置多个的时候，第一个优先级最高
	
	自定义配置文件——重要说明
		单服务器项目：使用自定义配置文件需求较低
		多服务器项目：使用自定义配置文件需求较高，将所有配置放置在一个目录中，统一管理
		基于SpringCloud技术，所有的服务器将不再设置配置文件，而是通过配置中心进行设定，动态加载配置信息


3.多坏境开发配置
	在yml配置多环境
	①首先在yml配置文件中将各个环境之间用---隔开
	②然后在坏境里面给出Spring：profiles：名字(这种是过时的)，分别对应dev，pro，test
	新版为spring: config: activate: on-profile: 名字
	③在最上面指定使用哪个环境，spring：profiles：active：名字即可
	注意：一般会把公共的配置写在最上面，这样维护比较方便，同时yml文件一级一级要用回车隔开

	yml文件版的多环境
	①首先再创建出3个yml配置文件，分别对应dev，pro，test和最原始的主启动类，命名为原始文件名字-dev等
	②然后在主启动类规定哪个环境即可
	注意：主配置文件中设置公共配置（全局）
		  环境分类配置文件中常用于设置冲突属性（局部）


	在properties(了解即可)
	①创建主配置application.properties，然后spring.profiles.active=dev，指明用哪个
	②创建其他环境配置如application-dev.properties
	
	命令行启动多坏境
	①先去setting里面给encoding里面全部设置成utf8
	②打包前，先进行clean，然后打包
	③ java –jar xxx.jar –-spring.profiles.active=test即可
	也能临时改端口在后面--server.port=88等
	
	多环境开发独立配置文件书写技巧
	①主配置文件中设置公共配置（全局）
	②环境分类配置文件中常用于设置冲突属性（局部）
	③根据功能对配置文件中的信息进行拆分，并制作成独立的配置文件，命名规则如下
		application-devDB.yml
		application-devRedis.yml
		application-devMVC.yml
	④使用include属性在激活指定环境的情况下，同时对多个环境进行加载使其生效，多个环境间使用逗号分隔
		spring:
			profiles:
				active: dev
				include: devDB,devRedis,devMVC
	注意：当主环境dev与其他环境有相同属性时，主环境属性生效；其他环境中有相同属性时，最后加载的环境属性生效
	并且include的用法是在boot2.4之前，现在是group
		spring:
			profiles:
				active: dev
				group:
					"dev": devDB,devRedis,devMVC
					"pro": proDB,proRedis,proMVC
					"test": testDB,testRedis,testMVC
	注意：在这里主环境的加载在最前面


	maven与boot多坏境兼容问题
	一般是让boot遵从maven来，如何让maven控制boot环境
	①首先在maven多环境中给出profiles下的profile下的properties的profile.active标签给出用哪个环境
	②编辑maven-resources-plugin插件把范围改为全局
		给出<configuration>标签修改<useDefaulDelimiters>为true，同时给出encoding为utf-8即可
	③在yml文件里面active修改为${profile.active}或@rofile.active@
	④执行Maven打包指令，并在生成的boot打包文件.jar文件中查看对应信息
	注意：当你想要在maven切换环境的时候，并且直接启动的时候，一定要compile一下和刷新
	
		<profiles>
			<profile>
				<id>dev_env</id>
				<properties>
					<profile.active>dev</profile.active>
				</properties>
				<activation>
					//这个是设置哪一个为默认的环境
					<activeByDefault>true</activeByDefault>
				</activation>
			</profile>
			<profile>
				<id>pro_env</id>
				<properties>
					<profile.active>pro</profile.active>
				</properties>
			</profile>
			<profile>
				<id>test_env</id>
				<properties>
					<profile.active>test</profile.active>
				</properties>
			</profile>
		</profiles>
	
	注意：boot2.5版本有一个bug，就是config必须要有子目录，给他一个就好


4.日志
	作用：
		①编程期调试代码
		②运营期记录信息
	代码中使用日志工具记录日志步骤：
		①添加日志记录操作(slf4j)
			@RestController
			@RequestMapping("/books")
			public class BookController extends BaseController {
				//创建记录日志的对象
				private static final Logger log = LoggerFactory.getLogger(BookController.class);
				@GetMapping
				public String getById(){
					System.out.println("springboot is running...");
					log.debug("debug ...");
					log.info("info ...");
					log.warn("warn ...");
					log.error("error ...");
					return "springboot is running...";
				}
			}
		②在这里可能日志等级没有配置，3种方式修改，直接在启动配置里面给出临时属性--debug，或者在yml配置文件给出debug：true，
		或者在配置文件种配置他是设置日志级别，root表示根节点，即整体应用日志级别
			logging:
				level:
					root: debug
		③设置日志组，控制指定包对应的日志输出级别，也可以直接控制指定包对应的日志输出级别
			logging:
				# 设置日志组
				group:
					# 自定义组名，设置当前组中所包含的包
					ebank: com.itheima.controller
				level:
					root: warn
					# 为对应组设置日志级别
					ebank: debug
					# 为对包设置日志级别
					com.itheima.controller: debug
	在这里遇到一个小问题，创建日志对象需要大量重复，在这里进行解决，我们直接在类上面加上注解@Slf4j，默认创建的对象为Log


	日志输出控制
	设置日志输出格式
		logging:
			pattern:
				console: "%d - %m%n"
		%d：日期
		%m：消息
		%n：换行

5.日志以文件的形式保存
	设置日志文件
	logging:
		file:
			name: server.log
	日志文件详细配置
	logging:
		file:
			name: server.log
		logback:
			rollingpolicy:
				max-file-size: 10MB
				file-name-pattern: server.%d{yyyy-MM-dd}.%i.log
	注意:%d是设置日期，%i表示序号



#### SpringBoot开发实用篇(整合第三方技术)

1.热部署
	概念：什么是热部署？简单说就是你程序改了，现在要重新启动服务器，嫌麻烦？不用重启，服务器会自己悄悄的把更新后的程序给重新加载一遍，这就是热部署。

	原因：tomcat是boot工程中一个内置的服务器，他受spring的管制，因此我们想要让他监督我们东西变没变化，需要在容器内建立一下程序去监管他并随之更新，接下来我们研究如何做这样的程序
	步骤：
		①导入开发者工具对应的坐标
			<dependency>
			    <groupId>org.springframework.boot</groupId>
			    <artifactId>spring-boot-devtools</artifactId>
			    <optional>true</optional>
			</dependency>
		②构建项目，可以使用快捷键激活此功能CTRL+<F9>
	注意：在这里热部署相当于只是重启了一下，即restart不包括reload
	restart类加载器：用来加载开发者自己开发的类、配置文件、页面等信息，这一类文件受开发者影响
	reload类加载器：用来加载jar包中的类，jar包中的类和配置文件由于不会发生变化，因此不管加载多少次，加载的内容不会发生变化
	
	自动开启bulid：在setting中的bulid中的compiler里面有个Bulid project auto的勾上以后并把advanced setting里面的第一个勾上即可
	
	在这里我们会发现当idea工具失去焦点5秒后进行热部署，并且静态页面是不参与热部署的，而配置文件又参与了热部署
	
	控制热部署的区域
	在boot配置文件中进行设置
		spring:
		  devtools:
		    restart:
		      # 设置不参与热部署的文件或文件夹
		      exclude: static/**,public/**,config/application.yml
	线上环境运行时是不可能使用热部署功能的，所以需要强制关闭此功能，通过配置可以关闭此功能。
		spring:
		  devtools:
		    restart:
		      enabled: false
	注意：在这里出现问题或许是因为配置优先级的缘故


2.关于@ConfigurationProperties注解
	作用：此注解的作用是用来为bean绑定属性的。它既可以给自己的bean绑定属性，也可以为第三方bean绑定
	简单使用：
	①开发者可以在yml配置文件中以对象的格式添加若干属性
		如servers:
		  ip-address: 192.168.0.1 
		  port: 2345
		  timeout: -1
	②然后再开发一个用来封装数据的实体类，注意要提供属性对应的setter方法
	@Component
	@Data
	public class ServerConfig {
	    private String ipAddress;
	    private int port;
	    private long timeout;
	}
	③使用@ConfigurationProperties注解就可以将配置中的属性值关联到开发的模型类上
	@ConfigurationProperties(prefix = "servers")
	注意：在这里有个@EnableConfigurationProperties，它相当于起到一个开关的作用，他写在引导类中，告诉boot谁在配置中读取属性，在他括号里用一个数组写谁用了这个注解如ServerConfig.class，同时ServerConfig就不用在写@Compoent注解(因为他写在括号里已经自动帮他注册为一个bean了)，但是@ConfigurationProperties不能省
	记得在添加一组坐标防止报错：
		<dependency>
		    <groupId>org.springframework.boot</groupId>
		    <artifactId>spring-boot-configuration-processor</artifactId>
		</dependency>

	松散绑定
	@ConfigurationProperties绑定属性支持属性名宽松绑定，但是其他的注解不支持如@Value

3.常用计量单位绑定
	两个JDK8中新增的类，分别是Duration和DataSize
		@Component
		@Data
		@ConfigurationProperties(prefix = "servers")
		public class ServerConfig {
		    @DurationUnit(ChronoUnit.HOURS)
		    private Duration serverTimeOut;
		    @DataSizeUnit(DataUnit.MEGABYTES)
		    private DataSize dataSize;
		}
	Duration：表示时间间隔，可以通过@DurationUnit注解描述时间单位，例如上例中描述的单位为小时（ChronoUnit.HOURS）
	DataSize：表示存储空间，可以通过@DataSizeUnit注解描述存储空间单位，例如上例中描述的单位为MB（DataUnit.MEGABYTES）


	数据校验
	步骤：
		①开启校验框架
			<!--1.导入JSR303规范-->
			<dependency>
			    <groupId>javax.validation</groupId>
			    <artifactId>validation-api</artifactId>
			</dependency>
			注意：这只是导入了接口需要实现类
			<!--使用hibernate框架提供的校验器做实现-->
			<dependency>
			    <groupId>org.hibernate.validator</groupId>
			    <artifactId>hibernate-validator</artifactId>
			</dependency>
		②在需要开启校验功能的类上使用注解@Validated开启校验功能
			//开启对当前bean的属性注入校验
			@Validated
			public class ServerConfig {
			}
		③对具体的字段设置校验规则
			@Validated
			public class ServerConfig {
			    //设置具体的规则
			    @Max(value = 8888,message = "最大值不能超过8888")
			    @Min(value = 202,message = "最小值不能低于202")
			    private int port;
			}


	开发遇到的问题数据类型转换
	在yml里面配置连接数据库的时候设置密码如果为数字一定要用引号括起来，因为他解析yml文件的时候支持8进制和16进制还有2进制。

4.测试
	加载测试专用属性
	方法一：
		在SpringBootTest中有一个属性叫做properties，在这里面可以用一个数组把临时属性填进去，他只针对当前测试类的测试环境，他的优先级大于全局配置
		如@SpringBootTest(properties = {"test.prop=testValue1"})
	方法二：
		在之前我们讲过在引导类中那个args对它进行操作也可以进行临时属性的添加，在这里也有这个args属性，使用如下
		@SpringBootTest(args={"--test.prop=testValue2"})
		他的优先级属于命令行那个，所以优先级更高

	加载测试专用配置
	他是相当于测试的时候，用了一些专门用于测试的bean辅助测试的，这个测试bean可以放在test里面，不用放在main里面
	步骤：
	①在这里我们创建一个第三方bean，用String类型返回(在这里是支持String的，只要是对象都可以被spring管理)
	②在启动测试环境时，导入测试环境专用的配置类，使用@Import注解即可实现
		@SpringBootTest
		@Import({MsgConfig.class})
		public class ConfigurationTest {
	
		    @Autowired
		    private String msg;
	
		    @Test
		    void testConfiguration(){
		        System.out.println(msg);
		    }
		}


5.Web环境模拟测试
	之前是在postman或者apifox测试的，在这里我们使用源码测试
	在SpringBootTest里面有一个属性叫做webEnvironment，他是开启web环境
	如@SpringBootTest(webEnvironment = SpringBootTest.WebEnvironment.RANDOM_PORT)建议这个
	其中：
		- MOCK：根据当前设置确认是否启动web环境，例如使用了Servlet的API就启动web环境，属于适配性的配置
		- DEFINED_PORT：使用自定义的端口作为web服务器端口
		- RANDOM_PORT：使用随机端口作为web服务器端口
		- NONE：不启动web环境

	测试类中发送请求
	在java中其实就有，boot对它进行了封装，使用步骤：
		①在测试类中开启web虚拟调用功能，通过注解@AutoConfigureMockMvc实现此功能的开启
			@SpringBootTest(webEnvironment = SpringBootTest.WebEnvironment.RANDOM_PORT)
			//开启虚拟MVC调用
			@AutoConfigureMockMvc
			public class WebTest {
			}
		②定义发起虚拟调用的对象MockMVC，通过自动装配的形式初始化对象
			@SpringBootTest(webEnvironment = SpringBootTest.WebEnvironment.RANDOM_PORT)
			//开启虚拟MVC调用
			@AutoConfigureMockMvc
			public class WebTest {
	
			    @Test
			    void testWeb(@Autowired MockMvc mvc) {
			    }
			}
		③创建一个虚拟请求对象，封装请求的路径，并使用MockMVC对象发送对应请求
			@SpringBootTest(webEnvironment = SpringBootTest.WebEnvironment.RANDOM_PORT)
			//开启虚拟MVC调用
			@AutoConfigureMockMvc
			public class WebTest {
	
			    @Test
			    void testWeb(@Autowired MockMvc mvc) throws Exception {
			        //http://localhost:8080/books
			        //创建虚拟请求，当前访问/books
			        MockHttpServletRequestBuilder builder = MockMvcRequestBuilders.get("/books");
			        //执行对应的请求
			        mvc.perform(builder);
			    }
			}
	
	匹配响应执行状态
	①响应状态匹配
		@Test
		void testStatus(@Autowired MockMvc mvc) throws Exception {
		    MockHttpServletRequestBuilder builder = MockMvcRequestBuilders.get("/books");
		    ResultActions action = mvc.perform(builder);
		    //设定预期值 与真实值进行比较，成功测试通过，失败测试失败
		    //定义本次调用的预期值
		    StatusResultMatchers status = MockMvcResultMatchers.status();
		    //预计本次调用时成功的：状态200
		    ResultMatcher ok = status.isOk();
		    //添加预计值到本次调用过程中进行匹配
		    action.andExpect(ok);
		}
	②响应体匹配（非json数据格式）
		@Test
		void testBody(@Autowired MockMvc mvc) throws Exception {
		    MockHttpServletRequestBuilder builder = MockMvcRequestBuilders.get("/books");
		    ResultActions action = mvc.perform(builder);
		    //设定预期值 与真实值进行比较，成功测试通过，失败测试失败
		    //定义本次调用的预期值
		    ContentResultMatchers content = MockMvcResultMatchers.content();
		    ResultMatcher result = content.string("springboot2");
		    //添加预计值到本次调用过程中进行匹配
		    action.andExpect(result);
		}
	③响应体匹配（json数据格式，开发中的主流使用方式）
		@Test
		void testJson(@Autowired MockMvc mvc) throws Exception {
		    MockHttpServletRequestBuilder builder = MockMvcRequestBuilders.get("/books");
		    ResultActions action = mvc.perform(builder);
		    //设定预期值 与真实值进行比较，成功测试通过，失败测试失败
		    //定义本次调用的预期值
		    ContentResultMatchers content = MockMvcResultMatchers.content();
		    ResultMatcher result = content.json("{\"id\":1,\"name\":\"springboot2\",\"type\":\"springboot\"}");
		    //添加预计值到本次调用过程中进行匹配
		    action.andExpect(result);
		}
	④响应头信息匹配
		@Test
		void testContentType(@Autowired MockMvc mvc) throws Exception {
		    MockHttpServletRequestBuilder builder = MockMvcRequestBuilders.get("/books");
		    ResultActions action = mvc.perform(builder);
		    //设定预期值 与真实值进行比较，成功测试通过，失败测试失败
		    //定义本次调用的预期值
		    HeaderResultMatchers header = MockMvcResultMatchers.header();
		    ResultMatcher contentType = header.string("Content-Type", "application/json");
		    //添加预计值到本次调用过程中进行匹配
		    action.andExpect(contentType);
		}
	⑤完整的信息匹配过程
		@Test
		void testGetById(@Autowired MockMvc mvc) throws Exception {
		    MockHttpServletRequestBuilder builder = MockMvcRequestBuilders.get("/books");
		    ResultActions action = mvc.perform(builder);
	
		    StatusResultMatchers status = MockMvcResultMatchers.status();
		    ResultMatcher ok = status.isOk();
		    action.andExpect(ok);
	
		    HeaderResultMatchers header = MockMvcResultMatchers.header();
		    ResultMatcher contentType = header.string("Content-Type", "application/json");
		    action.andExpect(contentType);
	
		    ContentResultMatchers content = MockMvcResultMatchers.content();
		    ResultMatcher result = content.json("{\"id\":1,\"name\":\"springboot\",\"type\":\"springboot\"}");
		    action.andExpect(result);
		}

6.业务层测试事务回滚
	因为在进行打包的时候，测试中我们会产生许多无用的数据，在这里我们想让该执行的执行，但是不要留下数据在数据库。
	步骤：
		①在测试类中加上注解@Transactional
	注解解释：当程序运行后，只要注解@Transactional出现的位置存在注解@SpringBootTest，springboot就会认为这是一个测试程序，无需提交事务，所以也就可以避免事务的提交。
	注意：如果开发者想提交事务，也可以，再添加一个@RollBack的注解，设置回滚状态为false即可正常提交事务

7.测试用例设置随机数据
	使用原因：为了保证测试的权威性，一般我们用随机数据
	步骤：
		①在yml文件里面进行配置(在实际中应该写在测试环境中的配置)
			testcase:
			  book:
			    id: ${random.int}
			    //id2: ${random.int(10)}
			    //type: ${random.int(5,10)}
			    name: ${random.value}
			    uuid: ${random.uuid}
			    publishTime: ${random.long}
		②在test中创建domin，数据的加载按照之前加载数据的形式，使用@ConfigurationProperties注解即可
			@Component
			@Data
			@ConfigurationProperties(prefix = "testcase.book")
			public class BookCase {
			    private int id;
			    private int id2;
			    private int type;
			    private String name;
			    private String uuid;
			    private long publishTime;
			}

8.数据层解决方案
	我们一直使用的技术是Mysql+Druid+MyBatisPlus，分别对应
	- 数据源技术：Druid
	- 持久化技术：MyBatisPlus
	- 数据库技术：MySQL


	关于数据源Hikari
	在这里如果不使用druid(依赖也要去掉不然还是会被boot自动装配为druid)，他自己默认的是Hikari数据源
	springboot提供了3款内嵌数据源技术，分别如下：
		- HikariCP	他速度快，轻量级
		- Tomcat提供DataSource	如果第一个不可用，并且在web环境可以用这个
		- Commons DBCP	这个是最后的选择
	
	hikari数据源配置方式：
		spring:
		  datasource:
		    url: jdbc:mysql://localhost:3306/ssm_db?serverTimezone=UTC
		    hikari:
		      driver-class-name: com.mysql.cj.jdbc.Driver
		      username: root
		      password: he020724
		      maximum-pool-size: 50


	关于持久化技术jdbcTemplate
	概述：这个技术其实就是回归到jdbc最原始的编程形式来进行数据层的开发
	步骤：
		①导入jdbc对应的坐标，记得是starter
			<dependency>
			    <groupId>org.springframework.boot</groupId>
			    <artifactId>spring-boot-starter-jdbc</artifactId>
			</dependency
		②自动装配JdbcTemplate对象
			@SpringBootTest
			class Springboot15SqlApplicationTests {
			    @Test
			    void testJdbcTemplate(@Autowired JdbcTemplate jdbcTemplate){
			    }
			}
		③使用JdbcTemplate实现查询操作（非实体类封装数据的查询操作）
			@Test
			void testJdbcTemplate(@Autowired JdbcTemplate jdbcTemplate){
			    String sql = "select * from tbl_book";
			    List<Map<String, Object>> maps = jdbcTemplate.queryForList(sql);
			    System.out.println(maps);
			}
		④使用JdbcTemplate实现查询操作（实体类封装数据的查询操作）
			@Test
			void testJdbcTemplate(@Autowired JdbcTemplate jdbcTemplate){
	
			    String sql = "select * from tbl_book";
			    RowMapper<Book> rm = new RowMapper<Book>() {
			        @Override
			        public Book mapRow(ResultSet rs, int rowNum) throws SQLException {
			            Book temp = new Book();
			            temp.setId(rs.getInt("id"));
			            temp.setName(rs.getString("name"));
			            temp.setType(rs.getString("type"));
			            temp.setDescription(rs.getString("description"));
			            return temp;
			        }
			    };
			    List<Book> list = jdbcTemplate.query(sql, rm);
			    System.out.println(list);
			}
		⑤使用JdbcTemplate实现增删改操作
			@Test
			void testJdbcTemplateSave(@Autowired JdbcTemplate jdbcTemplate){
			    String sql = "insert into tbl_book values(3,'springboot1','springboot2','springboot3')";
			    jdbcTemplate.update(sql);
			}
	如果想对JdbcTemplate对象进行相关配置，可以在yml文件中进行设定
		spring:
		  jdbc:
		    template:
		      query-timeout: -1   # 查询超时时间
		      max-rows: 500       # 最大行数
		      fetch-size: -1      # 缓存行数
	
	关于数据库技术
	springboot提供了3款内置的数据库，分别是
		- H2
		- HSQL
		- Derby
	使用原因：他们可以内嵌在容器中运行，且都是java开发的，优点方便进行功能测试。
	在这里我们使用H2，步骤：
		①导入H2数据库对应的坐标，一共2个
			<dependency>
			    <groupId>com.h2database</groupId>
			    <artifactId>h2</artifactId>
			</dependency>
			<dependency>
			    <groupId>org.springframework.boot</groupId>
			    <artifactId>spring-boot-starter-data-jpa</artifactId>
			</dependency>
		②将工程设置为web工程，启动工程时启动H2数据库
			<dependency>
			    <groupId>org.springframework.boot</groupId>
			    <artifactId>spring-boot-starter-web</artifactId>
			</dependency>
		③通过配置开启H2数据库控制台访问程序，也可以使用其他的数据库连接软件操作
			spring:
			  h2:
			    console:
			      enabled: true
			      path: /h2
		注意：web端访问路径/h2，访问密码123456，如果访问失败，先配置下列数据源，启动程序运行后再次访问/h2路径就可以正常访问了
			datasource:
			  url: jdbc:h2:~/test
			  hikari:
			    driver-class-name: org.h2.Driver
			    username: sa
			    password: 123456
		④使用JdbcTemplate或MyBatisPlus技术操作数据库
		可以直接进行使用，注意表明创建一样即可。
	注意：上线后一定要关闭这个，H2数据库控制台仅用于开发阶段

9.NoSQL解决方案
	市场上常见的NoSQL解决方案
		Redis
		Mongo
		ES
	注意：他们一般都在linux系统中，在这里我们使用windows

	Redis
	概述：Redis是一款采用key-value数据存储格式的内存级NoSQL数据库
	特点：支持多种数据存储格式，支持持久化，支持集群
	
	boot整合Redis
	步骤：
		①导入springboot整合redis的starter坐标
			<dependency>
			    <groupId>org.springframework.boot</groupId>
			    <artifactId>spring-boot-starter-data-redis</artifactId>
			</dependency>
		②在yml文件进行基础配置
			spring:
			  redis:
			    host: localhost
			    port: 6379
		③使用springboot整合redis的专用客户端接口操作，此处使用的是RedisTemplate
			@SpringBootTest
			class Springboot16RedisApplicationTests {
			    @Autowired
			    private RedisTemplate redisTemplate;
			    @Test
			    void set() {
			        ValueOperations ops = redisTemplate.opsForValue();
			        ops.set("age",41);
			    }
			    @Test
			    void get() {
			        ValueOperations ops = redisTemplate.opsForValue();
			        Object age = ops.get("name");
			        System.out.println(age);
			    }
			    @Test
			    void hset() {
			        HashOperations ops = redisTemplate.opsForHash();
			        ops.put("info","b","bb");
			    }
			    @Test
			    void hget() {
			        HashOperations ops = redisTemplate.opsForHash();
			        Object val = ops.get("info", "b");
			        System.out.println(val);
			    }
			}
	注意：在这里我们发现，前面在黑框那边设置的值这边取不到，这边的值黑框客户端那边也取不到
	在这里我们使用StringRedisTemplate可以操作客户端
		@SpringBootTest
		public class StringRedisTemplateTest {
		    @Autowired
		    private StringRedisTemplate stringRedisTemplate;
		    @Test
		    void get(){
		        ValueOperations<String, String> ops = stringRedisTemplate.opsForValue();
		        String name = ops.get("name");
		        System.out.println(name);
		    }
		}
	注意：这样两者是同步的，RedisTemplate是以对象作为ket和value，内部对数据进行序列化，而StringRedisTemplate是以字符串作为ket和value，与Redis客户端操作等效
	
	客户端选择
	默认提供的是lettucs客户端技术，可以根据需要使用jedis
	使用步骤：
		①导入jedis坐标
			<dependency>
			    <groupId>redis.clients</groupId>
			    <artifactId>jedis</artifactId>
			</dependency>
		//jedis坐标受springboot管理，无需提供版本号
		②配置客户端技术类型，设置为jedis
			spring:
			  redis:
			    host: localhost
			    port: 6379
			    client-type: jedis
		③根据需要设置对应的配置
			spring:
			  redis:
			    host: localhost
			    port: 6379
			    client-type: jedis
			    lettuce:
			      pool:
			        max-active: 16
			    jedis:
			      pool:
			        max-active: 16
	
	lettcue与jedis区别
	①jedis连接Redis服务器是直连模式，当多线程模式下使用jedis会存在线程安全问题，解决方案可以通过配置连接池使每个连接专用，这样整体性能就大受影响
	②lettcus基于Netty框架进行与Redis服务器连接，底层设计中采用StatefulRedisConnection。 StatefulRedisConnection自身是线程安全的，可以保障并发访问安全问题，所以一个连接可以被多线程复用。当然lettcus也支持多连接实例一起工作


	Mongodb
	概述：MongoDB是一个开源、高性能、无模式(没有固定的数据存储结构)的文档型数据库，它是NoSQL数据库产品中的一种，是最像关系型数据库的非关系型数据库
	适用场景：
		- 淘宝用户数据
		  - 存储位置：数据库
		  - 特征：永久性存储，修改频度极低
		- 游戏装备数据、游戏道具数据
		  - 存储位置：数据库、Mongodb
		  - 特征：永久性存储与临时存储相结合、修改频度较高
		- 直播数据、打赏数据、粉丝数据
		  - 存储位置：数据库、Mongodb
		  - 特征：永久性存储与临时存储相结合，修改频度极高
		- 物联网数据
		  - 存储位置：Mongodb
		  - 特征：临时存储，修改频度飞速
	boot整合Mongodb
	步骤：
		①导入springboot整合MongoDB的starter坐标
			<dependency>
			    <groupId>org.springframework.boot</groupId>
			    <artifactId>spring-boot-starter-data-mongodb</artifactId>
			</dependency>
		②在yml文件进行基础配置
			spring:
			  data:
			    mongodb:
			      uri: mongodb://localhost/itheima
		注意：操作MongoDB需要的配置与操作redis一样，最基本的信息都是操作哪一台服务器，区别就是连接的服务器IP地址和端口不同，书写格式不同而已。
		③使用springboot整合MongoDB的专用客户端接口MongoTemplate来进行操作
			@SpringBootTest
			class Springboot17MongodbApplicationTests {
			    @Autowired
			    private MongoTemplate mongoTemplate;
			    @Test
			    void contextLoads() {
			        Book book = new Book();
			        book.setId(2);
			        book.setName("springboot2");
			        book.setType("springboot2");
			        book.setDescription("springboot2");
			        mongoTemplate.save(book);
			    }
			    @Test
			    void find(){
			        List<Book> all = mongoTemplate.findAll(Book.class);
			        System.out.println(all);
			    }
			}


	ES
	概述：ES（Elasticsearch）是一个分布式全文搜索引擎，重点是全文搜索。
	作用：加速数据的查询
	思想：具体操作过程如下
		①将被查询的字段的数据全部文本信息进行查分，分成若干个词
		②将分词得到的结果存储起来，对应每条数据的id
		③当进行查询时，如果输入“人民”作为查询条件，可以通过上述表格数据进行比对，得到id值1,2，然后根据id值就可以得到查询的结果数据了。
	在这里这种分词结果关键字起了一个全新的名称，叫做倒排索引
	使用去查看使用篇
	
	boot整合ES
	步骤：
		①导入springboot整合ES的starter坐标
			<dependency>
			    <groupId>org.springframework.boot</groupId>
			    <artifactId>spring-boot-starter-data-elasticsearch</artifactId>
			</dependency>
		②在yml进行基础配置
			spring:
			  elasticsearch:
			    rest:
			      uris: http://localhost:9200
		③使用springboot整合ES的专用客户端接口ElasticsearchRestTemplate来进行操作
			@SpringBootTest
			class Springboot18EsApplicationTests {
			    @Autowired
			    private ElasticsearchRestTemplate template;
			}
	注意：上述操作形式是ES早期的操作方式，使用的客户端被称为Low Level Client，这种客户端操作方式性能方面略显不足，于是ES开发了全新的客户端操作方式，称为High Level Client。高级别客户端与ES版本同步更新，但是springboot最初整合ES的时候使用的是低级别客户端，所以企业开发需要更换成高级别的客户端模式。
	步骤：
		①导入springboot整合ES高级别客户端的坐标，此种形式目前没有对应的starter
			<dependency>
			    <groupId>org.elasticsearch.client</groupId>
			    <artifactId>elasticsearch-rest-high-level-client</artifactId>
			</dependency>
		②使用编程的形式设置连接的ES服务器，并获取客户端对象
			@SpringBootTest
			class Springboot18EsApplicationTests {
			    private RestHighLevelClient client;
			      @Test
			      void testCreateClient() throws IOException {
			          HttpHost host = HttpHost.create("http://localhost:9200");
			          RestClientBuilder builder = RestClient.builder(host);
			          client = new RestHighLevelClient(builder);
			  
			          client.close();
			      }
			}
	注意：配置ES服务器地址与端口9200，记得客户端使用完毕需要手工关闭。由于当前客户端是手工维护的，因此不能通过自动装配的形式加载对象。
		③使用客户端对象操作ES，例如创建索引
			@SpringBootTest
			class Springboot18EsApplicationTests {
			    private RestHighLevelClient client;
			      @Test
			      void testCreateIndex() throws IOException {
			          HttpHost host = HttpHost.create("http://localhost:9200");
			          RestClientBuilder builder = RestClient.builder(host);
			          client = new RestHighLevelClient(builder);
			          
			          CreateIndexRequest request = new CreateIndexRequest("books");
			          client.indices().create(request, RequestOptions.DEFAULT);
			          
			          client.close();
			      }
			}
	注意：①高级别客户端操作是通过发送请求的方式完成所有操作的，ES针对各种不同的操作，设定了各式各样的请求对象，上例中创建索引的对象是CreateIndexRequest，其他操作也会有自己专用的Request对象。
	②当前操作我们发现，无论进行ES何种操作，第一步永远是获取RestHighLevelClient对象，最后一步永远是关闭该对象的连接。在测试中可以使用测试类的特性去帮助开发者一次性的完成上述操作，但是在业务书写时，还需要自行管理。将上述代码格式转换成使用测试类的初始化方法和销毁方法进行客户端对象的维护。
		@SpringBootTest
		class Springboot18EsApplicationTests {
		    @BeforeEach		//在测试类中每个操作运行前运行的方法
		    void setUp() {
		        HttpHost host = HttpHost.create("http://localhost:9200");
		        RestClientBuilder builder = RestClient.builder(host);
		        client = new RestHighLevelClient(builder);
		    }
	
		    @AfterEach		//在测试类中每个操作运行后运行的方法
		    void tearDown() throws IOException {
		        client.close();
		    }
	
		    private RestHighLevelClient client;
	
		    @Test
		    void testCreateIndex() throws IOException {
		        CreateIndexRequest request = new CreateIndexRequest("books");
		        client.indices().create(request, RequestOptions.DEFAULT);
		    }
		}
	后续再补.....



10.缓存(cache)
	场景：
		企业级应用主要作用是信息处理，当需要读取数据时，由于受限于数据库的访问效率，导致整体系统性能偏低。此时就出现在应用程序与数据库之间建立一种临时的数据存储机制，该区域中的数据在内存中保存，读写速度较快，可以有效解决数据库访问效率低下的问题。这一块临时存储数据的区域就是缓存。
	概述：
		缓存是一种介于数据永久存储介质与应用程序之间的数据临时存储介质
	作用：
		使用缓存可以有效的减少低速数据读取过程的次数（例如磁盘IO），提高系统性能。
		还可以提供临时的数据存储空间

	简单模拟缓存：
		①在业务实现层进行创建一个HashMap<Integer,Book>集合，在进行查的时候先判断集合内是否包含有，如果没有则进行查询同时把这给实体数据put到集合中。
		②还有一些临时数据，并不需要存储，比如验证码登录，我们就可以创建一个HashMap集合去存储根据手机号获取验证码的code，这样就是实现了开辟临时空间
	
	boot内部缓存实现：
		①导入springboot提供的缓存技术对应的starter
			<dependency>
			    <groupId>org.springframework.boot</groupId>
			    <artifactId>spring-boot-starter-cache</artifactId>
			</dependency>
		②启用缓存，在引导类上方标注注解@EnableCaching配置springboot程序中可以使用缓存
			@SpringBootApplication
			//开启缓存功能
			@EnableCaching
			public class Springboot19CacheApplication {
			    public static void main(String[] args) {
			        SpringApplication.run(Springboot19CacheApplication.class, args);
			    }
			}
		③设置操作的数据是否使用缓存
			@Service
			public class BookServiceImpl implements BookService {
			    @Autowired
			    private BookDao bookDao;
	
			    //这个key也就是说你下次找用什么找，value是存储空间名字
			    @Cacheable(value="cacheSpace",key="#id")
			    public Book getById(Integer id) {
			        return bookDao.selectById(id);
			    }
			}
		注意：@Cacheable注解，value相当于是存储空间名字，key也就是说你用什么去查询他。他有一个存和取的过程，但是验证码案例只需要存不需要取在这里用@CachePut注解，他的属性和上面一样。boot默认的缓存方案是simple


​		
​	使用boot内部缓存实现验证码案例：
​		①导入坐标
​		②启用缓存
​		③定义验证码对应的实体类，封装手机号与验证码两个属性
​			@Data
​			public class SMSCode {
​			    private String tele;
​			    private String code;
​			}
​		④定义验证码功能的业务层接口与实现类
​			public interface SMSCodeService {
​			    public String sendCodeToSMS(String tele);
​			    public boolean checkCode(SMSCode smsCode);
​			}
​	
			@Service
			public class SMSCodeServiceImpl implements SMSCodeService {
			    @Autowired
			    private CodeUtils codeUtils;
	
			    @CachePut(value = "smsCode", key = "#tele")
			    public String sendCodeToSMS(String tele) {
			        String code = codeUtils.generator(tele);
			        return code;
			    }
	
			    public boolean checkCode(SMSCode smsCode) {
			        //取出内存中的验证码与传递过来的验证码比对，如果相同，返回true
			        String code = smsCode.getCode();
			        String cacheCode = codeUtils.get(smsCode.getTele());
			        return code.equals(cacheCode);
			    }
			}
		注意：在这里注意启用的是@CachePut注解，因为验证码只需要缓存
		⑤生成验证码(加密)
			@Component
			public class CodeUtils {
			    private String [] patch = {"000000","00000","0000","000","00","0",""};
	
			    public String generator(String tele){
			        int hash = tele.hashCode();
			        int encryption = 20206666;
			        long result = hash ^ encryption;
			        long nowTime = System.currentTimeMillis();
			        result = result ^ nowTime;
			        long code = result % 1000000;
			        code = code < 0 ? -code : code;
			        String codeStr = code + "";
			        int len = codeStr.length();
			        return patch[len] + codeStr;
			    }
	
			    @Cacheable(value = "smsCode",key="#tele")
			    public String get(String tele){
			        return null;
			    }
			}
		注意：为什么要把获取验证码的方法放在这里，因为他在这里可以被加载成一个bean，这样注解才能生效才能够获取到值
		⑥定义验证码功能的web层接口，一个方法用于提供手机号获取验证码，一个方法用于提供手机号和验证码进行校验
			@RestController
			@RequestMapping("/sms")
			public class SMSCodeController {
			    @Autowired
			    private SMSCodeService smsCodeService;
			    
			    @GetMapping
			    public String getCode(String tele){
			        String code = smsCodeService.sendCodeToSMS(tele);
			        return code;
			    }
			    
			    @PostMapping
			    public boolean checkCode(SMSCode smsCode){
			        return smsCodeService.checkCode(smsCode);
			    }
			}



	boot整合Ehcache
		①导入坐标
			<dependency>
			    <groupId>net.sf.ehcache</groupId>
			    <artifactId>ehcache</artifactId>
			</dependency>
		②在yml进行配置
			spring:
			  cache:
			    type: ehcache
			    ehcache:
			      config: ehcache.xml
		注意：因为他是外部文件，他有着自己的配置文件，如果不给出就会报找不到ehcache的错
		③创建ehcache.xml配置文件
			<?xml version="1.0" encoding="UTF-8"?>
			<ehcache xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
			         xsi:noNamespaceSchemaLocation="http://ehcache.org/ehcache.xsd"
			         updateCheck="false">
			    <diskStore path="D:\ehcache" />
	
			    <!--默认缓存策略 -->
			    <!-- external：是否永久存在，设置为true则不会被清除，此时与timeout冲突，通常设置为false-->
			    <!-- diskPersistent：是否启用磁盘持久化-->
			    <!-- maxElementsInMemory：最大缓存数量-->
			    <!-- overflowToDisk：超过最大缓存数量是否持久化到磁盘-->
			    <!-- timeToIdleSeconds：最大不活动间隔，设置过长缓存容易溢出，设置过短无效果，可用于记录时效性数据，例如验证码-->
			    <!-- timeToLiveSeconds：最大存活时间-->
			    <!-- memoryStoreEvictionPolicy：缓存清除策略-->
			    <defaultCache
			        eternal="false"
			        diskPersistent="false"
			        maxElementsInMemory="1000"
			        overflowToDisk="false"
			        timeToIdleSeconds="60"
			        timeToLiveSeconds="60"
			        memoryStoreEvictionPolicy="LRU" />
	
			    <cache
			        name="smsCode"
			        eternal="false"
			        diskPersistent="false"
			        maxElementsInMemory="1000"
			        overflowToDisk="false"
			        timeToIdleSeconds="10"
			        timeToLiveSeconds="10"
			        memoryStoreEvictionPolicy="LRU" />
			</ehcache>
		注意：前面案例缓存空间的名字叫做smsCode，你要在xml里面配置名字，在这里淘汰策略LRU其实就算淘汰掉长期不活跃的，也就是上次用的时间离现在的时间最长，而LFU其实就算淘汰掉使用次数最小的那一个
		代码都没有改变和之前一样


	boot整合Redis缓存
		①导入坐标
			<dependency>
			    <groupId>org.springframework.boot</groupId>
			    <artifactId>spring-boot-starter-data-redis</artifactId>
			</dependency>
		②配置缓存技术实现使用redis
			spring:
			  redis:
			    host: localhost
			    port: 6379
			  cache:
			    type: redis
		注意：如果需要对redis作为缓存进行配置，注意不是对原始的redis进行配置，而是配置redis作为缓存使用相关的配置，隶属于spring.cache.redis节点下，注意不要写错位置了。
			spring:
			  redis:
			    host: localhost
			    port: 6379
			  cache:
			    type: redis
			    redis:
			      //是否使用前缀，也就是查key的时候存储空间名字不显示，默认为true
			      use-key-prefix: false
			      //设置存储空间名字前缀
			      key-prefix: sms_
			      //是否缓存空值
			      cache-null-values: false
			      //最大活动时间
			      time-to-live: 10s
		总结：代码并没有改变，只是改变了缓存使用商


	boot整合Memcached(国内流行)
	安装：https://www.runoob.com/memcached/window-install-memcached.htm
	启用管理员命令窗口进入到安装目录然后执行memcached.exe -d install
	然后使用memcached.exe -d start		# 启动服务
			memcached.exe -d stop		# 停止服务
	boot并没有整合他，所以不能像以前一样整合了
	注意：memcached有三种客户端技术，分别是Memcached Client for Java、SpyMemcached和Xmemcached，其中性能指标各方面最好的客户端是Xmemcached，本次整合就使用这个作为客户端实现技术了。下面开始使用Xmemcached
	①导入xmemcached的坐标
		<dependency>
		    <groupId>com.googlecode.xmemcached</groupId>
		    <artifactId>xmemcached</artifactId>
		    <version>2.4.7</version>
		</dependency>
	②配置memcached，制作memcached的配置类
		@Configuration
		public class XMemcachedConfig {
		    @Bean
		    public MemcachedClient getMemcachedClient() throws IOException {
		        MemcachedClientBuilder memcachedClientBuilder = new XMemcachedClientBuilder("localhost:11211");
		        MemcachedClient memcachedClient = memcachedClientBuilder.build();
		        return memcachedClient;
		    }
		}
		注意：memcached默认对外服务端口11211。
	③使用xmemcached客户端操作缓存，注入MemcachedClient对象
		@Service
		public class SMSCodeServiceImpl implements SMSCodeService {
		    @Autowired
		    private CodeUtils codeUtils;
		    @Autowired
		    private MemcachedClient memcachedClient;
	
		    public String sendCodeToSMS(String tele) {
		        String code = codeUtils.generator(tele);
		        try {
		            memcachedClient.set(tele,10,code);
		        } catch (Exception e) {
		            e.printStackTrace();
		        }
		        return code;
		    }
	
		    public boolean checkCode(SMSCode smsCode) {
		        String code = null;
		        try {
		            code = memcachedClient.get(smsCode.getTele()).toString();
		        } catch (Exception e) {
		            e.printStackTrace();
		        }
		        return smsCode.getCode().equals(code);
		    }
		}
		注意：直接把端口写到创建对象里面不合适，在这里进行属性配置
		定义配置属性
		①定义配置类，加载必要的配置属性，读取配置文件中memcached节点信息
			@Component
			@ConfigurationProperties(prefix = "memcached")
			@Data
			public class XMemcachedProperties {
			    private String servers;
			    private int poolSize;
			    private long opTimeout;
			}
		②定义memcached节点信息(写道yml里面)
			memcached:
			  servers: localhost:11211
			  poolSize: 10
			  opTimeout: 3000
		③在memcached配置类中加载信息
			@Configuration
			public class XMemcachedConfig {
			    @Autowired
			    private XMemcachedProperties props;
			    @Bean
			    public MemcachedClient getMemcachedClient() throws IOException {
			        MemcachedClientBuilder memcachedClientBuilder = new XMemcachedClientBuilder(props.getServers());
			        memcachedClientBuilder.setConnectionPoolSize(props.getPoolSize());
			        memcachedClientBuilder.setOpTimeout(props.getOpTimeout());
			        MemcachedClient memcachedClient = memcachedClientBuilder.build();
			        return memcachedClient;
			    }
			}
		总结：1. memcached安装后需要启动对应服务才可以对外提供缓存功能，安装memcached服务需要基于windows系统管理员权限
		2. 由于springboot没有提供对memcached的缓存整合方案，需要采用手工编码的形式创建xmemcached客户端操作缓存
		3. 导入xmemcached坐标后，创建memcached配置类，注册MemcachedClient对应的bean，用于操作缓存
		4. 初始化MemcachedClient对象所需要使用的属性可以通过自定义配置属性类的形式加载



	boot整合jetcache缓存(阿里)
	场景：上面的使用既有远程的也有本地的使用，并且有时候需要A的缓存和B的缓存，这个时候jetcache就可以实现两个一起使用
	概述：jetcache严格意义上来说，并不是一个缓存解决方案，只能说他算是一个缓存框架，然后把别的缓存放到jetcache中管理，这样就可以支持AB缓存一起用了。并且jetcache参考了springboot整合缓存的思想，整体技术使用方式和springboot的缓存解决方案思想非常类似。
	目前jetcache支持的缓存方案本地缓存支持两种，远程缓存支持两种
		- 本地缓存（Local）
		  - LinkedHashMap
		  - Caffeine
		- 远程缓存（Remote）
		  - Redis
		  - Tair
	纯远程方案步骤：
		①导入springboot整合jetcache对应的坐标starter，当前坐标默认使用的远程方案是redis
			<dependency>
			    <groupId>com.alicp.jetcache</groupId>
			    <artifactId>jetcache-starter-redis</artifactId>
			    <version>2.6.2</version>
			</dependency>
		②远程方案基本配置
			jetcache:
			  remote:
			    default:
			      type: redis
			      host: localhost
			      port: 6379
			      poolConfig:
			        maxTotal: 50
			    sms:
			      type: redis
			      host: localhost
			      port: 6379
			      poolConfig:
			        maxTotal: 50
		注意：其中poolConfig是必配项，否则会报错
		③启用缓存，在引导类上方标注注解@EnableCreateCacheAnnotation配置springboot程序中可以使用注解的形式创建缓存
			@SpringBootApplication
			//jetcache启用缓存的主开关
			@EnableCreateCacheAnnotation
			public class Springboot20JetCacheApplication {
			    public static void main(String[] args) {
			        SpringApplication.run(Springboot20JetCacheApplication.class, args);
			    }
			}
		④创建缓存对象Cache，并使用注解@CreateCache标记当前缓存的信息，然后使用Cache对象的API操作缓存，put写缓存，get读缓存。
			@Service
			public class SMSCodeServiceImpl implements SMSCodeService {
			    @Autowired
			    private CodeUtils codeUtils;
			    
			    @CreateCache(area="sms", name="jetCache_",expire = 10,timeUnit = TimeUnit.SECONDS)
			    private Cache<String ,String> jetCache;
	
			    public String sendCodeToSMS(String tele) {
			        String code = codeUtils.generator(tele);
			        jetCache.put(tele,code);
			        return code;
			    }
	
			    public boolean checkCode(SMSCode smsCode) {
			        String code = jetCache.get(smsCode.getTele());
			        return smsCode.getCode().equals(code);
			    }
			}
		总结：通过上述jetcache使用远程方案连接redis可以看出，jetcache操作缓存时的接口操作更符合开发者习惯，使用缓存就先获取缓存对象Cache，放数据进去就是put，取数据出来就是get，更加简单易懂。并且jetcache操作缓存时，可以为某个缓存对象设置过期时间，将同类型的数据放入缓存中，方便有效周期的管理。
	
	纯本地方案步骤：
	远程方案中，配置中使用remote表示远程，换成local就是本地，只不过类型不一样而已。
		①导入springboot整合jetcache对应的坐标starter
			<dependency>
			    <groupId>com.alicp.jetcache</groupId>
			    <artifactId>jetcache-starter-redis</artifactId>
			    <version>2.6.2</version>
			</dependency>
		②本地缓存基本配置
			jetcache:
			  local:
			    default:
			      type: linkedhashmap
			      keyConvertor: fastjson
		注意：为了加速数据获取时key的匹配速度，jetcache要求指定key的类型转换器。简单说就是，如果你给了一个Object作为key的话，我先用key的类型转换器给转换成字符串，然后再保存。等到获取数据时，仍然是先使用给定的Object转换成字符串，然后根据字符串匹配。由于jetcache是阿里的技术，这里推荐key的类型转换器使用阿里的fastjson。
		③启用缓存
			@SpringBootApplication
			//jetcache启用缓存的主开关
			@EnableCreateCacheAnnotation
			public class Springboot20JetCacheApplication {
			    public static void main(String[] args) {
			        SpringApplication.run(Springboot20JetCacheApplication.class, args);
			    }
			}
		④创建缓存对象Cache时，标注当前使用本地缓存
			@Service
			public class SMSCodeServiceImpl implements SMSCodeService {
			    @CreateCache(name="jetCache_",expire = 1000,timeUnit = TimeUnit.SECONDS,cacheType = CacheType.LOCAL)
			    private Cache<String ,String> jetCache;
	
			    public String sendCodeToSMS(String tele) {
			        String code = codeUtils.generator(tele);
			        jetCache.put(tele,code);
			        return code;
			    }
	
			    public boolean checkCode(SMSCode smsCode) {
			        String code = jetCache.get(smsCode.getTele());
			        return smsCode.getCode().equals(code);
			    }
			}
		注意：cacheType控制当前缓存使用本地缓存还是远程缓存，配置cacheType=CacheType.LOCAL即使用本地缓存。


	本地+远程方案
	其实就是将两种配置合并到一起就可以了。
		jetcache:
		  local:
		    default:
		      type: linkedhashmap
		      keyConvertor: fastjson
		  remote:
		    default:
		      type: redis
		      host: localhost
		      port: 6379
		      poolConfig:
		        maxTotal: 50
		    sms:
		      type: redis
		      host: localhost
		      port: 6379
		      poolConfig:
		        maxTotal: 50
	在创建缓存的时候，配置cacheType为BOTH即则本地缓存与远程缓存同时使用。cacheType = CacheType.BOTH
	cacheType如果不进行配置，默认值是REMOTE，即仅使用远程缓存方案。
	
	方法缓存
	jetcache提供了方法缓存方案，只不过名称变更了而已。在对应的操作接口上方使用注解@Cached即可
	步骤：①导入springboot整合jetcache对应的坐标starter
		<dependency>
		    <groupId>com.alicp.jetcache</groupId>
		    <artifactId>jetcache-starter-redis</artifactId>
		    <version>2.6.2</version>
		</dependency>
		②配置缓存
			jetcache:
			  local:
			    default:
			      type: linkedhashmap
			      keyConvertor: fastjson
			  remote:
			    default:
			      type: redis
			      host: localhost
			      port: 6379
			      keyConvertor: fastjson
			      valueEncode: java
			      valueDecode: java
			      poolConfig:
			        maxTotal: 50
			    sms:
			      type: redis
			      host: localhost
			      port: 6379
			      poolConfig:
			        maxTotal: 50
		注意：由于redis缓存中不支持保存对象，因此需要对redis设置当Object类型数据进入到redis中时如何进行类型转换。需要配置keyConvertor表示key的类型转换方式，同时标注value的转换类型方式，值进入redis时是java类型，标注valueEncode为java，值从redis中读取时转换成java，标注valueDecode为java。
	
		注意，为了实现Object类型的值进出redis，需要保障进出redis的Object类型的数据必须实现序列化接口。
			@Data
			public class Book implements Serializable {
			    private Integer id;
			    private String type;
			    private String name;
			    private String description;
			}
		③启用缓存时开启方法缓存功能，并配置basePackages，说明在哪些包中开启方法缓存
			@SpringBootApplication
			//jetcache启用缓存的主开关
			@EnableCreateCacheAnnotation
			//开启方法注解缓存
			@EnableMethodCache(basePackages = "com.itheima")
			public class Springboot20JetCacheApplication {
			    public static void main(String[] args) {
			        SpringApplication.run(Springboot20JetCacheApplication.class, args);
			    }
			}
		④使用注解@Cached标注当前方法使用缓存
			@Service
			public class BookServiceImpl implements BookService {
			    @Autowired
			    private BookDao bookDao;
			    
			    @Override
			    @Cached(name="book_",key="#id",expire = 3600,cacheType = CacheType.REMOTE)
			    public Book getById(Integer id) {
			        return bookDao.selectById(id);
			    }
			}
	远程方案的数据同步
	由于远程方案中redis保存的数据可以被多个客户端共享，这就存在了数据同步问题。jetcache提供了3个注解解决此问题，分别在更新、删除操作时同步缓存数据，和读取缓存时定时刷新数据
	更新缓存
		@CacheUpdate(name="book_",key="#book.id",value="#book")
		public boolean update(Book book) {
		    return bookDao.updateById(book) > 0;
		}
	删除缓存
		@CacheInvalidate(name="book_",key = "#id")
		public boolean delete(Integer id) {
	    return bookDao.deleteById(id) > 0;
	}
	定时刷新缓存
		@Cached(name="book_",key="#id",expire = 3600,cacheType = CacheType.REMOTE)
		@CacheRefresh(refresh = 5)
		public Book getById(Integer id) {
		    return bookDao.selectById(id);
		}
	数据报表
	jetcache还提供有简单的数据报表功能，帮助开发者快速查看缓存命中信息，只需要添加一个配置即可
		jetcache:
			statIntervalMinutes: 1
		设置后，每1分钟在控制台输出缓存数据命中信息
	[DefaultExecutor] c.alicp.jetcache.support.StatInfoLogger  : jetcache stat from 2022-02-28 09:32:15,892 to 2022-02-28 09:33:00,003
		cache    |    qps|   rate|   get|    hit|   fail|   expire|   avgLoadTime|   maxLoadTime
		---------+-------+-------+------+-------+-------+---------+--------------+--------------
		book_    |   0.66| 75.86%|    29|     22|      0|        0|          28.0|           188
		---------+-------+-------+------+-------+-------+---------+--------------+--------------
	hit表示通过缓存实现的次数，get是请求的次数，fail丢失的，maxLoadTime是最大响应时间
	总结
		1. jetcache是一个类似于springcache的缓存解决方案，自身不具有缓存功能，它提供有本地缓存与远程缓存多级共同使用的缓存解决方案
		2. jetcache提供的缓存解决方案受限于目前支持的方案，本地缓存支持两种，远程缓存支持两种
		3. 注意数据进入远程缓存时的类型转换问题
		4. jetcache提供方法缓存，并提供了对应的缓存更新与刷新功能
		5. jetcache提供有简单的缓存信息命中报表方便开发者即时监控缓存数据命中情况
	但是他不能进行自由搭配。


	boot整合j2cache缓存
	场景：jetcache可以在限定范围内构建多级缓存，但是灵活性不足，不能随意搭配缓存，j2cache就可以满足
	概述：他是一个缓存整合框架，可以提供缓存的整合方案，但是自身不提供缓存
	步骤：
		①导入j2cache、redis、ehcache坐标
			<dependency>
			    <groupId>net.oschina.j2cache</groupId>
			    <artifactId>j2cache-core</artifactId>
			    <version>2.8.4-release</version>
			</dependency>
			<dependency>
			    <groupId>net.oschina.j2cache</groupId>
			    <artifactId>j2cache-spring-boot2-starter</artifactId>
			    <version>2.8.0-release</version>
			</dependency>
			<dependency>
			    <groupId>net.sf.ehcache</groupId>
			    <artifactId>ehcache</artifactId>
			</dependency>
		注意：j2cache的starter中默认包含了redis坐标，官方推荐使用redis作为二级缓存，因此此处无需导入redis坐标
		②配置一级与二级缓存，并配置一二级缓存间数据传递方式，配置书写在名称为j2cache.properties的文件中。如果使用ehcache还需要单独添加ehcache的配置文件
			# 1级缓存
			j2cache.L1.provider_class = ehcache
			ehcache.configXml = ehcache.xml
	
			# 2级缓存
			j2cache.L2.provider_class = net.oschina.j2cache.cache.support.redis.SpringRedisProvider
			j2cache.L2.config_section = redis
			redis.hosts = localhost:6379
	
			# 1级缓存中的数据如何到达二级缓存
			j2cache.broadcast = net.oschina.j2cache.cache.support.redis.SpringRedisPubSubPolicy
		注意：此处配置不能乱配置，需要参照官方给出的配置说明进行。例如1级供应商选择ehcache，供应商名称仅仅是一个ehcache，但是2级供应商选择redis时要写专用的Spring整合Redis的供应商类名SpringRedisProvider，而且这个名称并不是所有的redis包中能提供的，也不是spring包中提供的。因此配置j2cache必须参照官方文档配置，而且还要去找专用的整合包，导入对应坐标才可以使用。
		一级与二级缓存最重要的一个配置就是两者之间的数据沟通方式，此类配置也不是随意配置的，并且不同的缓存解决方案提供的数据沟通方式差异化很大，需要查询官方文档进行设置。
		③使用缓存
			@Service
			public class SMSCodeServiceImpl implements SMSCodeService {
			    @Autowired
			    private CodeUtils codeUtils;
	
			    @Autowired
			    private CacheChannel cacheChannel;
	
			    public String sendCodeToSMS(String tele) {
			        String code = codeUtils.generator(tele);
			        cacheChannel.set("sms",tele,code);
			        return code;
			    }
	
			    public boolean checkCode(SMSCode smsCode) {
			        String code = cacheChannel.get("sms",smsCode.getTele()).asString();
			        return smsCode.getCode().equals(code);
			    }
			}
		注意：j2cache的使用和jetcache比较类似，但是无需开启使用的开关，直接定义缓存对象即可使用，缓存对象名CacheChannel。
		总结：
			1. j2cache是一个缓存框架，自身不具有缓存功能，它提供多种缓存整合在一起使用的方案
			2. j2cache需要通过复杂的配置设置各级缓存，以及缓存之间数据交换的方式
			3. j2cache操作接口通过CacheChannel实现



11.任务
	概述：定时任务是企业级应用中常见操作

	Quartz
	概述：Quartz技术是一个比较成熟的定时任务框架，springboot对其进行整合后，简化了一系列的配置，将很多配置采用默认设置，这样开发阶段就简化了很多。它里面的一些概念：
		- 工作（Job）：用于定义具体执行的工作
		- 工作明细（JobDetail）：用于描述定时工作相关的信息
		- 触发器（Trigger）：描述了工作明细与调度器的对应关系
		- 调度器（Scheduler）：用于描述触发工作的执行规则，通常使用cron表达式定义规则
	步骤：
		①导入springboot整合Quartz的starter
			<dependency>
			    <groupId>org.springframework.boot</groupId>
			    <artifactId>spring-boot-starter-quartz</artifactId>
			</dependency>
		②定义任务Bean，按照Quartz的开发规范制作，继承QuartzJobBean
		这个类卸载quartz包下
			public class MyQuartz extends QuartzJobBean {
			    @Override
			    protected void executeInternal(JobExecutionContext context) throws JobExecutionException {
			        System.out.println("quartz task run...");
			    }
			}
		③创建Quartz配置类(config包中)，定义工作明细（JobDetail）与触发器的（Trigger）bean
			@Configuration
			public class QuartzConfig {
			    @Bean
			    public JobDetail printJobDetail(){
			        //绑定具体的工作
			        return JobBuilder.newJob(MyQuartz.class).storeDurably().build();
			    }
			    @Bean
			    public Trigger printJobTrigger(){
			        ScheduleBuilder schedBuilder = CronScheduleBuilder.cronSchedule("0/5 * * * * ?");
			        //绑定对应的工作明细
			        return TriggerBuilder.newTrigger().forJob(printJobDetail()).withSchedule(schedBuilder).build();
			    }
			}
		注意：工作明细中要设置对应的具体工作，使用newJob()操作传入对应的工作任务类型即可。
		触发器需要绑定任务，使用forJob()操作传入绑定的工作明细对象。此处可以为工作明细设置名称然后使用名称绑定，也可以直接调用对应方法绑定。触发器中最核心的规则是执行时间，此处使用调度器定义执行时间，执行时间描述方式使用的是cron表达式。		
		总结：
			1. springboot整合Quartz就是将Quartz对应的核心对象交给spring容器管理，包含两个对象，JobDetail和Trigger对象
			2. JobDetail对象描述的是工作的执行信息，需要绑定一个QuartzJobBean类型的对象
			3. Trigger对象定义了一个触发器，需要为其指定绑定的JobDetail是哪个，同时要设置执行周期调度器


	Task
	场景：spring根据定时任务的特征，将定时任务的开发简化到了极致。怎么说呢？要做定时任务总要告诉容器有这功能吧，然后定时执行什么任务直接告诉对应的bean什么时间执行就行了。
	步骤：
		①开启定时任务功能，在引导类上开启定时任务功能的开关，使用注解@EnableScheduling
			@SpringBootApplication
			//开启定时任务功能
			@EnableScheduling
			public class Springboot22TaskApplication {
			    public static void main(String[] args) {
			        SpringApplication.run(Springboot22TaskApplication.class, args);
			    }
			}
		②定义Bean，在对应要定时执行的操作上方，使用注解@Scheduled定义执行的时间，执行时间的描述方式还是cron表达式
			@Component
			public class MyBean {
			    @Scheduled(cron = "0/1 * * * * ?")
			    public void print(){
			        System.out.println(Thread.currentThread().getName()+" :spring task run...");
			    }
			}
		注意：总体感觉其实什么东西都没少，只不过没有将所有的信息都抽取成bean，而是直接使用注解绑定定时执行任务的事情而已。
		如何想对定时任务进行相关配置，可以通过配置文件进行
			spring:
			  task:
			   	scheduling:
			      pool:
			       	size: 1							# 任务调度线程池大小 默认 1
			      thread-name-prefix: ssm_      	# 调度线程名称前缀 默认 scheduling-      
			      shutdown:
			        await-termination: false		# 线程池关闭时等待所有任务完成
			        await-termination-period: 10s	# 调度线程关闭前最大等待时间，确保最后一定关闭
		总结
		1. spring task需要使用注解@EnableScheduling开启定时任务功能
		2. 为定时执行的的任务设置执行周期，描述方式cron表达式


12.邮件
	学习邮件发送之前先了解3个概念，这些概念规范了邮件操作过程中的标准。
	- SMTP（Simple Mail Transfer Protocol）：简单邮件传输协议，用于发送电子邮件的传输协议
	- POP3（Post Office Protocol - Version 3）：用于接收电子邮件的标准协议
	- IMAP（Internet Mail Access Protocol）：互联网消息协议，是POP3的替代协议
	发送简单邮件步骤：
		①导入springboot整合javamail的starter
			<dependency>
			    <groupId>org.springframework.boot</groupId>
			    <artifactId>spring-boot-starter-mail</artifactId>
			</dependency>
		②配置邮箱的登录信息
			spring:
			  mail:
			    host: smtp.qq.com
			    username: 571247566@qq.com
			    password: 授权码
		③使用JavaMailSender接口发送邮件
			@Service
			public class SendMailServiceImpl implements SendMailService {
			    @Autowired
			    private JavaMailSender javaMailSender;

			    //发送人
			    private String from = "test@qq.com";
			    //接收人
			    private String to = "test@126.com";
			    //标题
			    private String subject = "测试邮件";
			    //正文
			    private String context = "测试邮件正文内容";
	
			    @Override
			    public void sendMail() {
			        SimpleMailMessage message = new SimpleMailMessage();
			        message.setFrom(from+"(小甜甜)");
			        //这里在后面加小甜甜意思是修改发件人的姓名
			        message.setTo(to);
			        message.setSubject(subject);
			        message.setText(context);
			        javaMailSender.send(message);
			    }
			}
		注意：将发送邮件的必要信息（发件人、收件人、标题、正文）封装到SimpleMailMessage对象中，可以根据规则设置发送人昵称等。
	
	多组件邮件(复杂的)
	发送简单邮件仅需要提供对应的4个基本信息就可以了，如果想发送复杂的邮件，需要更换发送邮件对象。使用MimeMessage可以发送特殊的邮件。
	发送网页正文邮件
		@Service
		public class SendMailServiceImpl2 implements SendMailService {
		    @Autowired
		    private JavaMailSender javaMailSender;
	
		    //发送人
		    private String from = "test@qq.com";
		    //接收人
		    private String to = "test@126.com";
		    //标题
		    private String subject = "测试邮件";
		    //正文
		    private String context = "<img src='ABC.JPG'/><a href='https://www.itcast.cn'>点开有惊喜</a>";
	
		    public void sendMail() {
		        try {
		            MimeMessage message = javaMailSender.createMimeMessage();
		            MimeMessageHelper helper = new MimeMessageHelper(message);
		            helper.setFrom(to+"(小甜甜)");
		            helper.setTo(from);
		            helper.setSubject(subject);
		            helper.setText(context,true);		//此处true设置正文支持html解析
	
		            javaMailSender.send(message);
		        } catch (Exception e) {
		            e.printStackTrace();
		        }
		    }
		}
	发送带有附件的邮件
		@Service
		public class SendMailServiceImpl2 implements SendMailService {
		    @Autowired
		    private JavaMailSender javaMailSender;
	
		    //发送人
		    private String from = "test@qq.com";
		    //接收人
		    private String to = "test@126.com";
		    //标题
		    private String subject = "测试邮件";
		    //正文
		    private String context = "测试邮件正文";
	
		    public void sendMail() {
		        try {
		            MimeMessage message = javaMailSender.createMimeMessage();
		            MimeMessageHelper helper = new MimeMessageHelper(message,true);		//此处设置支持附件
		            helper.setFrom(to+"(小甜甜)");
		            helper.setTo(from);
		            helper.setSubject(subject);
		            helper.setText(context);
	
		            //添加附件
		            File f1 = new File("springboot_23_mail-0.0.1-SNAPSHOT.jar");
		            File f2 = new File("resources\\logo.png");
	
		            helper.addAttachment(f1.getName(),f1);
		            helper.addAttachment("最靠谱的培训结构.png",f2);
	
		            javaMailSender.send(message);
		        } catch (Exception e) {
		            e.printStackTrace();
		        }
		    }
		}


13.消息
	目前企业级开发中广泛使用的消息处理技术共三大类，具体如下：
		- JMS
		- AMQP
		- MQTT
	解释：为什么是三大类，而不是三个技术呢？因为这些都是规范，就想JDBC技术，是个规范，开发针对规范开发，运行还要靠实现类，例如MySQL提供了JDBC的实现，最终运行靠的还是实现。并且这三类规范都是针对异步消息进行处理的，也符合消息的设计本质，处理异步的业务。

	JMS
	概述：
		JMS（Java Message Service）,这是一个规范，作用等同于JDBC规范，提供了与消息服务相关的API接口。
	JMS消息模型：
			①点对点模型：peer-2-peer，生产者会将消息发送到一个保存消息的容器中，通常使用队列模型，使用队列保存消息。一个队列的消息只能被一个消费者消费，或未被及时消费导致超时。这种模型下，生产者和消费者是一对一绑定的。
			②发布订阅模型：publish-subscribe，生产者将消息发送到一个保存消息的容器中，也是使用队列模型来保存。但是消息可以被多个消费者消费，生产者和消费者完全独立，相互不需要感知对方的存在。
			注意：以上这种分类是从消息的生产和消费过程来进行区分，针对消息所包含的信息不同，还可以进行不同类别的划分。
	JMS消息种类：
		根据消息中包含的数据种类划分，可以将消息划分成6种消息。
		- TextMessage
		- MapMessage
		- BytesMessage
		- StreamMessage
		- ObjectMessage
		- Message （只有消息头和属性）
	注意：JMS主张不同种类的消息，消费方式不同，可以根据使用需要选择不同种类的消息。但是这一点也成为其诟病之处，后面再说。整体上来说，JMS就是典型的保守派，什么都按照J2EE的规范来，做一套规范，定义若干个标准，每个标准下又提供一大批API。目前对JMS规范实现的消息中间件技术还是挺多的，毕竟是皇家御用，肯定有人舔，例如ActiveMQ、Redis、HornetMQ。但是也有一些不太规范的实现，参考JMS的标准设计，但是又不完全满足其规范，例如：RabbitMQ、RocketMQ。
	
	AMQP
	概述：
		一种协议（高级消息队列协议，也是消息代理规范），规范了网络交换的数据格式，兼容JMS操作。
	优点：
		具有跨平台性，服务器供应商，生产者，消费者可以使用不同的语言来实现
	消息种类：
		AMQP消息种类：byte[]
		AMQP在JMS的消息模型基础上又进行了进一步的扩展，除了点对点和发布订阅的模型，开发了几种全新的消息模型，适应各种各样的消息发送。
	消息模型：
		- direct exchange
		- fanout exchange
		- topic exchange
		- headers exchange
		- system exchange
	场景：目前实现了AMQP协议的消息中间件技术也很多，而且都是较为流行的技术，例如：RabbitMQ、StormMQ、RocketMQ
	
	MQTT(消息队列遥测传输，专为小设备设计)
	
	KafKa
	概述：一种高吞吐量的分布式发布订阅消息系统，提供实时消息功能。
	场景：Kafka技术并不是作为消息中间件为主要功能的产品，但是其拥有发布订阅的工作模式，也可以充当消息中间件来使用，而且目前企业级开发中其身影也不少见。


	购物订单发送手机短信案例
	需求：
		- 执行下单业务时（模拟此过程），调用消息服务，将要发送短信的订单id传递给消息中间件
		- 消息处理服务接收到要发送的订单id后输出订单id（模拟发短信）
	步骤：①创建订单业务(业务层接口)
			public interface OrderService {
			    void order(String id);
			}
		注意：模拟传入订单id，执行下订单业务，参数为虚拟设定，实际应为订单对应的实体类
		②业务层实现
			@Service
			public class OrderServiceImpl implements OrderService {
			    @Autowired
			    private MessageService messageService;
			    
			    @Override
			    public void order(String id) {
			        //一系列操作，包含各种服务调用，处理各种业务
			        System.out.println("订单处理开始");
			        //短信消息处理
			        messageService.sendMessage(id);
			        System.out.println("订单处理结束");
			        System.out.println();
			    }
			}
		注意：业务层转调短信处理的服务MessageService
		③表现层服务
			@RestController
			@RequestMapping("/orders")
			public class OrderController {
	
			    @Autowired
			    private OrderService orderService;
	
			    @PostMapping("{id}")
			    public void order(@PathVariable String id){
			        orderService.order(id);
			    }
			}
		注意：表现层对外开发接口，传入订单id即可（模拟）
		④短信处理业务(业务层接口)
			public interface MessageService {
			    void sendMessage(String id);
			    String doMessage();
			}
		注意：短信处理业务层接口提供两个操作，发送要处理的订单id到消息中间件，另一个操作目前暂且设计成处理消息，实际消息的处理过程不应该是手动执行，应该是自动执行，到具体实现时再进行设计
		⑤业务层实现
			@Service
			public class MessageServiceImpl implements MessageService {
			    private ArrayList<String> msgList = new ArrayList<String>();
	
			    @Override
			    public void sendMessage(String id) {
			        System.out.println("待发送短信的订单已纳入处理队列，id："+id);
			        msgList.add(id);
			    }
	
			    @Override
			    public String doMessage() {
			        String id = msgList.remove(0);
			        System.out.println("已完成短信发送业务，id："+id);
			        return id;
			    }
			}
		注意：短信处理业务层实现中使用集合先模拟消息队列，观察效果
		⑥表现层服务
			@RestController
			@RequestMapping("/msgs")
			public class MessageController {
	
			    @Autowired
			    private MessageService messageService;
	
			    @GetMapping
			    public String doMessage(){
			        String id = messageService.doMessage();
			        return id;
			    }
			}
		注意：短信处理表现层接口暂且开发出一个处理消息的入口，但是此业务是对应业务层中设计的模拟接口，实际业务不需要设计此接口。



	Boot整合ActiveMQ
	概述：ActiveMQ是MQ产品中的元老级产品，早期标准MQ产品之一，现在新产品用的很少了
	整合步骤：
		①导入springboot整合ActiveMQ的starter
			<dependency>
			    <groupId>org.springframework.boot</groupId>
			    <artifactId>spring-boot-starter-activemq</artifactId>
			</dependency>
		②配置ActiveMQ的服务器地址
			spring:
			  activemq:
			    broker-url: tcp://localhost:61616
		③使用JmsMessagingTemplate操作ActiveMQ
			@Service
			public class MessageServiceActivemqImpl implements MessageService {
			    @Autowired
			    private JmsMessagingTemplate messagingTemplate;
	
			    @Override
			    public void sendMessage(String id) {
			        System.out.println("待发送短信的订单已纳入处理队列，id："+id);
			        messagingTemplate.convertAndSend("order.queue.id",id);
			    }
	
			    @Override
			    public String doMessage() {
			        String id = messagingTemplate.receiveAndConvert("order.queue.id",String.class);
			        System.out.println("已完成短信发送业务，id："+id);
			        return id;
			    }
			}
		注意：发送消息需要先将消息的类型转换成字符串，然后再发送，所以是convertAndSend，定义消息发送的位置，和具体的消息内容，此处使用id作为消息内容。
		接收消息需要先将消息接收到，然后再转换成指定的数据类型，所以是receiveAndConvert，接收消息除了提供读取的位置，还要给出转换后的数据的具体类型。
		④使用消息监听器在服务器启动后，监听指定位置，当消息出现后，立即消费消息(在listener包)
			@Component
			public class MessageListener {
			    @JmsListener(destination = "order.queue.id")
			    @SendTo("order.other.queue.id")
			    public String receive(String id){
			        System.out.println("已完成短信发送业务，id："+id);
			        return "new:"+id;
			    }
			}
		注意：使用注解@JmsListener定义当前方法监听ActiveMQ中指定名称的消息队列。
		如果当前消息队列处理完还需要继续向下传递当前消息到另一个队列中使用注解@SendTo即可，这样即可构造连续执行的顺序消息队列。
		⑤切换消息模型由点对点模型到发布订阅模型，修改jms配置即可
			spring:
			  activemq:
			    broker-url: tcp://localhost:61616
			  jms:
			    pub-sub-domain: true
		注意：pub-sub-domain默认值为false，即点对点模型，修改为true后就是发布订阅模型。
	总结：
		1. springboot整合ActiveMQ提供了JmsMessagingTemplate对象作为客户端操作消息队列
		2. 操作ActiveMQ需要配置ActiveMQ服务器地址，默认端口61616
		3. 企业开发时通常使用监听器来处理消息队列中的消息，设置监听器使用注解@JmsListener
		4. 配置jms的pub-sub-domain属性可以在点对点模型和发布订阅模型间切换消息模型



	Boot整合RabbitMQ
	场景：RabbitMQ是MQ产品中的目前较为流行的产品之一，它遵从AMQP协议。RabbitMQ的底层实现语言使用的是Erlang，所以安装RabbitMQ需要先安装Erlang。
	Erlang安装：
		windows版安装包下载地址：https://www.erlang.org/downloads
	注意：下载完毕后得到exe安装文件，一键傻瓜式安装，安装完毕需要重启，需要重启，需要重启。
	Erlang安装后需要配置环境变量，否则RabbitMQ将无法找到安装的Erlang。需要配置项如下，作用等同JDK配置环境变量的作用。
	然后安装rabbitmq
	启动服务器(在sbin下)：
		rabbitmq-service.bat start		# 启动服务
		rabbitmq-service.bat stop		# 停止服务
		rabbitmqctl status				# 查看服务状态
	(建议在电脑服务进行开启)
	运行sbin目录下的rabbitmq-service.bat命令即可，start参数表示启动，stop参数表示退出，默认对外服务端口5672。
	注意：启动rabbitmq的过程实际上是开启rabbitmq对应的系统服务，需要管理员权限方可执行。
	注意：activemq与rabbitmq有一个端口冲突问题，学习阶段无论操作哪一个？请确保另一个处于关闭状态。
	
	访问web管理服务
	概述：RabbitMQ也提供有web控制台服务，但是此功能是一个插件，需要先启用才可以使用。
		rabbitmq-plugins.bat list							# 查看当前所有插件的运行状态
		rabbitmq-plugins.bat enable rabbitmq_management		# 启动rabbitmq_management插件
		关闭是disable
	启动插件后可以在插件运行状态中查看是否运行，运行后通过浏览器即可打开服务后台管理界面
		http://localhost:15672
	先输入访问用户名和密码，初始化用户名和密码相同，均为：guest


	boot整合(direct模型)
	这种属于直连交换机模式
	RabbitMQ满足AMQP协议，因此不同的消息模型对应的制作不同，先使用最简单的direct模型开发。
	步骤：
		①导入springboot整合amqp的starter，amqp协议默认实现为rabbitmq方案
			<dependency>
			    <groupId>org.springframework.boot</groupId>
			    <artifactId>spring-boot-starter-amqp</artifactId>
			</dependency>
		②配置RabbitMQ的服务器地址
			spring:
			  rabbitmq:
			    host: localhost
			    port: 5672
		③初始化直连模式系统设置
		由于RabbitMQ不同模型要使用不同的交换机，因此需要先初始化RabbitMQ相关的对象，例如队列，交换机等
		//他在RabbitMQ包下的direct包下的config中
			@Configuration
			public class RabbitConfigDirect {
			    @Bean
			    public Queue directQueue(){
			    //创建队列的参数
			    //durable：是否持久化，默认false
			    //exclusive：是否当前连接专用，默认false，连接关闭后队列即被删除
			    //autoDelete：是否自动删除，当生产者或消费者不再使用此队列，自动删除
			        return new Queue("direct_queue");
			    }
			    @Bean
			    public Queue directQueue2(){
			        return new Queue("direct_queue2");
			    }
			    @Bean
			    public DirectExchange directExchange(){
			        return new DirectExchange("directExchange");
			    }
			    @Bean
			    public Binding bindingDirect(){
			        return BindingBuilder.bind(directQueue()).to(directExchange()).with("direct");
			    }
			    @Bean
			    public Binding bindingDirect2(){
			        return BindingBuilder.bind(directQueue2()).to(directExchange()).with("direct2");
			    }
			}
		注意：队列Queue与直连交换机DirectExchange创建后，还需要绑定他们之间的关系Binding，这样就可以通过交换机操作对应队列。
		④使用AmqpTemplate操作RabbitMQ
		//他是在RabbitMq包下的direct包下
			@Service
			public class MessageServiceRabbitmqDirectImpl implements MessageService {
			    @Autowired
			    private AmqpTemplate amqpTemplate;
	
			    @Override
			    public void sendMessage(String id) {
			        System.out.println("待发送短信的订单已纳入处理队列（rabbitmq direct），id："+id);
			        amqpTemplate.convertAndSend("directExchange","direct",id);
			    }
			}
		注意：amqp协议中的操作API接口名称看上去和jms规范的操作API接口很相似，但是传递参数差异很大。
		⑤使用消息监听器在服务器启动后，监听指定位置，当消息出现后，立即消费消息
			@Component
			public class MessageListener {
			    @RabbitListener(queues = "direct_queue")
			    public void receive(String id){
			        System.out.println("已完成短信发送业务(rabbitmq direct)，id："+id);
			    }
			}
		注意：使用注解@RabbitListener定义当前方法监听RabbitMQ中指定名称的消息队列


	boot整合(topic模型)
	步骤：
		①导入springboot整合amqp的starter，amqp协议默认实现为rabbitmq方案
		②配置RabbitMQ的服务器地址
		③初始化主题模式系统设置
			@Configuration
			public class RabbitConfigTopic {
			    @Bean
			    public Queue topicQueue(){
			        return new Queue("topic_queue");
			    }
			    @Bean
			    public Queue topicQueue2(){
			        return new Queue("topic_queue2");
			    }
			    @Bean
			    public TopicExchange topicExchange(){
			        return new TopicExchange("topicExchange");
			    }
			    @Bean
			    public Binding bindingTopic(){
			        return BindingBuilder.bind(topicQueue()).to(topicExchange()).with("topic.*.id");
			    }
			    @Bean
			    public Binding bindingTopic2(){
			        return BindingBuilder.bind(topicQueue2()).to(topicExchange()).with("topic.orders.*");
			    }
			}
		注意：主题模式支持routingKey匹配模式，*表示匹配一个单词，#表示匹配任意内容，这样就可以通过主题交换机将消息分发到不同的队列中，详细内容请参看RabbitMQ系列课程。
		④使用AmqpTemplate操作RabbitMQ
			@Service
			public class MessageServiceRabbitmqTopicImpl implements MessageService {
			    @Autowired
			    private AmqpTemplate amqpTemplate;
	
			    @Override
			    public void sendMessage(String id) {
			        System.out.println("待发送短信的订单已纳入处理队列（rabbitmq topic），id："+id);
			        amqpTemplate.convertAndSend("topicExchange","topic.orders.id",id);
			    }
			}
		注意：发送消息后，根据当前提供的routingKey与绑定交换机时设定的routingKey进行匹配，规则匹配成功消息才会进入到对应的队列中。
		⑤使用消息监听器在服务器启动后，监听指定队列
			@Component
			public class MessageListener {
			    @RabbitListener(queues = "topic_queue")
			    public void receive(String id){
			        System.out.println("已完成短信发送业务(rabbitmq topic 1)，id："+id);
			    }
			    @RabbitListener(queues = "topic_queue2")
			    public void receive2(String id){
			        System.out.println("已完成短信发送业务(rabbitmq topic 22222222)，id："+id);
			    }
			}
		注意：使用注解@RabbitListener定义当前方法监听RabbitMQ中指定名称的消息队列。
		总结：
			1. springboot整合RabbitMQ提供了AmqpTemplate对象作为客户端操作消息队列
			2. 操作ActiveMQ需要配置ActiveMQ服务器地址，默认端口5672
			3. 企业开发时通常使用监听器来处理消息队列中的消息，设置监听器使用注解@RabbitListener
			4. RabbitMQ有5种消息模型，使用的队列相同，但是交换机不同。交换机不同，对应的消息进入的策略也不同



	Boot整合RocketMQ
	RocketMQ由阿里研发，后捐赠给apache基金会，目前是apache基金会顶级项目之一，也是目前市面上的MQ产品中较为流行的产品之一，它遵从AMQP协议。
	还是先安装，默认服务端口9876
	windows版安装包下载地址：https://rocketmq.apache.org
	然后配置环境变量
		- ROCKETMQ_HOME
		- PATH
		- NAMESRV_ADDR （建议）： 127.0.0.1:9876
	注意：第三条不配的话在bin目录启动mqbroker时不能直接双击，需要现在cmd先set NAMESRV_ADDR 127.0.0.1:9876才能够启动
	
	RocketMQ工作模式
	在RocketMQ中，处理业务的服务器称为broker，生产者与消费者不是直接与broker联系的，而是通过命名服务器进行通信。broker启动后会通知命名服务器自己已经上线，这样命名服务器中就保存有所有的broker信息。当生产者与消费者需要连接broker时，通过命名服务器找到对应的处理业务的broker，因此命名服务器在整套结构中起到一个信息中心的作用。并且broker启动前必须保障命名服务器先启动。
	启动服务器
		mqnamesrv		# 启动命名服务器
		mqbroker		# 启动broker
	测试服务器启动状态
	运行bin目录下的tools命令即可使用。
	tools org.apache.rocketmq.example.quickstart.Producer		# 生产消息
	tools org.apache.rocketmq.example.quickstart.Consumer		# 消费消息


	boot整合rocketMQ（异步消息）
	步骤：
		①导入springboot整合RocketMQ的starter，此坐标不由springboot维护版本
			<dependency>
			    <groupId>org.apache.rocketmq</groupId>
			    <artifactId>rocketmq-spring-boot-starter</artifactId>
			    <version>2.2.1</version>
			</dependency>
		②配置RocketMQ的服务器地址
			rocketmq:
			  name-server: localhost:9876
			  producer:
			    group: group_rocketmq
		注意：设置默认的生产者消费者所属组group。
		③使用RocketMQTemplate操作RocketMQ
			@Service
			public class MessageServiceRocketmqImpl implements MessageService {
			    @Autowired
			    private RocketMQTemplate rocketMQTemplate;
	
			    @Override
			    public void sendMessage(String id) {
			        System.out.println("待发送短信的订单已纳入处理队列（rocketmq），id："+id);
			        SendCallback callback = new SendCallback() {
			            @Override
			            public void onSuccess(SendResult sendResult) {
			                System.out.println("消息发送成功");
			            }
			            @Override
			            public void onException(Throwable e) {
			                System.out.println("消息发送失败！！！！！");
			            }
			        };
			        rocketMQTemplate.asyncSend("order_id",id,callback);
			    }
			}
		注意：使用asyncSend方法发送异步消息。
		④使用消息监听器在服务器启动后，监听指定位置，当消息出现后，立即消费消息
			@Component
			@RocketMQMessageListener(topic = "order_id",consumerGroup = "group_rocketmq")
			public class MessageListener implements RocketMQListener<String> {
			    @Override
			    public void onMessage(String id) {
			        System.out.println("已完成短信发送业务(rocketmq)，id："+id);
			    }
			}
		注意：RocketMQ的监听器必须按照标准格式开发，实现RocketMQListener接口，泛型为消息类型。
		使用注解@RocketMQMessageListener定义当前类监听RabbitMQ中指定组、指定名称的消息队列。
		总结
			1. springboot整合RocketMQ使用RocketMQTemplate对象作为客户端操作消息队列
			2. 操作RocketMQ需要配置RocketMQ服务器地址，默认端口9876
			3. 企业开发时通常使用监听器来处理消息队列中的消息，设置监听器使用注解@RocketMQMessageListener



	boot整合kafka
	启动服务器
		kafka服务器的功能相当于RocketMQ中的broker，kafka运行还需要一个类似于命名服务器的服务。在kafka安装目录中自带一个类似于命名服务器的工具，叫做zookeeper，它的作用是注册中心，相关知识请到对应课程中学习。
		在bin目录里的windows下进行运行
		zookeeper-server-start.bat ..\..\config\zookeeper.properties		# 启动zookeeper
		kafka-server-start.bat ..\..\config\server.properties				# 启动kafka
	运行bin目录下的windows目录下的zookeeper-server-start命令即可启动注册中心，默认对外服务端口2181。
	运行bin目录下的windows目录下的kafka-server-start命令即可启动kafka服务器，默认对外服务端口9092。
	
	创建主题
	和之前操作其他MQ产品相似，kakfa也是基于主题操作，操作之前需要先初始化topic。
		# 创建topic
		kafka-topics.bat --create --zookeeper localhost:2181 --replication-factor 1 --partitions 1 --topic itheima
		# 查询topic
		kafka-topics.bat --zookeeper 127.0.0.1:2181 --list					
		# 删除topic
		kafka-topics.bat --delete --zookeeper localhost:2181 --topic itheima
	测试服务器启动状态
	Kafka提供有一套测试服务器功能的测试程序，运行bin目录下的windows目录下的命令即可使用。
		kafka-console-producer.bat --broker-list localhost:9092 --topic itheima							# 测试生产消息
		kafka-console-consumer.bat --bootstrap-server localhost:9092 --topic itheima --from-beginning	# 测试消息消费
	
	整合步骤：
		①导入springboot整合Kafka的starter，此坐标由springboot维护版本
			<dependency>
			    <groupId>org.springframework.kafka</groupId>
			    <artifactId>spring-kafka</artifactId>
			</dependency>
		②配置Kafka的服务器地址
			spring:
			  kafka:
			    bootstrap-servers: localhost:9092
			    consumer:
			      group-id: order
		注意：设置默认的生产者消费者所属组id。
		③使用KafkaTemplate操作Kafka
			@Service
			public class MessageServiceKafkaImpl implements MessageService {
			    @Autowired
			    private KafkaTemplate<String,String> kafkaTemplate;
	
			    @Override
			    public void sendMessage(String id) {
			        System.out.println("待发送短信的订单已纳入处理队列（kafka），id："+id);
			        kafkaTemplate.send("itheima2022",id);
			    }
			}
		注意：使用send方法发送消息，需要传入topic名称。
		④使用消息监听器在服务器启动后，监听指定位置，当消息出现后，立即消费消息
			@Component
			public class MessageListener {
			    @KafkaListener(topics = "itheima2022")
			    public void onMessage(ConsumerRecord<String,String> record){
			        System.out.println("已完成短信发送业务(kafka)，id："+record.value());
			    }
			}
		注意：使用注解@KafkaListener定义当前方法监听Kafka中指定topic的消息，接收到的消息封装在对象ConsumerRecord中，获取数据从ConsumerRecord对象中获取即可。
		总结：
			1. springboot整合Kafka使用KafkaTemplate对象作为客户端操作消息队列
			2. 操作Kafka需要配置Kafka服务器地址，默认端口9092
			3. 企业开发时通常使用监听器来处理消息队列中的消息，设置监听器使用注解@KafkaListener。接收消息保存在形参ConsumerRecord对象中



14.监控
	概述：
		①监控是一个非常重要的工作，是保障程序正常运行的基础手段
		②监控的过程通过一个监控程序进行，它汇总所有被监控的程序的信息集中统一展示
		③被监控程序需要主动上报自己被监控，同时要设置哪些指标被监控
	意义：
		①监控服务状态是否处理宕机状态
		②监控服务运行指标(内存，虚拟机，线程，请求等)
		③监控程序运行日志
		④管理服务状态(服务下线)


15.可视化监控平台(SpringBootAdmin)
	概述：Spring Boot Admin，这是一个开源社区项目，用于管理和监控SpringBoot应用程序。这个项目中包含有客户端和服务端两部分，而监控平台指的就是服务端。我们做的程序如果需要被监控，将我们做的程序制作成客户端，然后配置服务端地址后，服务端就可以通过HTTP请求的方式从客户端获取对应的信息，并通过UI界面展示对应信息。
	下面就来开发这套监控程序，先制作服务端，其实服务端可以理解为是一个web程序，收到一些信息后展示这些信息。
	服务端开发
	步骤：
		①导入springboot admin对应的starter，版本与当前使用的springboot版本保持一致，并将其配置成web工程(创建时可以在ops里面勾选)
			<dependency>
			    <groupId>de.codecentric</groupId>
			    <artifactId>spring-boot-admin-starter-server</artifactId>
			    <version>2.5.4</version>
			</dependency>

			<dependency>
			    <groupId>org.springframework.boot</groupId>
			    <artifactId>spring-boot-starter-web</artifactId>
			</dependency>
		②在引导类上添加注解@EnableAdminServer，声明当前应用启动后作为SpringBootAdmin的服务器使用
			@SpringBootApplication
			@EnableAdminServer
			public class Springboot25AdminServerApplication {
			    public static void main(String[] args) {
			        SpringApplication.run(Springboot25AdminServerApplication.class, args);
			    }
			}
		注意：在这里就好了，使用http://localhost:8080/applications访问
	
	客户端开发
	客户端程序开发其实和服务端开发思路基本相似，多了一些配置而已。
	步骤：
		①导入springboot admin对应的starter，版本与当前使用的springboot版本保持一致，并将其配置成web工程
			<dependency>
			    <groupId>de.codecentric</groupId>
			    <artifactId>spring-boot-admin-starter-client</artifactId>
			    <version>2.5.4</version>
			</dependency>
	
			<dependency>
			    <groupId>org.springframework.boot</groupId>
			    <artifactId>spring-boot-starter-web</artifactId>
			</dependency>
		注意：上述过程也可以通过创建项目时使用勾选的形式完成，不过一定要小心，端口配置成不一样的，否则会冲突。
		②置当前客户端将信息上传到哪个服务器上，通过yml文件配置
			server:
			  port: 80
			spring:
			  boot:
			    admin:
			      client:
			        url: http://localhost:8080
			management:
			  endpoint:
			    health:
			      show-details: always
			  endpoints:
			    web:
			      exposure:
			        include: "*"
	配置多个客户端
	可以通过配置客户端的方式在其他的springboot程序中添加客户端坐标，这样当前服务器就可以监控多个客户端程序了。每个客户端展示不同的监控信息。
	总结：
		1. 开发监控服务端需要导入坐标，然后在引导类上添加注解@EnableAdminServer，并将其配置成web程序即可
		2. 开发被监控的客户端需要导入坐标，然后配置服务端服务器地址，并做开放指标的设定即可
		3. 在监控平台中可以查阅到各种各样被监控的指标，前提是客户端开放了被监控的指标

16.监控原理
	概述：通过查阅监控中的映射指标，可以看到当前系统中可以运行的所有请求路径，其中大部分路径以/actuator开头
	简单来说，分四点：
		①Actuato提供了SpringBoot生产就绪功能，通过端点的配置与访问，获取端点信息
		②端点描述了一组监控信息，SpringBoot提供了多个内置端点，也可以根据需要自定义端点信息
		③访问当前应用所有端点信息：/actuator
		④访问端点详细信息：/actuator/端点名称

	原来监控中显示的信息实际上是通过发送请求后得到json数据，然后展示出来。按照上述操作，可以发送更多的以/actuator开头的链接地址，获取更多的数据，这些数据汇总到一起组成了监控平台显示的所有数据。
	到这里我们得到了一个核心信息，监控平台中显示的信息实际上是通过对被监控的应用发送请求得到的。那这些请求谁开发的呢？打开被监控应用的pom文件，其中导入了springboot admin的对应的client，在这个资源中导入了一个名称叫做actuator的包。被监控的应用之所以可以对外提供上述请求路径，就是因为添加了这个包。
	
	//health的端点是不能关闭的
	management:
	  endpoint:
	    health:						# 端点名称
	      show-details: always
	    info:						# 端点名称
	      enabled: true				# 是否开放
	为了方便开发者快速配置端点，springboot admin设置了13个较为常用的端点作为默认开放的端点，如果需要控制默认开放的端点的开放状态，可以通过配置设置，如下：
		management:
		  endpoints:
		    enabled-by-default: true	# 是否开启默认端点，默认值true
	上述端点开启后，就可以通过端点对应的路径查看对应的信息了。但是此时还不能通过HTTP请求查询此信息，还需要开启通过HTTP请求查询的端点名称，使用“*”可以简化配置成开放所有端点的WEB端HTTP请求权限。
		management:
		  endpoints:
		    web:
		      exposure:
		        include: "*"
	整体上来说，对于端点的配置有两组信息，一组是endpoints开头的，对所有端点进行配置，一组是endpoint开头的，对具体端点进行配置。
		management:
		  endpoint:		# 具体端点的配置
		    health:
		      show-details: always
		    info:
		      enabled: true
		  endpoints:	# 全部端点的配置
		    web:
		      exposure:
		        include: "*"
		    enabled-by-default: true
	总结
		1. 被监控客户端通过添加actuator的坐标可以对外提供被访问的端点功能
		2. 端点功能的开放与关闭可以通过配置进行控制
		3. web端默认无法获取所有端点信息，通过配置开放端点功能


17.自定义监控指标
	info端点指标控制(在actuator层下创建对应的类)
	概述：info端点描述了当前应用的基本信息，可以通过两种形式快速配置info端点的信息
	配置形式：
	①在yml文件中通过设置info节点的信息就可以快速配置端点信息
		info:
		  appName: @project.artifactId@
		  version: @project.version@
		  company: B站大学
		  author: yixin
	也可以通过请求端点信息路径获取对应json信息：http://localhost:81/actuator/info
	②编程形式
	通过配置的形式只能添加固定的数据，如果需要动态数据还可以通过配置bean的方式为info端点添加信息，此信息与配置信息共存
		@Component
		public class InfoConfig implements InfoContributor {
		    @Override
		    public void contribute(Info.Builder builder) {
		        builder.withDetail("runTime",System.currentTimeMillis());		//添加单个信息
		        Map infoMap = new HashMap();		
		        infoMap.put("buildTime","2006");
		        builder.withDetail("runTime",System.currentTimeMillis()).withiDetail("company","大学")
		        builder.withDetails(infoMap);									//添加一组信息
		    }
		}


	控制Health端点
	概述：health端点描述当前应用的运行健康指标，即应用的运行是否成功。通过编程的形式可以扩展指标信息。
		@Component
		public class HealthConfig extends AbstractHealthIndicator {
		    @Override
		    protected void doHealthCheck(Health.Builder builder) throws Exception {
		        boolean condition = true;
		        if(condition) {
		            builder.status(Status.UP);					//设置运行状态为启动状态
		            builder.withDetail("runTime", System.currentTimeMillis());
		            Map infoMap = new HashMap();
		            infoMap.put("buildTime", "2006");
		            builder.withDetails(infoMap);
		        }else{
		            builder.status(Status.OUT_OF_SERVICE);		//设置运行状态为不在服务状态
		            builder.withDetail("上线了吗？","你做梦");
		        }
		    }
		}
	注意：当任意一个组件状态不为UP时，整体应用对外服务状态为非UP状态。


	Metrics端点
	概述：metrics端点描述了性能指标，除了系统自带的监控性能指标，还可以自定义性能指标。
		@Service
		public class BookServiceImpl extends ServiceImpl<BookDao, Book> implements IBookService {
		    @Autowired
		    private BookDao bookDao;
	
		    private Counter counter;
	
		    //这个MeterRegistry是自动注入的
		    public BookServiceImpl(MeterRegistry meterRegistry){
		        counter = meterRegistry.counter("用户付费操作次数：");
		    }
	
		    @Override
		    public boolean delete(Integer id) {
		        //每次执行删除业务等同于执行了付费业务
		        counter.increment();
		        return bookDao.deleteById(id) > 0;
		    }
		}
	注意：这样在性能指标中就出现了自定义的性能指标监控项


	自定义端点
	可以根据业务需要自定义端点，方便业务监控
		@Component
		@Endpoint(id="pay",enableByDefault = true)
		public class PayEndpoint {
		    @ReadOperation
		    public Object getPay(){
		        Map payMap = new HashMap();
		        payMap.put("level 1","300");
		        payMap.put("level 2","291");
		        payMap.put("level 3","666");
		        return payMap;
		    }
		}
	注意：由于此端点数据spirng boot admin无法预知该如何展示，所以通过界面无法看到此数据，通过HTTP请求路径可以获取到当前端点的信息，但是需要先开启当前端点对外功能，或者设置当前端点为默认开发的端点。
	总结：	
		1. 端点的指标可以自定义，但是每种不同的指标根据其功能不同，自定义方式不同
		2. info端点通过配置和编程的方式都可以添加端点指标
		3. health端点通过编程的方式添加端点指标，需要注意要为对应指标添加启动状态的逻辑设定
		4. metrics指标通过在业务中添加监控操作设置指标
		5. 可以自定义端点添加更多的指标
