---
title: HBase完全分布式安装
date: 2022-12-10 13:20:46
tags:
  - BigData
  - HBase
cover: https://tvax3.sinaimg.cn/large/008waiCQly1h8yov44twfj32yo1o0b2j.jpg
categories: 技术记录
description: Hadoop的生态圈之间的版本真是一个让人头大的问题，希望他能越来越完善吧！
---



## 安装流程：



```

准备工作：
	安装、搭建好JDK、MySQL、Hadoop、Zookeeper、Hive，并做好免密登录


#上传文件到/usr/software
sudo tar -zxvf hbase-2.5.0-bin.tar.gz -C  /usr/local/
cd /usr/local
sudo chmod -R 777 hbase-2.5.0/
sudo chown -R huser:huser hbase-2.5.0/

#hbase-2.5.0

#完全分布式配置
cd /usr/local/hbase-2.5.0/conf
vim hbase-env.sh

export JAVA_HOME=/usr/local/java
export HBASE_MANAGES_ZK=false
export HBASE_DISABLE_HADOOP_CLASSPATH_LOOKUP=true



vim hbase-site.xml

<configuration>

	<!-- 指定hbase在HDFS上存储的路径 -->
    <property>
	    <name>hbase.rootdir</name>
	    <value>hdfs://node1:9000/hbase</value>
    </property>

    <!-- 指定hbase是否分布式运行 -->
    <property>
	    <name>hbase.cluster.distributed</name>
	    <value>true</value>
    </property>

    <!-- 指定zookeeper的地址，多个用“,”分割 -->
    <property>
	    <name>hbase.zookeeper.quorum</name>
	    <value>node1,node2,node3:2181</value>
    </property>

    <!-- -->
	<property>
		<name>hbase.tmp.dir</name>
		<value>/usr/local/hbase-2.5.0/tmp</value>
	</property>


    <!--指定hbase管理页面-->
    <property>
		<name>hbase.master.info.port</name>
		<value>60010</value>
    </property>

    <!--在分布式的情况下一定要设置，不然容易出现Hmaster起不来的情况-->
    <property>
		<name>hbase.unsafe.stream.capability.enforce</name>
		<value>false</value>
    </property>
</configuration>

#本地模式只需要在上面配
    <property>
	    <name>hbase.rootdir</name>
	    <value>file:///usr/local/hbase-2.5.0/data</value>
    </property>
只需要在hbase-env.sh添加java路径即可
export JAVA_HOME=/usr/local/java
export HBASE_MANAGES_ZK=true

vim regionservers
localhost




vim regionservers
#指定HBase集群的从节点；原内容清空，添加如下三行
node1
node2
node3


#创建back-masters配置文件，里边包含备份HMaster节点的主机名，每个机器独占一行，实现HMaster的高可用
vim backup-masters
node2

#分发安装包
sudo scp -r hbase-2.5.0/ node2:/usr/local/
sudo scp -r hbase-2.5.0/ node3:/usr/local/


#创建软连接
注意：三台机器均做如下操作
#因为HBase集群需要读取hadoop的core-site.xml、hdfs-site.xml的配置文件信息，所以我们三台机器都要执行以下命令，在相应的目录创建这两个配置文件的软连接
ln -s /usr/local/hadoop-3.3.4/etc/hadoop/core-site.xml  /usr/local/hbase-2.5.0/conf/core-site.xml

ln -s /usr/local/hadoop-3.3.4/etc/hadoop/hdfs-site.xml  /usr/local/hbase-2.5.0/conf/hdfs-site.xml

cd /usr/local/hbase-2.5.0/conf/
ll进行查看


#添加HBase环境变量
sudo vim /etc/profile

export HBASE_HOME=/usr/local/hbase-2.5.0
export PATH=$PATH:$HBASE_HOME/bin
source /etc/profile

sudo scp -r /etc/profile node2:/etc/
sudo scp -r /etc/profile node3:/etc/
#记得在对应机器source /etc/profile



#完全分布式启动
需要提前启动HDFS及ZooKeeper集群
如果没开启hdfs，请在node1运行start-all.sh命令
如果没开启zookeeper，请在3个节点分别运行zkServer.sh start命令
hdfs dfsadmin -safemode get     #查看是否处于安全模式
hdfs dfsadmin -safemode leave  #关闭安全模式

第一台机器node01（HBase主节点）执行以下命令，启动HBase集群
方法一：
	start-hbase.sh

方法二：
	#HMaster节点上启动HMaster命令
	hbase-daemon.sh start master
	#启动HRegionServer命令
	hbase-daemon.sh start regionserver



启动完后，jps查看HBase相关进程
node1、node2上有进程HMaster、HRegionServer
node3上有进程HRegionServer
注意：
Zookeeper ：主要用于获取 meta 表的位置信息，Master 的信息；
HBase Master ：主要用于执行 HBaseAdmin 接口的一些操作，例如建表等；
HBase RegionServer ：用于读、写数据。

访问：http://192.168.217.80:60010
新的端口：16010


停止HBase集群的正确顺序
#node1上运行，关闭hbase集群
stop-hbase.sh
关闭ZooKeeper集群
关闭Hadoop集群


启动命令：
在node1
start-all.sh
在3台执行
zkServer.sh start
在node1执行
start-hbase.sh

关闭命令：
	顺序：
	在node1，node2执行
	hbase-daemon.sh stop master
	hbase-daemon.sh stop regionserver
	node3执行
	hbase-daemon.sh stop regionserver
	在node1执行
	stop-hbase.sh

	在node1，node2，node3
	zkServer.sh stop

	在node1执行stop-all.sh






```

