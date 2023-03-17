---
title: Mysql知识点
date: 2023-03-17 19:46:35
cover: https://wltc2-1258834326.cos.ap-guangzhou.myqcloud.com/2023/03/17/64145f8c5b8f2.png
tags: 
  - Mysql
  - 数据库
categories: 技术记录
description: 希望他收费的那一天不会来临！
---





# MySql

### 1.数据库相关概念

​	数据库：存储数据的仓库，数据是有组织的进行存储 简称DataBase（DB）
​	数据库管理系统：操纵和管理数据库的大型软件，简称DataBase Management System (DBMS)
​	SQL：操作关系型数据库的编程语言，定义了一套操作关系型数据库统一标准，简称Structured Query Language (SQL)

### 2.各个数据库

​	Oracle：大型的收费数据库，Oracle公司产品，价格昂贵。
​	MySQL：开源免费的中小型数据库，后来Sun公司收购了MySQL，而Oracle又收购了Sun公司。目前Oracle推出了收费版本的MySQL，也提供了免费的社区版本。
​	SQL Server：Microsoft 公司推出的收费的中型数据库，C#、.net等语言常用。
​	PostgreSQL：开源免费的中小型数据库。
​	DB2：IBM公司的大型收费数据库产品。
​	SQLLite：嵌入式的微型数据库。Android内置的数据库采用的就是该数据库。
​	MariaDB：开源免费的中小型数据库。是MySQL数据库的另外一个分支、另外一个衍生产品，与MySQL数据库有很好的兼容性。

### 3.版本

​	社区版本（MySQL Community Server）：免费，MySQL不提供任何技术支持
​	商业版本（MySQL Enterprise Edition）：收费，可以使用30天，官方提供技术支持

### 4.数据模型

​	关系型数据库（RDBMS）
​		概念：建立在关系模型基础上，由多张相互连接的二维表组成的数据库。
​		而所谓二维表，指的是由行和列组成的表，如下图（就类似于Excel表格数据，有表头、有列、有行，还可以通过一列关联另外一个表格中的某一列数据）。我们之前提到的MySQL、Oracle、DB2、SQLServer这些都是属于关系型数据库，里面都是基于二维表存储数据的。简单说，基于二维表存储数据的数据库就成为关系型数据库，不是基于二维表存储数据的数据库，就是非关系型数据库。
​	特点：
​		A. 使用表存储数据，格式统一，便于维护。
​		B. 使用SQL语言操作，标准统一，使用方便。

### 5.SQL概念

​	概念：全称 Structured Query Language，结构化查询语言。操作关系型数据库的编程语言，定义了一套操作关系型数据库统一标准 。

### 6.SQL通用语法

​		①SQL语句可以单行或多行书写，以分号结尾。
​		②/缩进来增强语句的可读性。
​		③MySQL数据库的SQL语句不区分大小写，关键字建议使用大写。
​		④注释：
​			单行注释：-- 注释内容 或 # 注释内容
​			多行注释：/* 注释内容 */

### 7.SQL分类

