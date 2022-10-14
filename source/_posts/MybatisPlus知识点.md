---
title: MybatisPlus知识点
date: 2022-10-12 21:35:56
cover: ./img/mybaitsplus.png
tags: Java
categories: java
description: MP真是好用呀，不过或许进了公司还是得回到Mybatis呀
---



MyBatisPlus
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
			//添加注解表明为配置类
			@Configuration
			public class MybatisPlusConfig {
				//添加注解注册为bean让spring去管理
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
	注意：lt: 小于(<)，gt：大于(>)，多个条件，可以依次写即可，也可也通过.依次连接，这些都是并关系，想写或者在第一个条件写完加上.or()后面再继续链式连接，注意要先判断条件是否为null
	
	条件查询null时判断
	如lqw.lt(null!=uq.getAge2(),User::getAge, uq.getAge2());他表示如果前面哪个值不为空，连接两个条件即并
		#在这里like里面还有一个属性，当为true的时候sql语句连接条件，如lqw.like(Strings.isNotEmpty(name),Book::getName,"Spring");
	
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
	#在这里like里面还有一个属性，当为true的时候sql语句连接条件
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
