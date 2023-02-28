---
title: nginx知识点
date: 2022-12-02 16:44:19
tags: 
  - Java
  - Nginx
cover: https://wltc2-1258834326.cos.ap-guangzhou.myqcloud.com/2023/01/28/63d4ba60b7736.jpg
categories: 技术记录
description: 对反向代理挺好奇的，来学习学习nginx的强大。
---







踩坑：
	* 代理名不能使用下划线 会报400
	* 安装完nginx后使用 -t 查看配置文件目录 不要改错了配置文件
	* 配置完成后响应速度变慢  不要使用localhost 最好使用ip 具体原因可以百度

## Nginx

#### 	概述：

​		Nginx是一款轻量级的Web服务器/反向代理服务器及电子邮件(IMAP/POP3)代理服务器。其特点是占有内存少，并发能力强，事实上nginx的并发能力在同类型的网页服务器中表现较好，中国大陆使用nginx的网站有:百度、京东、新浪、网易、腾讯、淘宝等。官网: https://nginx.org/

#### 	作用：

​		①Http代理或者反向代理：他是代理服务器的
​		②正向代理：他是代理客户端的，客户端通过他去请求外网，比如VPN，属于正向代理
​		③负载均衡
​		④动静分离：在我们的软件开发中，有些请求是需要后台处理的，有些请求是不需要经过后台处理的（如：css、html、jpg、js等等文件），这些不需要经过后台处理的文件称为静态文件。让动态网站里的动态网页根据一定规则把不变的资源和经常变的资源区分开来，动静资源做好了拆分以后，我们就可以根据静态资源的特点将其做缓存操作。提高资源响应的速度。

	安装过程:
		1.安装依赖包yum -y install gcc pcre-devel zlib-devel openssl openssl-devel zlib zlib-devel
		# yum install gcc-c++
		2、下载Nginx安装包wget https://nginx.org/download/nginx-1.16.1.tar.gz
		3、解压tar -zxvf nginx-1.16.1.tar.gz
			mkdir -p /usr/local/nginx
		4、cd nginx-1.16.1
		5、./configure --prefix=/usr/local/nginx
		6、make && make install
	
	目录结构
		重点目录/文件:
		●conf/nginx.conf：nginx配置文件
		●html：存放静态文件(html. CsS、 Js等)
		●logs：日志目录，存放日志文件
		●sbin/nginx：二进制文件，用于启动、停止Nginx服务
	
	nginx命令
		查看版本：./nginx -v(进入到sbin目录,下面都是)
		检查配置文件是否有错：./nginx -t
		启动Nginx服务使用如下命令：./nginx
		停止Nginx服务使用如下命令：./nginx -s stop
		启动完成后可以查看Nginx进程：ps -ef|grep nginx
		重新加载配置文件：./nginx -s reload
	注意：在浏览器访问http://192.168.217.128:80，即可查看是否启动成功
	
	环境变量配置
		①vim /etc/profile
		②进去以后直接在path后面追加/usr/local/nginx/sbin:
			#这里加:是因为后面还有java等的路径
		③source /etc/profile
	
	配置文件的结构
	Nginx配置文件(conf/nginx.conf)整体分为三部分:
		●全局块：和Nginx运行相关的全局配置
		●events块：和网络连接相关的配置
		●http块：代理、缓存、日志记录、虚拟主机配置
			■http全局块
			■Server块
				◆Server全局块
				◆location块
	注意: http块中可以配置多个Server块，每个Server块中可以配置多个location块。