​	SQL语句，根据其功能，主要分为四类：DDL、DML、DQL、DCL。
​	DDL：全称Data Definition Language，数据定义语言，用来定义数据库对象(数据库，表，字段)
​	DML：Data Manipulation Language，数据操作语言，用来对数据库表中的数据进行增删改
​	DQL：Data Query Language，数据查询语言，用来查询数据库中表的记录
​	DCL：Data Control Language，数据控制语言，用来创建数据库用户、控制数据库的访问权限

	DDL(数据定义语言)
	数据库操作
		①查询所有数据库：show databases;
		②查询当前数据库：select database();
		③创建数据库：create database [if not exists] 数据库名 [default charset 字符集] [collate 排序规则];
		④删除数据库：drop database [if exists] 数据库名;
		⑤切换数据库：use 数据库名;
	注意：在同一个数据库服务器中，不能创建两个名称相同的数据库，否则将会报错。可以通过if not exists参数来解决这个问题，数据库不存在,则创建该数据库，如果存在，则不创建。
	
	表操作-查询创建
		①查询当前数据库所有表：show tables;
		②查看指定表结构：desc 表名;
		③查询指定表的建表语句：show create table 表名;
		④创建表结构：
			CREATE TABLE 表名(
				字段1 字段1类型 [COMMENT 字段1注释 ],
				字段2 字段2类型 [COMMENT 字段2注释 ],
				字段3 字段3类型 [COMMENT 字段3注释 ],
				......
				字段n 字段n类型 [COMMENT 字段n注释 ]
			) [COMMENT 表注释 ];
		如：
			create table tb_user(
				id int comment '编号',
				name varchar(50) comment '姓名',
				age int comment '年龄',
				gender varchar(1) comment '性别'
			) comment '用户表';
	
	表操作-数据类型
	MySQL中的数据类型有很多，主要分为三类：数值类型、字符串类型、日期时间类型。
	
	表操作-修改
		①添加字段：ALTER TABLE 表名 ADD 字段名 类型 (长度) [COMMENT 注释] [约束];
		②修改数据类型：ALTER TABLE 表名 MODIFY 字段名 新数据类型 (长度);
		③修改字段名和字段类型：ALTER TABLE 表名 CHANGE 旧字段名 新字段名 类型 (长度) [COMMENT 注释] [约束];
		④删除字段：ALTER TABLE 表名 DROP 字段名;
		⑤修改表名：ALTER TABLE 表名 RENAME TO 新表名;
	
	表操作-删除
		①删除表：DROP TABLE [IF EXISTS] 表名;
		②删除指定表, 并重新创建表：TRUNCATE TABLE 表名;
	注意: 在删除表的时候，表中的全部数据也都会被删除。


	DML(数据操作语言)
	添加数据
		①给指定字段添加数据：INSERT INTO 表名 (字段名1, 字段名2, ...) VALUES (值1, 值2, ...);
		②给全部字段添加数据：INSERT INTO 表名 VALUES (值1, 值2, ...);
		③批量添加数据：INSERT INTO 表名 (字段名1, 字段名2, ...) VALUES (值1, 值2, ...), (值1, 值2, ...), (值1, 值2, ...);
		或：INSERT INTO 表名 VALUES (值1, 值2, ...), (值1, 值2, ...), (值1, 值2, ...);
	注意事项:
		•插入数据时，指定的字段顺序需要与值的顺序是一一对应的。
		•字符串和日期型数据应该包含在引号中。
		•插入的数据大小，应该在字段的规定范围内。
	
	修改数据
		语法：UPDATE 表名 SET 字段名1 = 值1 , 字段名2 = 值2 , .... [ WHERE 条件 ] ;
	注意事项：修改语句的条件可以有，也可以没有，如果没有条件，则会修改整张表的所有数据。
	
	删除数据
		语法：DELETE FROM 表名 [ WHERE 条件 ] ;
	注意事项:
		•DELETE 语句的条件可以有，也可以没有，如果没有条件，则会删除整张表的所有数据。
		•DELETE 语句不能删除某一个字段的值(可以使用UPDATE，将该字段值置为NULL即可)。
		•当进行删除全部数据操作时，datagrip会提示我们，询问是否确认删除，我们直接点击Execute即可。


	DQL(数据查询语言)
	基本语法：
		数字代表执行顺序
		SELECT    (4)
			字段列表
		FROM      (1)
			表名列表
		WHERE     (2)
			条件列表
		GROUP BY  (3)
			分组字段列表
		HAVING    
			分组后条件列表
		ORDER BY  (5)
			排序字段列表
		LIMIT     (6)
			分页参数
	简单分为：
		基本查询（不带任何条件）
		条件查询（WHERE）
		聚合函数（count、max、min、avg、sum）
		分组查询（group by）
		排序查询（order by）
		分页查询（limit）
	
	基础查询
		①查询多个字段：
			SELECT 字段1, 字段2, 字段3 ... FROM 表名;
			或SELECT * FROM 表名 ;
		注意 : * 号代表查询所有字段，在实际开发中尽量少用（不直观、影响效率）。
		②字段设置别名：
			SELECT 字段1 [ AS 别名1 ] , 字段2 [ AS 别名2 ] ... FROM 表名;
			或SELECT 字段1 [ 别名1 ] , 字段2 [ 别名2 ] ... FROM 表名;
			-- as可以省略
		③去除重复记录：
			SELECT DISTINCT 字段列表 FROM 表名;
	
	条件查询
		语法：SELECT 字段列表 FROM 表名 WHERE 条件列表 ;
		条件：
		常用的比较运算符如下:
			>： 大于
			>=： 大于等于
			<： 小于
			<=： 小于等于
			=： 等于
			<>或!=： 不等于
			BETWEEN ... AND ...： 在某个范围之内(含最小、最大值)
			IN(...)： 在in之后的列表中的值，多选一
			LIKE 占位符： 模糊匹配(_匹配单个字符, %匹配任意个字符)
			IS NULL： 是NULL
		常用的逻辑运算符如下:
			AND 或 &&： 并且 (多个条件同时成立)
			OR 或 ||： 或者 (多个条件任意一个成立)
			NOT 或 !： 非 , 不是
	
	聚合函数
		介绍：
			将一列数据作为一个整体，进行纵向计算 。
	
		常见的聚合函数：
			count：统计数量
			max：最大值
			min：最小值
			avg：平均值
			sum：求和
	
		语法：
			SELECT 聚合函数(字段列表) FROM 表名 ;
		注意 : 
			①NULL值是不参与所有聚合函数运算的。
			②对于count聚合函数，统计符合条件的总记录数，还可以通过 count(数字/字符串)的形式进行统计查询
	
	分组查询
		语法：
			SELECT 字段列表 FROM 表名 [ WHERE 条件 ] GROUP BY 分组字段名 [ HAVING 分组 后过滤条件 ];
	
		where与having区别
			①执行时机不同：where是分组之前进行过滤，不满足where条件，不参与分组；而having是分组之后对结果进行过滤。
			②判断条件不同：where不能对聚合函数进行判断，而having可以。
	
		注意事项:
			• 分组之后，查询的字段一般为聚合函数和分组字段，查询其他字段无任何意义。
			• 执行顺序: where > 聚合函数 > having 。
			• 支持多字段分组, 具体语法为 : group by columnA,columnB


	排序查询
		语法：
			SELECT 字段列表 FROM 表名 ORDER BY 字段1 排序方式1 , 字段2 排序方式2 ;
	
		排序方式：
			ASC : 升序(默认值)
			DESC: 降序
		注意事项：
			• 如果是升序, 可以不指定排序方式ASC ;
			• 如果是多字段排序，当第一个字段值相同时，才会根据第二个字段进行排序 ;


	分页查询
		语法：
		 	SELECT 字段列表 FROM 表名 LIMIT 起始索引, 查询记录数 ;
		注意事项:
			• 起始索引从0开始，起始索引 = （查询页码 - 1）* 每页显示记录数。
			• 分页查询是数据库的方言，不同的数据库有不同的实现，MySQL中是LIMIT。
			• 如果查询的是第一页数据，起始索引可以省略，直接简写为 limit 10。


	DCL(数据控制语言)
	管理用户
		①查询用户：select * from mysql.user;
		注意：其中 Host代表当前用户访问的主机,如果为localhost, 仅代表只能够在当前本机访问，是不可以远程访问的。 User代表的是访问该数据库的用户名。在MySQL中需要通过Host和User来唯一标识一个用户。
		②创建用户：CREATE USER '用户名'@'主机名' IDENTIFIED BY '密码';
		③修改用户密码：ALTER USER '用户名'@'主机名' IDENTIFIED WITH mysql_native_password BY '新密码' ;
		④删除用户：DROP USER '用户名'@'主机名' ;
	
		注意事项:
			• 在MySQL中需要通过用户名@主机名的方式，来唯一标识一个用户。
			• 主机名可以使用 % 通配。
			• 这类SQL开发人员操作的比较少，主要是DBA（ Database Administrator 数据库管理员）使用。
	
	权限控制
	常用：
		ALL, ALL PRIVILEGES：所有权限
		SELECT： 查询数据
		INSERT： 插入数据
		UPDATE： 修改数据
		DELETE： 删除数据
		ALTER： 修改表
		DROP： 删除数据库/表/视图
		CREATE： 创建数据库/表
	
		①查询权限：SHOW GRANTS FOR '用户名'@'主机名' ;
		②授予权限：GRANT 权限列表 ON 数据库名.表名 TO '用户名'@'主机名';
		③撤销权限：REVOKE 权限列表 ON 数据库名.表名 FROM '用户名'@'主机名';
	
		注意事项：
			• 多个权限之间，使用逗号分隔
			• 授权时， 数据库名和表名可以使用 * 进行通配，代表所有。

