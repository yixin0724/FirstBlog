---
title: Elasticsearch相关
date: 2023-09-14 11:20:46
tags: 
  - ES
  - Java
cover: https://wltc2-1258834326.cos.ap-guangzhou.myqcloud.com/2023/09/14/65027d3180be8.jpg
categories: 技术记录
description: 先简单学习一下ES搜索。
---





### ES简介

​	Elasticsearch 是由Apache开源的一个兼有搜索引擎和NoSQL数据库功能的系统

### ES特点

​	基于Java/Lucene构建，支持全文搜索、结构化搜索
​	低延迟，支持实时搜索
​	分布式部署，可横向集群扩展
​	支持百万级数据
​	支持多条件复杂查询，如聚合查询
​	高可用性，数据可以进行切片备份
​	支持Restful风格的api调用

### ES应用场景

​	监控，针对日志类数据进行存储、分析、可视化。针对日志数据，ES给出了ELK的解决方案。其中logstash采集日志，ES进行复杂的数据分析，kibana进行可视化展示。

	电商网站，用于商品信息检索。
	
	Json文档数据库，用于存放json格式的文档
	
	维基百科，提供全文搜索并高亮关键字

### ES核心概念

​	集群（Cluster): 包含一个或多个具有相同 cluster.name 的节点.

	节点(node): 是一个逻辑上独立的服务，可以存储数据，并参与集群的索引和搜索功能, 每个节点都有其唯一的名字，集群通过节点名称进行管理和通信。节点可以充当一个或多个角色。ES集群中的每个节点都会存储集群状态，知道索引内各分片所在的节点位置。
	
	主节点（master node）：主要负责集群方面的操作，比如节点的加入和退出、索引的创建和删除、分片被分配到哪个节点、节点状态监测。
	
	数据节点（Data Node）：存储文档数据的节点，执行文档数据的查询和写入等操作。
	
	协调节点（Coordinate Node）：客户端请求可以发送到集群的任何节点，集群中的每个节点都知道所有文档的位置。接收到客户端请求的节点自动变为协调节点，进行请求的转发，并整合数据返回给客户端。比如创建索引的请求，就转发到主节点。
	
	映射（Mapping）： mapping是对索引库中的索引字段及其数据类型进行定义，类似于关系型数据库中的表结构。ES默认动态创建索引和索引类型的mapping，这就像是关系型数据库中无需定义表结构，更不用指定字段的数据类型。也可以手动指定mapping类型。mapping机制可以自动检测数据的结构和类型，创建索引并使数据可搜索。
	
	分片（shard）：索引数据量很大，超过硬件存放单个文件的限制，就会影响查询请求的速度，Es引入了分片技术。一个分片本身就是一个完成的搜索引擎，文档存储在分片中，而分片会被分配到集群中的各个节点中，随着集群的扩大和缩小，ES会自动的将分片在节点之间进行迁移，以保证集群能保持平衡。一个索引中含有shard的数量，默认值为5，在索引创建后这个值是不能被更改的。
	
	副本（replica）：切片（shard）的冗余备份，每个切片默认的副本数为1。副本数可以随时进行调整。
	
	索引（Index)： 索引与关系型数据库实例(Database)相当。索引只是一个逻辑命名空间。ES可以把索引数据存放到服务器中，也可以sharding(分片)后存储到多台服务器上。每个索引有一个或多个分片，每个分片可以有多个副本。
	
	文档类型（Type）：相当于数据库中的table概念。每个文档在ElasticSearch中都必须设定它的类型。文档类型使得同一个索引中在存储结构不同文档时，只需要依据文档类型就可以找到对应的参数映射(Mapping)信息，方便文档的存取
	
	文档（Document) ：相当于数据库中的row， 是可以被索引的基本单位。其可以理解为关系型数据库中表的一行数据记录。每个文档由多个字段（field）组成，区别于关系型数据库的是，ES是一个非结构化的数据库，每个文档可以有不同的字段，并且有一个唯一标识。

### ES和关系型数据库概念对比

​	ES   关系型数据库

	索引（Index）  数据库（DataBase）
	
	类型（Type）  表（Table）
	
	映射（mapping）  表结构（Schema）
	
	文档（Document）  行（Row）
	
	字段（Field）  列（Column）
	
	反向索引   正向索引
	
	DSL查询   SQL查询