​	
​	nginx具体应用
​		部署静态资源
​		概述：Nginx可以作为静态web服务器来部署静态资源。静态资源指在服务端真实存在并且能够直接展示的一些文件，比如常见的html页面、css文件、js文件、图片、视频等资源。
​	
​		优势：相对于Tomcat, Nginx处理静 态资源的能力更加高效，所以在生产环境下，一般都会将静态资源部署到Nginx中。
​		
​		将静态资源部署到Nginx非常简单，只需要将文件复制到Nginx安装目录下的html目录中即可。
​		server {
​			listen 80;   #监听端口
​			server.name localhost;  服务器名称(一般是域名)
​			location/ {			#匹配客户端请求url
​				root html;   #指定静态资源根目录
​				index index.html;   #指定默认首页
​				}
​			}
​	
​	正向代理概念：
​		●正向代理
​		是一个位于客户端和原始服务器(origin server)之间的服务器，为了从原始服务器取得内容，客户端向代理发送一个请求并指定目标(原始服务器)，然后代理向原始服务器转交请求井将获得的内容返回给客户端。
​		正向代理的典型用途是为在防火墙内的局域网客户端提供访问Internet的途径。
​		正向代理一般是在客户端设置代理服务器，通过代理服务器转发请求，最终访问到目标服务器。
​	
​	反向代理概念：
​		●反向代理
​		反向代理服务器位于用户与目标服务器之间，但是对于用户而言，反向代理服务器就相当于目标服务器，即用户直接访问反向代理服务器就可以获得目标服务器的资源，反向代理服务器负责将请求转发给目标服务器。
​		用户不需要知道目标服务器的地址，也无须在用户端作任何设定。
​	如：客户端---->反向代理服务器(192.168.217.128)---->web服务器(192.168.217.129)
​	
	●配置反向代理
		这个机器ip为192.168.217.128
		server {
			listen 82;
			server_name localhost;
			location / {
				proxy_pass http://192.168.217.129:8080; #反向代理配置，将请求转发到指定服务
			}
		}
	这个意思就是和上面举的例子一样，就是把对这台服务器82端口的请求，代理到http://192.168.217.129:8080中
	
	负载均衡(在反向代理基础上，本质就是反向代理)
		背景：早期的网站流量和业务功能都比较简单，单台服务器就可以满足基本需求,但是随着互联网的发展，业务流量越来越大井且业务逻辑也越来越复杂，单台服务器的性能及单点故障问题就凸显出来了，因此需要多台服务器组成应用集群，进行性能的水平扩展以及避免单点故障出现。
	
		●应用集群:将同一应用部署到多台机器上，组成应用集群，接收负载均衡器分发的请求，进行业务处理并返回响应数据
		●负载均衡器:将用户请求根据对应的负载均衡算法分发到应用集群中的一台服务器进行处理
	
	配置负载均衡:
		upstream targetserver{     #upstream指令可以定义一组服务器
		server 192.168.217.129:8080;
		# 可以在后面添加weight
		# server 192.168.217.129:8080 weight=10;
		server 192.168.217.130:8080; 
		}
		server {
			listen  8080; 
			server_name localhost;
			location / {
				proxy_pass http://targetserver;
			}
		}
	
	负载均衡策略：
		轮询：就是代理端依次去访问每一个服务器(默认)
		权重轮询：就是给服务器加上权重，代理端会根据权重分配访问量
		ip_hash：他对客户端请求的ip进行hash操作，然后根据hash结果将同一个客户端ip的请求分发给同一台服务器进行处理，可以解决session不共享的问题。
		least_conn:一句最少连接方式
		url_hash：一句url分配方式
		fair：依据响应时间方式
	注意：在这里iphash可以解决session不共享问题，但是不建议使用他，建议使用redis





#### 常用命令：

​	cd /usr/local/nginx/sbin/
​	./nginx  启动
​	./nginx -s stop  停止
​	./nginx -s quit  安全退出
​	./nginx -s reload  重新加载配置文件
​	ps aux|grep nginx  查看nginx进程
注意：如果连接不上，检查阿里云安全组是否开放端口，或者服务器防火墙是否开放端口
相关命令：

```
开启

​	service firewalld start

重启

​	service firewalld restart

关闭

​	service firewalld stop

查看防火墙规则

​	firewall-cmd --list-all

查询端口是否开放

​	firewall-cmd --query-port=8080/tcp

开放80端口

​	firewall-cmd --permanent --add-port=80/tcp

移除端口

​	firewall-cmd --permanent --remove-port=8080/tcp
​	#重启防火墙(修改配置后要重启防火墙)
​	firewall-cmd --reload

参数解释

​	1、firwall-cmd：是Linux提供的操作firewall的一个工具；
​	2、--permanent：表示设置为持久；
​	3、--add-port：标识添加的端口；
```