### 8.函数

​	MySQL中的函数主要分为以下四类： 字符串函数、数值函数、日期函数、流程函数。

	字符串函数
	常用：
		CONCAT(S1,S2,...Sn：) 字符串拼接，将S1，S2，... Sn拼接成一个字符串
		LOWER(str)： 将字符串str全部转为小写
		UPPER(str)： 将字符串str全部转为大写
		LPAD(str,n,pad)：左填充，用字符串pad对str的左边进行填充，达到n个字符串长度
		RPAD(str,n,pad)：右填充，用字符串pad对str的右边进行填充，达到n个字符串长度
		TRIM(str)： 去掉字符串头部和尾部的空格
		SUBSTRING(str,start,len)： 返回从字符串str从start位置起的len个长度的字符串
	
	数值函数
	常用：
		CEIL(x)： 向上取整
		FLOOR(x)： 向下取整
		MOD(x,y)： 返回x/y的模
		RAND()： 返回0~1内的随机数
		ROUND(x,y)： 求参数x的四舍五入的值，保留y位小数


	日期函数
	常用：
		CURDATE()： 返回当前日期
		CURTIME()： 返回当前时间
		NOW()： 返回当前日期和时间
		YEAR(date)： 获取指定date的年份
		MONTH(date)： 获取指定date的月份
		DAY(date)： 获取指定date的日期
		DATE_ADD(date, INTERVAL exprtype)： 返回一个日期/时间值加上一个时间间隔expr后的时间值
		DATEDIFF(date1,date2)：返回起始时间date1和结束时间date2之间的天数


	流程函数
	流程函数也是很常用的一类函数，可以在SQL语句中实现条件筛选，从而提高语句的效率。
	常用：
		IF(value , t , f)： 如果value为true，则返回t，否则返回f
		IFNULL(value1 , value2)： 如果value1不为空，返回value1，否则返回value2
		CASE WHEN [ val1 ] THEN [res1] ... ELSE [ default ] END： 如果val1为true，返回res1，... 否则返回default默认值
		CASE [ expr ] WHEN [ val1 ] THEN [res1] ... ELSE [ default ] END： 如果expr的值等于val1，返回res1，... 否则返回default默认值

