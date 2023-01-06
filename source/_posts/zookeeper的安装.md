---
title: zookeeper的安装
date: 2022-12-10 14:20:57
tags:
  - BigData
  - Java
  - HBase
cover: https://freeimg.eu.org/i/2023/01/j1g3xp.jpg
categories: 技术记录
description: 分布式系统的注册服务等管理中心。
---







## 安装流程：

```
准备工作：
	搭建好jdk，mysql，hadoop和免密登录
	关闭防火墙

#上传安装包后
cd ~/software
#解压到/usr/local/
sudo tar -zxvf apache-zookeeper-3.7.1-bin.tar.gz -C /usr/local/
cd /usr/local/
#修改文件名为zookeeper
sudo mv apache-zookeeper-3.7.1-bin/ zookeeper
#修改属组
sudo chown -R huser:huser zookeeper/

#修改权限
sudo chmod -R 777 zookeeper/
cd zookeeper/

#创建目录mydata
sudo mkdir mydata
sudo chmod 777 mydata/

#配置文件
cd conf/
cp zoo_sample.cfg zoo.cfg
vim zoo.cfg
dataDir=/usr/local/zookeeper/mydata
server.1=node1:2888:3888
server.2=node2:2888:3888
server.3=node3:2888:3888

注意：server.x的x是服务器号，与对应服务器dataDir中myid文件内的号码一致。

 提示：
	  1.配置文件中的 tickTime 是心跳时间，意思是：集群必须以两秒为一个时间点，向 leader 报告“我不是死的”，所以这是 心跳包 的时间。
	  2.配置文件中的 syncLimit=5 代表有5台机器可以同时运转。
	  3.配置文件中的 clientPort=2181 是zk的默认端口。

#环境变量配置
sudo vim /etc/profile
export ZOOKEEPER_HOME=/usr/local/zookeeper
export PATH=$PATH:$ZOOKEEPER_HOME/bin

source /etc/profile

在主机执行命令
echo 1 >  /usr/local/zookeeper/mydata/myid
sudo chmod 777 /usr/local/zookeeper/mydata/myid

cd /usr/local
scp -r /usr/local/zookeeper root@node2:$PWD
scp -r /usr/local/zookeeper root@node3:$PWD
scp /etc/profile root@node2:/etc/
scp /etc/profile root@node3:/etc/

#在第二台执行
vim /usr/local/zookeeper/mydata/myid
把1改为2
source /etc/profile

#在第三台执行
vim /usr/local/zookeeper/mydata/myid
把1改为3
source /etc/profile





#在三台都执行
zkServer.sh start
使用jps查看

zkServer.sh status
在这里普及一下服务器状态：
LOOKING：寻找Leader状态。当服务器处于该状态时，它认为当前集群中没有Leader。
FOLLOWING：跟随者状态，表明当前服务器角色Follower。
LEADING：领导者状态，表明当前服务器角色是Leader。
OBSERVING：观察者状态，表明当前服务器是Observer。


zkServer.sh stop



```