1.windows版安装包下载地址：https://www.elastic.co/cn/downloads/elasticsearch

2.目录结构
	- bin目录：包含所有的可执行命令
	- config目录：包含ES服务器使用的配置文件
	- jdk目录：此目录中包含了一个完整的jdk工具包，版本17，当ES升级时，使用最新版本的jdk确保不会出现版本支持性不足的问题
	- lib目录：包含ES运行的依赖jar文件
	- logs目录：包含ES运行后产生的所有日志文件
	- modules目录：包含ES软件中所有的功能模块，也是一个一个的jar包。和jar目录不同，jar目录是ES运行期间依赖的jar包，modules是ES软件自己的功能jar包
	- plugins目录：包含ES软件安装的插件，默认为空

3.启动服务器
	双击elasticsearch.bat文件即可启动ES服务器，默认服务端口9200。通过浏览器访问http://localhost:9200。

4.操作索引
	可以把他看成是一个数据库，我们操作他需要库，所以在这里它叫做索引，先创建索引
	①创建索引
		用restful风格，put请求http://localhost:9200/books，即创建books索引
	发送请求后，看到如下信息即索引创建成功
		{
	    "acknowledged": true,
	    "shards_acknowledged": true,
	    "index": "books"
		}
	②查询索引
		GET请求		http://localhost:9200/books
	③删除索引
		DELETE请求	http://localhost:9200/books
	④创建索引并指定分词器
		注意：在这里需要用到分词器，我们常用的是IK分词器，默认的是standard分词器，他是一个插件，直接解压在es中的plugs目录即可
	在这里使用分词器需要在请求网址添加请求体，使用json格式
		PUT请求		http://localhost:9200/books
		请求参数如下（注意是json格式的参数）
		{
		    "mappings":{							#定义mappings属性，替换创建索引时对应的mappings属性		
		        "properties":{						#定义索引中包含的属性设置
		            "id":{							#设置索引中包含id属性
		                "type":"keyword"			#当前属性可以被直接搜索
		            },
		            "name":{						#设置索引中包含name属性
		                "type":"text",              #当前属性是文本信息，参与分词  
		                "analyzer":"ik_max_word",   #使用IK分词器进行分词             
		                "copy_to":"all"				#分词结果拷贝到all属性中
		            },
		            "type":{
		                "type":"keyword"
		            },
		            "description":{
		                "type":"text",	                
		                "analyzer":"ik_max_word",                
		                "copy_to":"all"
		            },
		            "all":{							#定义属性，用来描述多个字段的分词结果集合，当前属性可以参与查询
		                "type":"text",	                
		                "analyzer":"ik_max_word"
		            }
		        }
		    }
		}
5.操作文档
	这里面数据叫做文档
	①添加文档，有三种方式
		POST请求	http://localhost:9200/books/_doc		#使用系统生成id
		POST请求	http://localhost:9200/books/_create/1	#使用指定id
		POST请求	http://localhost:9200/books/_doc/1		#使用指定id，不存在创建，存在更新（版本递增）

		文档通过请求参数传递，数据格式json
		{
		    "name":"springboot",
		    "type":"springboot",
		    "description":"springboot"
		}  
	②查询文档
		GET请求	http://localhost:9200/books/_doc/1		 #查询单个文档 		
		GET请求	http://localhost:9200/books/_search		 #查询全部文档
	③条件查询
		GET请求	http://localhost:9200/books/_search?q=name:springboot	# q=查询属性名:查询属性值
	④删除文档
		DELETE请求	http://localhost:9200/books/_doc/1
	⑤修改文档（全量更新）
		PUT请求	http://localhost:9200/books/_doc/1
		文档通过请求参数传递，数据格式json
		{
		    "name":"springboot",
		    "type":"springboot",
		    "description":"springboot"
		}
	⑥修改文档（部分更新）	
		POST请求	http://localhost:9200/books/_update/1
		文档通过请求参数传递，数据格式json
		{			
		    "doc":{						#部分更新并不是对原始文档进行更新，而是对原始文档对象中的doc属性中的指定属性更新
		        "name":"springboot"		#仅更新提供的属性值，未提供的属性值不参与更新操作
		    }
		}