### 9.约束

​	概念：约束是作用于表中字段上的规则，用于限制存储在表中的数据。
​	目的：保证数据库中数据的正确、有效性和完整性。
​	分类：
​		非空约束：限制该字段的数据不能为null，关键字为NOT NULL
​		唯一约束：保证该字段的所有数据都是唯一、不重复的，关键字为UNIQUE
​		主键约束：主键是一行数据的唯一标识，要求非空且唯一，关键字为PRIMARY KEY
​		默认约束：保存数据时，如果未指定该字段的值，则采用默认值，关键字为DEFAULT
​		检查约束(8.0.16版本之后)：保证字段值满足某一个条件，关键字为CHECK
​		外键约束：用来让两张表的数据之间建立连接，保证数据的一致性和完整性，关键字为FOREIGNKEY
​	注意：约束是作用于表中字段上的，可以在创建表/修改表的时候添加约束。


	外键约束
		外键：用来让两张表的数据之间建立连接，从而保证数据的一致性和完整性。
		语法：
			添加外键：
				CREATE TABLE 表名(
					字段名 数据类型,
					...
					[CONSTRAINT] [外键名称] FOREIGN KEY (外键字段名) REFERENCES 主表 (主表列名)
				);
				或：
				ALTER TABLE 表名 ADD CONSTRAINT 外键名称 FOREIGN KEY (外键字段名) REFERENCES 主表 (主表列名) ;
	
			删除外键：ALTER TABLE 表名 DROP FOREIGN KEY 外键名称;
	
	删除/更新行为
		概念：添加了外键之后，再删除父表数据时产生的约束行为，我们就称为删除/更新行为。
		分类：
			NO ACTION：当在父表中删除/更新对应记录时，首先检查该记录是否有对应外键，如果有则不允许删除/更新。 (与 RESTRICT 一致) 默认行为
			RESTRICT：当在父表中删除/更新对应记录时，首先检查该记录是否有对应外键，如果有则不允许删除/更新。 (与 NO ACTION 一致) 默认行为
			CASCADE：当在父表中删除/更新对应记录时，首先检查该记录是否有对应外键，如果有，则也删除/更新外键在子表中的记录。
			SET NULL：当在父表中删除对应记录时，首先检查该记录是否有对应外键，如果有则设置子表中该外键值为null（这就要求该外键允许取null）。
			SET DEFAULT：父表有变更时，子表将外键列设置成一个默认的值 (Innodb不支持)
		语法为:
			ALTER TABLE 表名 ADD CONSTRAINT 外键名称 FOREIGN KEY (外键字段) REFERENCES
			主表名 (主表字段名) ON UPDATE CASCADE ON DELETE CASCADE;

