---
title: redis知识点
date: 2022-12-02 16:44:08
tags:
  - Java
  - Redis
cover: https://pic.imgdb.cn/item/66ebf0d1f21886ccc0d21961.jpg
categories: 技术记录
description: redis还是强，浅浅学一下redis吧。
---







## Redis

#### 1.概述：

​	Redis是一款采用key-value数据存储格式的内存级NoSQL数据库
​	特点：支持多种数据存储格式，支持持久化，支持集群
​		●基于内存存储，读写性能高
​		●适合存储热点数据(咨询，新闻，热点商品)

	Redis是用C语言开发的一个开源的高性能键值对(key-value)数据库，官方提供的数据是可以达到100000+的QPS (每秒内查询次数)。它存储的value类型比较丰富，也被称为结构化的NoSql数据库。
	NoSql (Not OnlySQL)，不仅仅是SQL,泛指非关系型数据库。NoSql数据库并不是要取代关系型数据库,而是关系型数
	据库的补充。
	
	Redis应用场景
		➢缓存
		➢任务队列
		➢消息队列
		➢分布式锁



#### 2.安装与使用

​	Redis下载与安装
​	在Linux系统安装Redis步骤:
​	1.将Redis安装包上传到Linux
​	2.解压安装包，命令: tar -zxvf redis-4.0.0.tar.gz -C /usr/local
​	3.安装Redis的依赖环境gcc,命令: yum install gcc-c++
​	4.进入/usr/local/redis-4.0.0，进行编译，命令: make
​	5.进入redis的src目录，进行安装，命令: make install

	开启服务
	进入到/usr/local/redis-4.0.0/src目录下执行
	./redis-server
	
	设置服务后台执行
	先去redis.cof进行修改配置文件，寻找dae那一行，将no改为yes，即为后台运行
	然后在根目录下执行
	src/redis-server ./redis.conf
	
	设置在连接时输入密码
	进入conf配置文件搜索/requirepass，注释关掉以后，后面那个就是密码
	设置完以后使用src/redis-cli -h localhost -p 6379
	然后进入以后输入  auth 密码或者直接加上-a 密码
	exit进行退出
	
	设置远程连接
	进入conf配置文件搜索bind，它默认只设置127.0.0.1，把他注释掉即可
	
	客户端连接
	进入到/usr/local/redis-4.0.0/src目录下执行
	./redis-cli
	
	使用
	keys *  #进行查看所有key





	window启动
	在redis目录启动服务器：
	redis-server.exe redis.windows.conf
	启动客户端：
		redis-cli.exe
	注意：在这里启动服务器发现失败了，这时启动客户端然后输入shutdown后在输入exit，然后重新启动服务器即可
	端口默认6379。



#### 3.数据类型

​	Redis存储的是key-value结构的数据，其中key是字符串类型，value有5种常用的数据类型:
​		●字符串 string(常用)
​		●哈希 hash(适合存储对象)
​		●列表 list(安装插入排序排序，可以有重复元素)
​		●集合 set(无序集合，没有重复元素)
​		●有序集合 sorted set(有序集合，没有重复元素)

#### 4.常用命令

​	Redis中字符串类型常用命令:
​		●SET key value
​			设置指定key的值
​		GET key
​			获取指定key的值
​		●SETEX key seconds value
​			设置指定key的值，井将key的过期时间设为seconds秒(验证码)
​		●SETNX key value
​			只有在key不存在时设置key的值(分布式锁)
​		注意：
​			set默认是字符串
​			如果后续对已存在的key继续进行set，后面的覆盖掉前面的
​			如果没有值的话显示为(nil)


	哈希hash操作命令
	Redis hash是一个string类型的field和value的映射表，hash特别适合用于存储对象，常用命令:
	●HSET key field value
		将哈希表key中的字段field的值设为value
	●HGET key field
		获取存储在哈希表中指定字段的值
	●HDEL key field
		删除存储在哈希表中的指定字段
	●HKEYS key
		获取哈希表中所有字段
	●HVALS key
		获取哈希表中所有值
	●HGETALL key
		获取在哈希表中指定key的所有字段和值


	列表list操作命令
	Redis列表是简单的字符串列表，他的值是一个列表形式，也就是一个key对应多个值，他是一个双向队列，按照插入顺序排序，常用命令:
	●LPUSH key value1 [value2]
		将一个或多个值插入到列表头部
	●LRANGE key start stop
		获取列表指定范围内的元素
		如lrange mylist 0 -1：获取所有，顺序是从后往前输出
	●RPOP key
		移除并获取列表最后一个元素，也就是把输出的最后一个删除
	●LLEN key
		获取列表长度
	●BRPOP key1 [key2] timeout
		移出并获取列表的最后一个元素，如果列表没有元素会阻塞列表直到等待超时或发现可弹出元素为止
	注意：这里的l和r对应左和右


	集合set操作命令
	Redis set是string类型的无序集合。集合成员是唯一的，这就意味着集合中不能出现重复的数据，常用命令:
	●SADD key member1 [member2]
		向集合添加一个或多个成员
	●SMEMBERS key
		返回集合中的所有成员.
	●SCARD key
		获取集合的成员数
	●SINTER key1 [key2]
		返回给定所有集合的交集
	●SUNION key1 [key2]
		返回所有给定集合的并集
	●SDIFF key1 [key2]
		返回给定所有集合的差集
		注意谁在前谁在后，输出的是前面集合元素中后面集合没有的
		他不是两个set的差和
	●SREM key member1 [member2]
		移除集合中一个或多个成员


	有序集合sorted set操作命令
	Redis sorted set有序集合是string类型元素的集合,且不允许重复的成员。每个元素都会关联一个double类型的分数
	(score)。redis正是通过分数来为集合中的成员进行从小到大排序。有序集合的成员是唯一的，但分数却可以重复。
	常用命令:
	●ZADD key score1 member1 [score2 member2]
		向有序集合添加一个或多个成员，或者更新已存在成员的分数
	●ZRANGE key start stop [WITHSCORES]
		通过索引区间返回有序集合中指定区间内的成员
	●ZINCRBY key increment member
		有序集合中对指定成员的分数加上增量increment，会影响排序
	●ZREM key member [member ..]
		移除有序集合中的一个或多个成员

