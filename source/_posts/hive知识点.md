---
title: hive知识点
date: 2022-11-15 16:52:46
cover: https://wltc2-1258834326.cos.ap-guangzhou.myqcloud.com/2023/01/09/63bbbaffd6ba1.png
tags: 
  - BigData
  - Hive
categories: 技术记录
description: 在大三学习hive的时候，听课顺带做了做笔记，毕竟笔记还是很重要的。
---





# 启动hive流程命令：

①hive --service metastore
②hive --service hiveserver2
③beeline
④在beeline输入!connect jdbc:hive2://localhost:10000
⑤输入账户密码即可 huser  1234
where条件中不能使用聚合函数

# Hive

### 1.概念：

​	Hive是基于Hadoop的一个数据仓库工具，可以将结构化的数据文件映射为一张表，并提供类SQL查询功能。
​	本质是：将HQL转化成MapReduce程序
​	要点：
​	（1）Hive处理的数据存储在HDFS
​	（2）Hive分析数据底层的实现是MapReduce
​	（3）执行程序运行在Yarn上
​	（4）Hive不是数据库

### 2.hive架构



		元数据：Metastore
		元数据包括：表名、表所属的数据库（默认是default）、表的拥有者、列/分区字段、表的类型（是否是外部表）、表的数据所在目录等；
		默认存储在自带的derby数据库中，推荐使用MySQL存储Metastore
	
	Hadoop
	使用HDFS进行存储，使用MapReduce进行计算。
	
	驱动器：Driver
	（1）解析器（SQL Parser）：将SQL字符串转换成抽象语法树AST，这一步一般都用第三方工具库完成，比如antlr；对AST进行语法分析，比如表是否存在、字段是否存在、SQL语义是否有误。
	（2）编译器（Physical Plan）：将AST编译生成逻辑执行计划。
	（3）优化器（Query Optimizer）：对逻辑执行计划进行优化。
	（4）执行器（Execution）：把逻辑执行计划转换成可以运行的物理计划。对于Hive来说，就是MR/Spark。
	Hive通过给用户提供的一系列交互接口，接收到用户的指令(SQL)，使用自己的Driver，结合元数据(MetaStore)，将这些指令翻译成MapReduce，提交到Hadoop中执行，最后，将执行返回的结果输出到用户交互接口。
	Hive中不建议对数据的改写，所有的数据都是在加载的时候确定好的。

### 3.Hive和数据库比较

​	①写时模式和读时模式
​	数据库是写时模式，可以对列建立索引，查找数据快；
​	Hive是读时模式，loaddata快，不需要读取数据进行解析，仅用于文件的复制和移动
​	②数据更新
​	 由于Hive是针对数据仓库应用设计的，而数据仓库的内容是读多写少的。因此，Hive中不建议对数据的改写，所有的数据都是在加载的时候确定好的。而数据库中的数据通常是需要经常进行修改的，
​	③执行延迟
​	 Hive 在查询数据的时候，由于没有索引，需要扫描整个表，因此延迟较高。另外一个导致 Hive 执行延迟高的因素是 MapReduce框架。由于MapReduce本身具有较高的延迟，因此在利用MapReduce执行Hive查询时，也会有较高的延迟。
​	④数据规模
​	 由于Hive建立在集群上并可以利用MapReduce进行并行计算，因此可以支持很大规模的数据；对应的，数据库可以支持的数据规模较小。
​	⑤数据存储位置不同
​	hive是将数据存储在hdfs上，而数据库则是将数据存储在本地或者块设 备上

### 4.基本数据类型

​	数据类型	Java数据类型	长度
​	TINYINT	byte	1byte有符号整数
​	SMALINT	short	2byte有符号整数
​	INT	int	4byte有符号整数
​	BIGINT	long	8byte有符号整数
​	BOOLEAN	boolean	布尔类型，true或者false
​	FLOAT	float	单精度浮点数
​	DOUBLE	double	双精度浮点数
​	STRING	string 字符系列。可以指定字符集。可以使用单引号或者双引号。
​	TIMESTAMP		时间类型	
​	DATA            日期对应年月日
​	BINARY		字节数组

### 5.集合数据类型

​	Array    一组具有相同数据类型的数据的集合
​	Map      一组键值对元组的集合
​	Struct   封装一组有名字的字段，其类型可以是任意的基本数据类型

### 6.类型转换

​	Hive的原子数据类型是可以进行隐式转换的，类似于Java的类型转换，例如某表达式使用INT类型，TINYINT会自动转换为INT类型，但是Hive不会进行反向转化，除非使用CAST操作。如cast(string as data)

### 7.运算符