###  10.多表查询

 	多表关系
 		分类：一对多(多对一)，多对多，一对一

 	笛卡尔积: 笛卡尔乘积是指在数学中，两个集合A集合和B集合的所有组合情况。
 		而在多表查询中，我们是需要加上条件消除无效的笛卡尔积的，只保留两张表关联部分的数据。
 	
 	如select * from emp , dept;
 		此时,我们看到查询结果中包含了大量的结果集，总共102条记录，而这其实就是员工表emp所有的记录(17)与部门表dept所有记录(6)的所有组合情况，这种现象称之为笛卡尔积。
 	
 	分类
 	 	连接查询：
 			内连接：相当于查询A、B交集部分数据
 			左外连接：查询左表所有数据，以及两张表交集部分数据
 			右外连接：查询右表所有数据，以及两张表交集部分数据
 			自连接：当前表与自身的连接查询，自连接必须使用表别名
 		子查询
 	
 		内连接
 			内连接查询的是两张表交集部分的数剧
 			内连接的语法分为两种: 隐式内连接、显式内连接。
 	
 			隐式内连接
 				SELECT 字段列表 FROM 表1 , 表2 WHERE 条件 ... ;
 			显式内连接
 				SELECT 字段列表 FROM 表1 [ INNER ] JOIN 表2 ON 连接条件 ... ;
 		注意事项：一旦为表起了别名，就不能再使用表名来指定对应的字段了，此时只能够使用别名来指定字段。
 	
 		外连接
 		外连接分为两种分别是：左外连接和右外连接。
 		左外连接
 			SELECT 字段列表 FROM 表1 LEFT [ OUTER ] JOIN 表2 ON 条件 ... ;
 			左外连接相当于查询表1(左表)的所有数据，当然也包含表1和表2交集部分的数据。
 		右外连接
 			SELECT 字段列表 FROM 表1 RIGHT [ OUTER ] JOIN 表2 ON 条件 ... ;
 			右外连接相当于查询表2(右表)的所有数据，当然也包含表1和表2交集部分的数据。
 		注意事项：
 			左外连接和右外连接是可以相互替换的，只需要调整在连接查询时SQL中，表结构的先后顺序就可以了。而我们在日常开发使用时，更偏向于左外连接。
 	
 		自连接
 		自连接查询
 		自连接查询，顾名思义，就是自己连接自己，也就是把一张表连接查询多次。
 			SELECT 字段列表 FROM 表A 别名A JOIN 表A 别名B ON 条件 ... ;
 			而对于自连接查询，可以是内连接查询，也可以是外连接查询。
 		注意事项:
 			在自连接查询中，必须要为表起别名，要不然我们不清楚所指定的条件、返回的字段，到底是哪一张表的字段。


		联合查询
		对于union查询，就是把多次查询的结果合并起来，形成一个新的查询结果集。
			SELECT 字段列表 FROM 表A ...
			UNION [ ALL ]
			SELECT 字段列表 FROM 表B ....;
		注意事项：
			对于联合查询的多张表的列数必须保持一致，字段类型也需要保持一致。
			union all 会将全部的数据直接合并在一起，union 会对合并之后的数据去重。
			如果多条查询语句查询出来的结果，字段数量不一致，在进行union/union all联合查询时，将会报错


		子查询
		SQL语句中嵌套SELECT语句，称为嵌套查询，又称子查询。
			SELECT * FROM t1 WHERE column1 = ( SELECT column1 FROM t2 );
		子查询外部的语句可以是INSERT / UPDATE / DELETE / SELECT 的任何一个。
	
		子查询分类
			根据子查询结果不同，分为：
			A. 标量子查询（子查询结果为单个值）
			B. 列子查询(子查询结果为一列)
			C. 行子查询(子查询结果为一行)
			D. 表子查询(子查询结果为多行多列)
			根据子查询位置，分为：
			A. WHERE之后
			B. FROM之后
			C. SELECT之后
	
		标量子查询
		子查询返回的结果是单个值（数字、字符串、日期等），最简单的形式，这种子查询称为标量子查询。
		常用的操作符：= <> > >= < <= 



