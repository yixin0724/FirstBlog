---
title: hive的安装
date: 2022-11-15 16:52:22
cover: https://freeimg.eu.org/i/2023/01/iw4vmd.png
tags: 
  - BigData
  - Hive
categories: hive
description: 关于安装hive时踩的超级多的坑，最终总结出来的。
---







# 流程

```
#-------------Mysql安装----------------------
只需要在主节点安装即可

#卸载Centos7自带mariadb
rpm -qa|grep mariadb
    mariadb-libs-5.5.68-1.el7.x86_64
rpm -e mariadb-libs-5.5.68-1.el7.x86_64 --nodeps
//nodeps是强制删除

sudo mkdir /usr/local/mysql
sudo chmod -R 777 /usr/local/mysql

#上传mysql-5.7.29安装包到/software下，解压
tar -xvf mysql-5.7.29-1.el7.x86_64.rpm-bundle.tar -C /usr/local/mysql

cd /usr/local/mysql

#执行安装

sudo rpm -ivh mysql-community-common-5.7.29-1.el7.x86_64.rpm mysql-community-libs-5.7.29-1.el7.x86_64.rpm mysql-community-devel-5.7.29-1.el7.x86_64.rpm mysql-community-libs-compat-5.7.29-1.el7.x86_64.rpm mysql-community-client-5.7.29-1.el7.x86_64.rpm mysql-community-server-5.7.29-1.el7.x86_64.rpm

#初始化mysql
sudo mysqld --initialize
#更改所属组
sudo chown mysql:mysql /var/lib/mysql -R

#启动mysql
systemctl start mysqld.service

#查看生成的临时root密码
cat /var/log/mysqld.log|grep password

&s9hL.kwOf=%




#修改mysql root密码、授权远程访问
mysql -u root -p
Enter password:     #这里输入在日志中生成的临时密码

#更新root密码  设置为hadoop
alter user user() identified by "hadoop";

#授权
mysql> use mysql;
mysql> GRANT ALL PRIVILEGES ON *.* TO 'root'@'%' IDENTIFIED BY 'hadoop' WITH GRANT OPTION;
mysql> FLUSH PRIVILEGES;

#退出
exit;

#mysql的启动和关闭 状态查看
systemctl stop mysqld
systemctl status mysqld
systemctl start mysqld
systemctl status mysqld

#建议设置为开机自启动服务
systemctl enable  mysqld
#查看是否已经设置自启动成功
systemctl list-unit-files | grep mysqld

netstat -tunlp
查看已经启动的服务
netstat -tunlp|grep mysql
ps -ef|grep mysql
查看mysq|进程



#--------------------Hive安装配置----------------------
# 上传解压安装包
cd /home/huser/software
tar -zxvf apache-hive-3.1.2-bin.tar.gz -C /usr/local
cd /usr/local
mv apache-hive-3.1.2-bin hive

#解决hadoop、hive之间guava版本差异（Java的一个工具包）
cd /usr/local/hive
rm -rf lib/guava-19.0.jar
cp /usr/local/hadoop-3.3.4/share/hadoop/common/lib/guava-27.0-jre.jar ./lib/

#添加mysql jdbc驱动到hive安装包lib/文件下
cd /home/huser/software/
mv mysql-connector-java-5.1.32.jar /usr/local/hive/lib/

#修改hive环境变量文件 添加Hadoop_HOME
cd /usr/local/hive/conf/
mv hive-env.sh.template hive-env.sh

vim hive-env.sh
export HADOOP_HOME=/usr/local/hadoop-3.3.4
export HIVE_CONF_DIR=/usr/local/hive/conf
export HIVE_AUX_JARS_PATH=/usr/local/hive/lib

#新增hive-site.xml 配置mysql等相关信息
vim hive-site.xml

#-----------------hive-site.xml--------------
<configuration>
    <!-- 存储元数据mysql相关配置 -->
    <property>
        <name>javax.jdo.option.ConnectionURL</name>
        <value> jdbc:mysql://localhost:3306/hive?createDatabaseIfNotExist=true&amp;useSSL=false&amp;useUnicode=true&amp;characterEncoding=UTF-8</value>
    </property>

    <property>
        <name>javax.jdo.option.ConnectionDriverName</name>
        <value>com.mysql.jdbc.Driver</value>
    </property>

    <property>
        <name>javax.jdo.option.ConnectionUserName</name>
        <value>root</value>
    </property>

    <property>
        <name>javax.jdo.option.ConnectionPassword</name>
        <value>hadoop</value>
    </property>

    <!-- 配置hiveservver2端口号和主机名 -->
	<property>
        <name>hive.server2.thrift.port</name>
        <value>10000</value>
    </property>
    <property>
        <name>hive.server2.thrift.bind.host</name>
        <value>localhost</value>
    </property>

    <!-- 远程模式部署metastore 服务地址 -->
    <property>
        <name>hive.metastore.uris</name>
        <value>thrift://node1:9083</value>
    </property>

    <!-- 关闭元数据存储授权  -->
    <property>
        <name>hive.metastore.event.db.notification.api.auth</name>
        <value>false</value>
    </property>

    <!-- 关闭元数据存储版本的验证 -->
    <property>
        <name>hive.metastore.schema.verification</name>
        <value>false</value>
    </property>

    <property>
        <name>hive.server2.enable.doAs</name>
        <value>false</value>
    </property>
</configuration>

#初始化metadata
cd /usr/local/hive
bin/schematool -initSchema -dbType mysql -verbos
#初始化成功会在mysql中的hive数据仓库创建74张表
mysql -uroot -p
密码是hadoop

use hive;
show tables;
show databases;


#-----------------Metastore Hiveserver2启动----


hive环境变量的配置
在/etc/profile.d下新建hive.sh文件，并添加环境变量信息：
sudo vim /etc/profile.d/hive.sh

添加：
export HIVE_HOME=/usr/local/hive
export PATH=$PATH:$HIVE_HOME/bin:$HIVE_HOME/sbin

保存并退出
:wq

刷新profile
source /etc/profile

启动hive命令：
①hive --service metastore
或者nohup hive --service metastore &> metastore.log &
②hive --service hiveserver2
或者nohup hive --service hiveserver2 &> hiveserver2.log &
③beeline
④在beeline输入!connect jdbc:hive2://localhost:10000
⑤输入账户密码即可 huser  1234
⑥set hive.exec.mode.local.auto=true;  # hive设置成loacl模式执行
⑥!quit  关闭beeline





set hive.support.quoted.identifiers = none;
set hive.support.concurrency=false;

# 开启分区
set hive.exec.dynamic.partition=true; 
set hive.exec.dynamic.partition.mode=nonstrict;

SET hive.support.quoted.identifiers = none; --带反引号的名称被解释为正则表达式

set hive.exec.mode.local.auto=true; 
或者在hive-site.xml
设置
    <property>
        <name>hive.exec.mode.local.auto</name>
        <value>true</value>
    </property>


select id,name,score from stus
limit (page-1)*page*size,page_size;

```

