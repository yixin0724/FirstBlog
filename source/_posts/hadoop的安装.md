---
title: hadoop的安装
date: 2022-11-15 16:52:30
cover: https://tva1.sinaimg.cn/large/008waiCQgy1h8ivd3s53sj32yo1o0npd.jpg
tags: Hadoop
categories: hadoop
description: hadoop,他的坑要比hive多的多的多。
---





# 流程

```
安装虚拟机
ip选取VMware Network Adapter VMnet8:修改后面那个即可

规划节点和IP地址
node1 192.168.000.80 NN
node2 192.168.000.81 DN
node3 192.168.000.82 SN

虚拟机设置管理员
用户名：huser
密码：1234

使用MobaXtem连接192.168.2.80（如果连接不上需要打开控制面板->网络和Internet->网络和共享中心->更改适配器设置->启用两个以太网VMware）
安装基础工具 
sudo yum install net-tools
sudo yum install vim

在huser的~目录下新建文件夹 mkdir software，修改权限 chmod -R 777 software
将Hadoop和jdk(后缀为gz的)安装包放到soft文件夹下，注意，Java版本要求Java8及以下

rpm -qa|grep java //查看已安装的openjdk
rpm -e --nodeps 加名字	//卸载已经安装openjdk的

然后进入到上传目录：cd software  
tar -zxvf jdk-8u351-linux-x64.tar.gz #解压压缩包

解压完成后，当前目录会有一个jdk1.8.0_351的文件夹。将文件夹移动到/usr/local/java下（一般安装的软件都会放到/usr/local/目录下）。
mv jdk1.8.0_351/  /usr/local/java #将文件移动到usr/local/目录下，并将文件夹名改为java

vim /etc/profile #编辑profile文件，设置环境变量
在文件底部写入下面内容：
JAVA_HOME=/usr/local/java
JRE_HOME=$JAVA_HOME/jre
PATH=$PATH:$JAVA_HOME/bin:$JRE_HOME/bin
CLASSPATH=.:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar:$JRE_HOME/lib
export JAVA_HOME JRE_HOME PATH CLASSPATH

source /etc/profile //使文件生效
验证是否成功：
	java -version
	javac -version


使用sudo tar -zxvf hadoop-3.3.4.tar.gz -C /usr/local/ 将Hadoop解压到/usr/local目录下
使用sudo chown -R huser:huser /usr/local/hadoop-3.3.4/  将hadoop-3.3.4改为huser用户组的huser用户


关闭防火墙（如果不关闭可能出现节点间无法通信的情况）
sudo systemctl stop firewalld.service （停止防火墙）
sudo systemctl disable firewalld.service （彻底关闭防火墙）

关闭selinux（防止传输文件时出问题）
sudo vim /etc/selinux/config
修改为 SELINUX=disabled

添加hadoop环境变量（把hdfs命令直接加到环境变量中，这样在任意地方执行hdfs命令都可以，不需要在进入hadoop-3.3.4/bin目录下执行），也可以直接在/etc/profile里改，但为了方便维护，就直接在profile.d文件夹下新增一个.sh文件hdfs.sh，如果后期不想要这个命令，可直接删除hdfs.sh文件hdfs
新增一个sh文件sudo vim /etc/profile.d/hdfs.sh，填入如下内容：
export HADOOP_HOME=/usr/local/hadoop-3.3.4
export PATH=$PATH:$HADOOP_HOME/bin:$HADOOP_HOME/sbin

vim /etc/profile
 
export HADOOP_HOME=/usr/local/hadoop-3.3.4
export PATH=$PATH:$HADOOP_HOME/bin
export PATH=$PATH:$HADOOP_HOME/sbin
source /etc/profile 

创建HDFS的NN和DN工作主目录:
cd /usr/local/hadoop-3.3.4
sudo mkdir tmp 
sudo mkdir var 
sudo mkdir dfs 
sudo mkdir dfs/name 
sudo mkdir dfs/data

sudo chown -R huser:huser /usr/local/hadoop-3.3.4/dfs
sudo chown -R huser:huser /usr/local/hadoop-3.3.4/tmp
sudo chown -R huser:huser /usr/local/hadoop-3.3.4/var


配置Hadoop（一般.sh文件都是寻找Java运行环境，因此主要配置JAVA_HOME）
进入 cd /usr/local/hadoop-3.3.4/etc/hadoop 目录下
vim hadoop-env.sh
(注意路径为你的java路径)
修改export JAVA_HOME=/usr/local/java

为Yarn任务、资源管理器提供Java运行环境（hadoop-3.3.4无需配置）
vim yarn-env.sh
(注意路径为你的java路径)
export JAVA_HOME=/usr/local/java

vim mapred-env.sh
export JAVA_HOME=/usr/local/java

配置HDFS主节点信息、持久化和数据文件的主目录（如果tab不是4个空格，改一下sudo vim /etc/vimrc，添加set ts=4）
cd /usr/local/hadoop-3.3.4/etc/hadoop
        vim core-site.xml   在<configuration>中添加如下配置
	<property>
		<name>fs.defaultFS</name>
		<value>hdfs://node1:9000</value>
	</property>

	<property>
		<name>hadoop.tmp.dir</name>
		<value>/usr/local/hadoop-3.3.4/tmp</value>
	</property>

	<property>
	    <name>hadoop.proxyuser.huser.hosts</name>
	    <value>*</value>
	</property>

	<property>
	    <name>hadoop.proxyuser.huser.groups</name>
	    <value>*</value>
	</property>


			
配置HDFS默认的数据存放策略
        vim hdfs-site.xml
	<property>
		<name>dfs.name.dir</name>
		<value>/usr/local/hadoop-3.3.4/dfs/name</value>
	</property>

	<property>
	    <name>dfs.data.dir</name>
	    <value>/usr/local/hadoop-3.3.4/dfs/data</value>
	</property>


	<property>
		<name>dfs.namenode.secondary.http-address</name>
		<value>node3:9868</value>
	</property>

	<property>
	    <name>dfs.replication</name>
	    <value>2</value>
	</property>

配置mapreduce任务调度策略
    vim mapred-site.xml 
	 
	<property>
	    <name>mapred.local.dir</name>
	    <value>/usr/local/hadoop-3.3.4/var</value>
	</property>
	 
	<property>
	    <name>mapreduce.framework.name</name>
	    <value>yarn</value>
	</property>


			
配置Yarn资源管理角色的信息
        vim yarn-site.xml

    <property>
		<name>yarn.resourcemanager.hostname</name>
		<value>node1</value>
	</property>

	<property>
	    <name>yarn.nodemanager.aux-services</name>
	    <value>mapreduce_shuffle</value>
	</property>




配置datanode节点信息
vim workers
node1
node2
node3
			
提前准备主机名解析文件，为后面的克隆机器做好准备（可选，若不做，克隆后为每台机器重新添加亦可，不用删除hosts自带的）
    sudo vim /etc/hosts
192.168.000.80  node1
192.168.000.81  node2
192.168.000.82  node3
		
重启 sudo reboot


克隆其他集群信息
    关闭机器，准备克隆
    克隆后，修改node2、node3的IP和主机名
	 修改主机名sudo vim /etc/hostname 
     修改IP:sudo vim /etc/sysconfig/network-scripts/ifcfg-ens32
	然后重启：sudo reboot
	
下面开始配置集群的ssh免密
    在3台机器上执行产生自己的公钥：
        ssh-keygen -t rsa
        ///home/yixin/.ssh/id_rsa
    按照默认值回车确定
    将每台机器的公钥拷贝给每台机器，注意下面的指令要求3台机器都要执行：
        ssh-copy-id node1
        ssh-copy-id node2
        ssh-copy-id node3
		
格式化hdfs(三台集群都要)
hdfs namenode -format

开启集群
start-all.sh
关闭
stop-all.sh



日志的地方
/usr/hadoop-3.3.4/logs 

http://192.168.000.80:9870

        

```