### 11.事务

​	介绍：事务 是一组操作的集合，它是一个不可分割的工作单位，事务会把所有的操作作为一个整体一起向系统提交或撤销操作请求，即这些操作要么同时成功，要么同时失败。
​	注意： 默认MySQL的事务是自动提交的，也就是说，当执行完一条DML语句时，MySQL会立即隐式的提交事务。

	控制事务一
		①查看/设置事务提交方式：
			SELECT @@autocommit ;
			SET @@autocommit = 0 ;
		②提交事务：COMMIT;
		③回滚事务：ROLLBACK;
	注意：上述的这种方式，我们是修改了事务的自动提交行为, 把默认的自动提交修改为了手动提交,此时我们执行的DML语句都不会提交, 需要手动的执行commit进行提交。
	
	控制事务二
		①开启事务：START TRANSACTION 或 BEGIN ;
		②提交事务：COMMIT;
		③回滚事务：ROLLBACK;
	
	四大特性(ACID)：
		原子性（Atomicity）：事务是不可分割的最小操作单元，要么全部成功，要么全部失败。
		一致性（Consistency）：事务完成时，必须使所有的数据都保持一致状态。
		隔离性（Isolation）：数据库系统提供的隔离机制，保证事务在不受外部并发操作影响的独立
		环境下运行。
		持久性（Durability）：事务一旦提交或回滚，它对数据库中的数据的改变就是永久的。


	并发事务问题
		①赃读：一个事务读到另外一个事务还没有提交的数据。
		②不可重复读：一个事务先后读取同一条记录，但两次读取的数据不同，称之为不可重复读。
		③幻读：一个事务按照条件查询数据时，没有对应的数据行，但是在插入数据时，又发现这行数据已经存在，好像出现了 "幻影"。


	事务隔离级别
	为了解决并发事务所引发的问题，在数据库中引入了事务隔离级别。主要有以下几种：
		分别对应脏读，不可重复读，幻读
		①Read uncommitted √ √ √
		②Read committed × √ √
		③Repeatable Read(默认) × × √
		④Serializable × × ×
	
		查看事务隔离级别：SELECT @@TRANSACTION_ISOLATION
		设置事务隔离级别：SET [ SESSION | GLOBAL ] TRANSACTION ISOLATION LEVEL { READ UNCOMMITTED | READ COMMITTED | REPEATABLE READ | SERIALIZABLE }
	注意：事务隔离级别越高，数据越安全，但是性能越低。