#### 5.通用命令

​	●KEYS pattern
​		查找所有符合给定模式(pattern)的key
​		如keys *
​	●EXISTS key
​		检查给定key是否存在
​	●TYPE key
​		返回key所储存的值的类型
​	●TTL key
​		返回给定key的剩余生存时间(TTL, time to live),以秒为单位
​	●DEL key
​		该命令用于在key存在是删除key
​	●select 1
​		表示切换到1号数据库

#### 6.在java中操作redis

​	Redis的Java客户端很多，官方推荐的有三种:
​	●Jedis
​	●Lettuce
​	●Redisson
​	Spring对Redis客户端进行了整合,提供了Spring Data Redis,在Spring Boot项目中还提供了对应的Starter,即
​	spring-boot-starter-data-redis


	Jedis
	Jedis的maven坐标:
		<dependency>
			<groupld>redis.clients</groupld>
			<artifactld>jedis</artifactld>
			<version>2.8.0</version>
		</dependency>
	使用Jedis操作Redis的步骤:
	①获取连接
		Jedis jedis = new Jedis("localhost",6379);
	②执行操作
		比如操作字符串类型的(方法和redis中的一样)
		jedis.set("username","xiaoming")
	③关闭连接
		jedis.close()
	注意：需要先启动redis服务


	Spring Data Redis
	在Spring Boot项目中，可以使用Spring Data Redis来简化Redis操作, maven坐标:
	<dependency>
		<groupld>org.springframework.boot</groupld>
		<artifactld>spring-boot-starter-data-redis</artifactld>
	</dependency>
	
	Spring Data Redis中提供了一个高度封装的类: RedisTemplate,针对jedis客户端中大量api进行了归类封装,将同一类型操作封装为operation接口，具体分类如下:
		●ValueOperations: 简单K-V操作(也就是字符串)
		●SetOperations: set类型数据操作
		●ZSetOperations: zset类 型数据操作
		●HashOperations:针对map类型的数据操作
		●ListOperations: 针对list类型的数据操作
	注意：他们都是通过RedisTemplate调用方法获得
	
	boot配置文件进行redis配置
	spring:
		redis:
			host: LocaLhost
			port: 6379
			#password: 123456 
			database: 0	
			#redis默认有16个数据库，这里使用0号数据库，默认也是0，默认数据库数量可以在配置文件改
			jedis:
				#Redis连接池配置
				pool:
					max-active: 8 #最大连接数
					max-wait: 1ms #连接池最大阻塞等待时间
					max-idle: 4 #连接池中的最大空闲连接
					min-idle: 0 #连接池中的最小空闲连接
	
	对应数据类型的使用
	①先注入RedisTemplate对象
		(不需要自动注入，因为在导入的坐标中有个叫boot-autoconfigure里面jar包有个叫做spring.factories，前提是你没有创建redisTemplate的类)
	②调用方法
		redisTemplate.opsForValue()返回ValueOperations
			如redisTemplate.opsForValue().set("city","beijing"，10l,TimeUnit.SECONDS)，注意在这里redisTemplate操作后默认存入到redis键和值进行了序列化。我们可以配置修改key的序列化方法，value不用修改，get时会自动反序列化
		redisTemplate.opsForHash()返回HashOperations
		redisTemplate.opsForList()返回ListOperations
		redisTemplate.opsForSet()返回SetOperations
		redisTemplate.opsForZSet()返回的是ZSetOperations
	
	③然后继续调用接口里面的方法即可
		接口里的方法和redis各个类型对应的操作类似
	
	注意：自定义如下配置类进行修改redisTemplate默认序列化方法
@Configuration
public class RedisConfig extends CachingConfigurerSupport {

    @Bean
    public RedisTemplate<Object, Object> redisTemplate(RedisConnectionFactory connectionFactory) {
    
        RedisTemplate<Object, Object> redisTemplate = new RedisTemplate<>();
    
        //默认的Key序列化器为：JdkSerializationRedisSerializer
        redisTemplate.setKeySerializer(new StringRedisSerializer());
        redisTemplate.setHashKeySerializer(new StringRedisSerializer());
    
        redisTemplate.setConnectionFactory(connectionFactory);
    
        return redisTemplate;
    }

}