### 8.存储格式

​	hive创建表时需要指明该表的存储格式，并且分为行存储和列存储
​	①TextFile(默认，行存储)
​	②SequenceFile(行存储)
​		他是hadoop用来存储二进制数形式的键值对而设计的文件格式
​	③ORC(列存储)
​	④Parquet(列存储)
​	压缩类型分为：
​	①无压缩类型(None)
​	②记录压缩类型(Recore)
​	③块压缩类型(Block)优先选择他

# DDL数据定义

### 9.创建数据库

​	CREATE DATABASE [IF NOT EXISTS] database_name
​	[COMMENT database_comment]
​	[LOCATION hdfs_path]  -- 数据库在HDFS上的默认存储路径是/user/hive/warehouse/*.db
​	[WITH DBPROPERTIES (property_name=property_value, ...)];

### 10.查询数据库

​	显示数据库：show databases;
​	过滤显示查询的数据库：show databases like ‘db_hive*’;
​	查看数据库详情：desc database db_hive；或 desc database extended db_hive;

### 11.删除数据库

​	drop database db_hive2;
​	drop database if exists db_hive2;
​	如果数据库不为空，可以采用cascade命令，强制删除：drop database db_hive cascade;

### 12.修改数据库

​	alter database hivedwh set dbproperties('createtime'='20221016')：设置数据库属性

### 13.创建表

​	CREATE [EXTERNAL] TABLE [IF NOT EXISTS] table_name 
​	[(col_name data_type [COMMENT col_comment], ...)] 
​	[COMMENT table_comment] -- 为表和列添加注释
​	[PARTITIONED BY (col_name data_type [COMMENT col_comment], ...)]  -- 创建分区表 
​	[CLUSTERED BY (col_name, col_name, ...)   -- 创建分桶表
​	[SORTED BY (col_name [ASC|DESC], ...)] INTO num_buckets BUCKETS]   -- 对桶中的一个或多个列另外排序
​	[ROW FORMAT row_format] -- eg：row format delimited fields terminated by '\t'。按照‘\t’分隔
​	[STORED AS file_format] -- 指定存储文件类型 SEQUENCEFILE（二进制序列文件）、TEXTFILE（文本）、RCFILE（列式存储格式文件）
​	[LOCATION hdfs_path] -- 指定表在HDFS上的存储位置。
​	[TBLPROPERTIES (property_name=property_value, ...)]
​	[AS select_statement] -- 后跟查询语句，根据查询结果创建表。
​	关键字解释说明：
​	①CREATE TABLE：创建一个名字为table_name的表，如果表已经存在，则抛出异常，可以使用 IF NOT EXISTS关键字选型忽略异常
​	②EXTERNAL：可以使用这个关键字创建一个外部表，在建表的同时指定实际表数据的存储路径(LOCATION)
​	③COMMENT：为表和字段添加注释信息
​	④PARTITONED BY：创建分区表(要创建一个新的字段作为表中字段)
​	⑤CLUSTERED BY：创建桶表(选择已经有的字段)
​	⑥SORTED BY：排序
​	⑦ROW FORMAT DELIMITERD：用于指定表中数据行和列的分隔符以及复杂数据类型数据的分隔符，这些分隔符必须与表数据中的分隔符完全一致
​		Fields Terminated By Char：用于指定字段分隔符
​		Collection Items Terminater By Char：用于指定复杂数据类型的分隔符
​		Map Keys Terminated By Char：用于指定Map中的key与value的分隔符
​		Lines Terminated By Char：用于指定行分隔符
​	⑧STORED AS：指定文件的存储格式
​	⑨LOCATION：指定所创建表的数据在HDFS中的存储位置

### 14.内外部表

​	内部表
​	默认创建的表都是所谓的管理表，有时也被称为内部表。因为这种表，Hive会（或多或少地）控制着数据的生命周期。Hive默认情况下会将这些表的数据存储在由配置项hive.metastore.warehouse.dir(例如，/user/hive/warehouse)所定义的目录的子目录下。 **当我们删除一个管理表时，Hive也会删除这个表中数据。**管理表不适合和其他工具共享数据。

	外部表（external ）
	因为表是外部表，所以Hive并非认为其完全拥有这份数据。删除该表并不会删除掉这份数据，不过描述表的元数据信息会被删除掉。
	查看表格式化数据：desc formatted 表名
	
	管理表与外部表的互相转换
	alter table 表名 set tblproperties('EXTERNAL'='TRUE');
	alter table 表名 set tblproperties('EXTERNAL'='FALSE');

### 15.分区表

​	概述： 分区表实际上就是对应一个HDFS文件系统上的独立的文件夹，该文件夹下是该分区所有的数据文件。**Hive中的分区就是分目录，**把一个大的数据集根据业务需要分割成小的数据集。在查询时通过WHERE子句中的表达式选择查询所需要的指定的分区，这样的查询效率会提高很多。
​	创建分区表dept_partition如：
​	create table dept_partition(
​	deptno int, dname string, loc string
​	)
​	partitioned by (day string)  //按天分区
​	row format delimited fields terminated by '\t';
​	注意：分区字段不能是表中已经存在的数据，可以将分区字段看作表的伪列。分区表加载数据时，必须指定分区
​	加载分区：
​	load data local inpath '/opt/module/hive/datas/dept_20200401.log' into table dept_partition partition(day='20200401');

	增加分区：
	alter table dept_partition add partition(day='20200405') partition(day='20200406');
	
	删除分区：
	alter table dept_partition drop partition (day='20200404'), partition(day='20200405');
	
	查看分区表结构：
	desc formatted dept_partition;
	
	创建二级分区(有两个分区字段)：
	create table dept_partition2(
	           deptno int, dname string, loc string
	           )
	           partitioned by (day string, hour string)
	           row format delimited fields terminated by '\t';
	
	把数据直接上传到分区目录上，让分区表和数据产生关联的三种方式
	方式一：上传数据后修复：
		msck repair table dept_partition2; //修复命令
	方式二：上传数据后添加分区
		dfs -mkdir -p /user/hive/warehouse/demo.db/dept_p2/month=202109/day=11;  //创建目录
		dfs -put /opt/datas/dept.txt /user/hive/warehouse/demo.db/dept_p2/month=202109/day=11; //上传数据
		alter table dept_p2 add partition(month='202109',day='11')  //添加分区字段
	方式三：创建文件夹后load数据到分区
		创建目录
		使用load data local inpath location into table上传数据
	也可以创建动态分区，根据分区字段，自动将数据添加至相应的分区。
	
	创建分区表例子：
	①创建分区表
CREATE TABLE if not exists stus_par(
   id int,
   name string,
   age int,
   major string,
   term int,
   score int,
   scholarship int
)partitioned by(major_p string)
row format delimited fields terminated by ",";
	②添加分区(可省略)
	alter table stus_par add if not exists partition(major_p='shuju') location 'shuju';
	③加载数据
	load data local INPATH '/home/huser/studets_shuju.txt' overwrite into table stus_par PARTITION (major_p='shuju');

	③加载数据方式二
	insert into table stus_par partition (major_p='shuju') select * from stus where major='shuju';
	
	④查询分区
	select * from stus_par where major_p='shuju';

### 16.分桶表

​	分区提供一个隔离数据和优化查询的便利方式。不过，并非所有的数据集都可形成合理的分区。对于一张表或者分区，Hive 可以进一步组织成桶，也就是更为细粒度的数据范围划分。

	分桶是将数据集分解成更容易管理的若干部分的另一个技术。
	
	分区针对的是数据的存储路径；分桶针对的是数据文件。
	创建分桶表：
	create table stu_buck(id int, name string)
	clustered by(id) 
	into 4 buckets
	row format delimited fields terminated by '\t';
	
	分桶规则：
		Hive的分桶采用对分桶字段的值进行哈希，然后除以桶的个数求余的方式决定该条记录存放在哪个桶当中
	
	例子：
	①创建源数据表
drop table if exists users_source;
CREATE TABLE users_source(id int,name string,age int,city string) row format delimited fields terminated by ',';
	②把数据加载到源数据
	load data local inpath ‘/home/huser/users.txt’ into table users_source;
	或者hadoop fs -put users.txt /user/hive/warehouse/demo.db/users_clu
	③创建分桶表
CREATE TABLE users_clu(id int,name string,age int,city string)CLUSTERED BY(city) 
Sorted by (id desc)  INTO 3 BUCKETS
row format delimited fields terminated by ',';
	④加载数据
	insert into users_clu select * from users_source;



# DML数据操作

### 17.数据导入

​	①向表中装载数据（Load）
​	load data [local] inpath '数据的path' [overwrite] into table 表名 [partition (partcol1=val1,…)];
​	解释：
​		local:表示从本地加载数据到hive表；否则从HDFS加载数据到hive表
​		partition:表示上传到指定分区
​	②通过查询语句向表中插入数据（Insert）
​	insert overwrite table student 
​	             select id, name from student where month='201709';
​	③查询语句中创建表并加载数据（As Select）
​	-- 根据查询结果创建表（查询的结果会添加到新创建的表中）
​	create table if not exists student3
​	        as select id, name from student;
​	④创建表时通过Location指定加载数据路径
​	create external table if not exists student5(
​              id int, name string
​              )
​              row format delimited fields terminated by '\t'
​              location '/student;  -- 创建表，并指定在hdfs上的位置
​    ⑤Import数据到指定Hive表中
​    import table student2  from
​	 '/user/hive/warehouse/export/student';
​	 -- 注意：先用export导出后，再将数据导入。

### 18.数据导出

​	①Insert导出
​	-- 将查询的结果格式化导出到本地
​	insert overwrite local directory '/opt/module/hive/datas/export/student1'
​	           ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'       
​	           select * from student;
​	-- 将查询的结果导出到HDFS上(没有local)
​	insert overwrite directory '/user/atguigu/student2'
​	             ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t' 
​	             select * from student;         
​	②Export导出到HDFS上
​	export table default.student to
 	 '/user/hive/warehouse/export/student';  

### 19.查询

​	①基本查询
​	SELECT [ALL | DISTINCT] select_expr, select_expr, ...
​	  FROM table_reference
​	  [WHERE where_condition]
​	  [GROUP BY col_list]
​	  [ORDER BY col_list]
​	  [CLUSTER BY col_list
​	    | [DISTRIBUTE BY col_list] [SORT BY col_list]
​	  ]
​	 [LIMIT number]  -- 限制返回的行数
​	执行顺序：
​	1st) FROM字句:执行顺序为从后往前、从右到左。数据量较大的表尽量放在后面。
​	2nd) WHERE字句：执行顺序为自下而上、从右到左。将能过滤掉最大数量记录的条件写在WHERE字句的最右。
​	3rd) GROUP BY:执行顺序从右往左分组，最好在GROUP BY前使用WHERE将不需要的记录在GROUP BY之前过滤掉
​	4th) HAVING字句：消耗资源。尽量避免使用，HAVING会在检索出所有记录之后才对结果进行过滤，需要排序等操作。
​	5th) SELECT字句：少用号，尽量使用字段名称，oracle在解析的过程中，通过查询数据字典将号依次转换成所有列名，消耗时间。
​	6th) ORDER BY字句：执行顺序从左到右，消耗资源

	别名：AS
	
	常用的聚合函数：
		求总行数（count）
		求最大、小值（max、min）
		求和：sum
		均值：avg
	
	Like和RLike：使用LIKE运算选择类似的值，RLIKE子句是Hive中这个功能的一个扩展，其可以通过Java的正则表达式这个更强大的语言来指定匹配条件。
	
	②分组
	GROUP BY语句通常会和聚合函数一起使用，按照一个或者多个列队结果进行分组，然后对每个组执行聚合操作。
	
	Having语句
	having与where不同点
	（1）where后面不能写分组函数，而having后面可以使用分组函数。
	（2）having只用于group by分组统计语句。
	
	③Join
	Hive支持通常的SQL JOIN语句，但是只支持等值连接，不支持非等值连接。
	
	内连接
	只有进行连接的两个表中都存在与连接条件相匹配的数据才会被保留下来。
	
	左外连接
	JOIN操作符左边表中符合WHERE子句的所有记录将会被返回。
	
	右外连接
	JOIN操作符右边表中符合WHERE子句的所有记录将会被返回。
	
	满外连接
	将会返回所有表中符合WHERE语句条件的所有记录。如果任一表的指定字段没有符合条件的值的话，那么就使用NULL值替代。
	
	多表连接
		SELECT e.ename, d.dname, l.loc_name
		FROM   emp e 
		JOIN   dept d
		ON     d.deptno = e.deptno 
		JOIN   location l
		ON     d.loc = l.loc;
	注意：连接 n个表，至少需要n-1个连接条件
	
	大多数情况下，Hive会对每对JOIN连接对象启动一个MapReduce任务。本例中会首先启动一个MapReduce job对表e和表d进行连接操作，然后会再启动一个MapReduce job将第一个MapReduce job的输出和表l进行连接操作。
	
	优化：当对3个或者更多表进行join连接时，**如果每个on子句都使用相同的连接键的话，**那么只会产生一个MapReduce job。

### 20.排序

​	全局排序
​		Order By：全局排序，只有一个Reducer
​		ASC（ascend）: 升序（默认）
​		DESC（descend）: 降序



### 21.hive的优化方式

​	1 fetch抓取（凡此）
​	2 本地模式
​	3 表的优化
​	4 设置map及reduce数
​	5 并行执行
​	6 严格模式
​	7 jvm重用
​	8 推测执行
​	9 压缩
​	10 执行计划
