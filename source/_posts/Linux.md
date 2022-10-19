---
title: Linux
date: 2022-09-18 22:33:55
cover: ./img/linux.png
tags: Linux
categories: linux
description: linux系统yyds！
---









## 前言

添加端口白名单：iptables -I INPUT -p tcp --dport 8080 -j ACCEPT

网络连接的三种连接方式：
	①桥接模式，虚拟系统可以和外部系统通讯，但是容易造成IP冲突
	②NAT模式，网络地址转换模式，虚拟系统可以和外部系统通讯，不容易造成IP冲突，但是不是双向的
	③主机模式，独立的系统

虚拟机克隆：方法①直接拷贝安装好的虚拟机文件
	②使用vmware的克隆操作，注意克隆时需要先关闭linux系统	//和之前的用户名密码都一样

#### MobaXterm连接Centos7

​	# 1、查看系统是否监听于tcp协议的22号端口
​	ss -tnl

	# 2、查看ip地址
	ifconfig
	# ifconfig command not found，则安装net-tools
	yum install net-tools
	# ip addr list同样可以查看ip地址
	ip addr list
	
	# 3、确保防火墙处于关闭状态
	iptables -L -n
	systemctl disable firewalld.service
	systemctl stop firewalld.service
	输入ip和用户名连接即可

#### 2.终端设备：terminal

​	物理终端，也称控制台：console
​	虚拟终端：tty
​	图形终端
​	串行终端：ttyS
​	伪终端：pty

#### 3.命令行接口：CLI

例：[root@localhost ~]#
	root：当前用户名称
	localhost：当前主机名
	~：用户当前所在目录（current directory），也称工作目录（working directory）
	#：命令提示符，# 为管理员账号，$ 为普通用户

	# 查看终端设备
	tty
	# 查看接口程序
	echo $SHELL

#### 4.文件系统

例：/dev/pts/0
	最左侧 / 表示根目录
	其余 / 表示为文件分隔符，Linux 文件分隔符为 / ，Windows 文件分隔符为 \
	文件名支持使用除 / 以外的任意字符，最长不能超过 255 个字符

	# 获取文件路径的基名basename
	basename /etc/sysconfig/network-scripts/ifcfg-ens33
	ifcfg-ens33
	
	# 获取文件路径的目录名dirname
	dirname /etc/sysconfig/network-scripts/ifcfg-ens33
	/etc/sysconfig/network-scripts

## Linux基础知识

#### 1.命令格式

​	语法通用格式：# COMMAND OPTIONS ARGUMENTS

	COMMAND
	概述：发起一个命令，请求内核将某个二进制程序运行为一个进程，命令本身就是一个可执行的程序文件，二进制格式的文件（ELF 格式），有可能调用共享库文件。命令分为两种，一种是由 shell 程序自带的命令，成为内置命令，另一种则是独立的可执行文件，文件名即为命令名，称为外部命令。shell 程序是独特的程序，负责解析用户提供的命令，查询通过环境变量，从左到右依次查看。
	# 查看文件信息
	file /bin/ls
	
	# 查看环境变量
	echo $PATH
	
	# 查看命令类型
	type COMMAND
	
	OPTIONS
	概述：用来指定命令的运行特性，选项分为短选项和长选项两种，短选项如-l 、-d，有些命令选项没有- ，多数可合并，-l -d可合并为-ld ，长选项如--help，长选项不能合并，有些选项可以带参数称为选项参数。
	
	ARGUMENTS
	概述：表示命令的作用对象，即命令对什么生效，多个命令参数之间以空白字符分隔，如ls -ld /var /etc。

#### 2.获取命令帮助

​	内部命令
​	help COMMAND

	外部命令
		命令自带简要格式的使用帮助，COMMAND --help
		使用手册获取帮助，man COMMAND，使用手册位置：/usr/share/man
	
		# 使用各章节的命令不同
		man1：用户命令；
		man2：系统调用；
		man3：C库调用；
		man4：设备文件及特殊文件；
		man5：文件格式；（配置文件格式）
		man6：游戏使用帮助；
		man7：杂项；
		man8：管理工具及守护进行；
	
		# 查看COMMAND在哪些手册出现过
		whatis COMMAND
	
		# 手动更新查询数据库
		makewhatis
	
	阅读手册快捷键
		空格：先后翻一屏
		b：向前翻一屏
		Ctrl+d：向后翻半屏
		Ctrl+u：向前翻半屏
		回车：向后翻一行
		k：向前翻一行
		G：跳转至最后一行
		g：跳转到第一行
		nG：跳转至第n行
		/keyword：从前往后找，不区分大小写，n：往后翻
		?keyword：从后往前找，n：往前翻，N：往后翻
		-M /PATH/TO/SOMEDIR：到指定目录下查找命令手册并打开之
		q：退出手册

#### 3.基础命令

​	pwd:显示当前路径

	cd
		# change directory
		# 切换回家目录，bash中，~表示家目录
		cd
		cd ~
	
		# 切换回上一次所在目录
		cd -
	
		# 相关环境变量，$PWD $OLDPWD，.代表当前目录，..代表上一级目录
	
	ls(或ll)
	命令：ls [OPTION]... [FILE]...
		文件详细属性：drwxr-xr-x. 2 root root 204 1月 18 11:20 anaconda
		详解：
			d：表示文件类型，-，d，b，c，l，s，p
			rwxr-xr-x：权限信息
			rwx：文件属主的权限
			r-x：文件数组的权限
			r-x：其他用户（非属主、属组）的权限
			.：表示该文件为隐藏文件
			2：数字表示文件被硬链接的次数
			root：文件的属主
			root：文件的属组
			204 ：表示文件的大小，单位为字节
			1月 18 11:20：文件最近一次被修改的时间
			anaconda：文件名
		[OPTION]
			-a：显示所有文件，包括隐藏文件
			-A:显示除.和..之外的所有文件
			-l,--long：长格式列表，即显示文件的详细属性
			-h,--human-readable：对文件大小单位进行换算显示，换算之后并不精确
			-d：查看目录自身而非其内部的文件列表，通常与l连用
			-r：revrese，逆序显示
			-R：recursive，递归显示
	cat
	# cat concatenate，文本文件查看工具，[OPTION]... [FILE]...
		# -n：显示文本行编号
		# -E：显示行结束符
	tac
	# 与cat反过来，逆序打印文件内容
	
	file
	# 查看文件内容类型
	如：# file /etc/fstab
	
	echo
	# 回显
		-n：不自动进行换行
		-e：让转义符生效
		单引号：强引用，变量引用不执行替换
			[root@localhost ~]# echo '$SHELL'
			$SHELL
		双引号：弱引用，变量引用会被替换
			[root@localhost ~]# echo "$SHELL"
			/bin/bash
		变量引用的正规符号：${name}
			[root@localhost ~]# echo ${SHELL}
			/bin/bash
	
	shutdown
	# 关机或重启命令，shutdown [OPTIONS...] [TIME] [WALL...]
		# OPTIONS: -h：关机，-r：重启，-c：取消操作
		# TIME：now，hh:mm，+m
		# WALL：即为广播发送的消息(加双引号)
	
	date
		# 显示系统时间，date [OPTION]... [+FORMAT]
		# 设置系统时间，date [-u|--utc|--universal] [MMDDhhmm[[CC]YY][.ss]]
		date：显示当前时间
		date +"%Y-%m-%d %H:%M:%S"：时间格式输出
	
	clock，hwclock
		# 显示或设置硬件时钟
		# -s，--hctosys，将系统时间改为硬件时间
		# -w，--systohc，将硬件时间改为系统时间
	
	cal
	# 日历
	# cal [options] [[[day] month] year]
		cal 1 2021：显示2021年1月的日历
	
	alias
	# 若使用原命令可用 \COMMAND
	# 获取所有可用别名的定义
		# 若使用原命令可用 \COMMAND
		# 获取所有可用别名的定义
		[root@localhost ~]# alias
		alias cp='cp -i'
		alias egrep='egrep --color=auto'
		alias fgrep='fgrep --color=auto'
		alias grep='grep --color=auto'
		alias l.='ls -d .* --color=auto'
		alias ll='ls -l --color=auto'
		alias ls='ls --color=auto'
		alias mv='mv -i'
		alias rm='rm -i'
		alias which='alias | /usr/bin/which --tty-only --read-alias --show-dot --show-tilde'
	
		# 设置别名，alias NAME=COMMAND，只对当前 shell 进程有效
		alias cls=clean
	
		# 取消别名，unalias NAME
		unalias cls
	
	which
		# shows the full path of (shell) commands
		[root@localhost ~]# which ls
		alias ls='ls --color=auto'
			/usr/bin/ls
	
		# 使用which原命令
		[root@localhost ~]# \which ls
		/usr/bin/ls
	
	whereis
		# locate the binary, source, and manual page files for a command
		[root@localhost ~]# whereis ls
		ls: /usr/bin/ls /usr/share/man/man1/ls.1.gz
	
		# -b，只查看二进制程序路径
		[root@localhost ~]# whereis -b ls
		ls: /usr/bin/ls
	
		# -m，只查看使用手册文件路径
		[root@localhost ~]# whereis -m ls
		ls: /usr/share/man/man1/ls.1.gz
	
	who
		# show who is logged on
		[root@localhost ~]# who
		root     tty1         2021-01-21 18:05
		root     pts/0        2021-01-23 14:43 (192.168.1.105)
		root     pts/1        2021-01-23 15:43 (192.168.1.105)
	
		# -b，系统此次启动时间
		[root@localhost ~]# who -b
		         系统引导 2021-01-21 18:04
	
		# -r，运行级别
		[root@localhost ~]# who -r
		         运行级别 3 2021-01-21 18:05
	
	w
		# Show who is logged on and what they are doing
		[root@localhost ~]# w
		 16:18:47 up 18:58,  3 users,  load average: 0.05, 0.03, 0.05
		USER     TTY      FROM             LOGIN@   IDLE   JCPU   PCPU WHAT
		root     tty1                      四18    1:35m  0.03s  0.03s -bash
		root     pts/0    192.168.1.105    14:43    7.00s  0.06s  0.04s w
		root     pts/1    192.168.1.105    15:43   23.00s  0.01s  0.00s less -s

#### 4.根目录系统

​	文件层级结构：FHS
​	Filesystem Hierarchy Standard

		/bin ：所有用户可用的基本命令程序文件
		/sbin：供系统管理使用的工具程序
		/boot：引导加载器必须用到的各静态文件
		/dev：存储特殊文件或设备文件
		/etc：系统程序的配置文件，只能为静态
		/home：普通用户家目录，一般为/home/USERNAME
		/root：管理员家目录
		/lib：为系统启动或根文件系统上的应用程序（/bin，/sbin等）提供共享库，以及为内核提供内核模块
			./libc.so.*：动态链接的C库
			./ld*：运行时链接器/加载器
			./modules：用于存储内核模块的目录
		/lib64：64位系统特有的存放64位共享库的路径
		/media：便携式设备挂载点，cdrom, floppy等
		/mnt：其它文件系统的临时挂载点
		/opt：附加应用程序的安装位置（可选路径）
		/srv：当前主机为服务提供的数据
		/tmp：为产生临时文件的程序提供用于存储临时文件的目录，可供所有用户执行写入操作，有特殊权限
		/usr：usr Hierarchy，全局共享的只读数据路径
			./bin、./sbin
			./lib、./lib64
			./include：C程序头文件
			./share：命令手册页和自带文档等架构特有的文件的存储位置
			./local：让系统管理员安装本地应用程序；也通常用于安装第三方程序
			./X11R6：X-Window 程序的安装位置
			./src：程序源码文件的存储位置
		/var：存储发生变化的数据的目录
			./cache：Application cache data
			./lib：Variable state information
			./local：Variable data for /usr/local
			./lock：Lock files
			./log：Log files and directories
			./opt：Variable data for /opt
			./run：Data relevant to running processes
			./spool：Application spool data
			./tmp：Temporary files preserved between system reboots
		/proc：基于内存的虚拟文件系统，用于为内核及进程存储其相关信息，它们多为内核参数，例如net.ipv4.ip_forward， 虚拟为net/ipv4/ip_forward，存储于/proc/sys/，因此其完整路径为/proc/sys/net/ipv4/ip_forward
		/sys：sysfs 虚拟文件系统提供了一种比 proc 更为理想的访问内核数据的途径，其主要作用在于为管理 Linux 设备提供一种统一模型的的接口

#### 6.文件类型

​	-：常规文件 file
​	d：directory，目录文件
​	b：block device，块设备文件，支持以block为单位进行随机访问
​	c：character device，字符设备文件
​		major number：主设备号，用于标识设备类型，进而确定要加载的驱动程序
​		minor number：次设备号，用于标识同一类型中的不同设备
​	symbolic link：符号链接文件
​	pipe：命名管道
​	socket：套接字文件
​	

#### 7.bash

​	命令历史
​	shell 进程会将其会话中保存此前用户提交执行过的命令

	# history
	[root@localhost ~]# history
	    1  ll /dev
	    2  ech $SHELL
	    3  echo $SHELL
	    4  pwd
	    5  history
	
	# $HISTSIZE，shell 进程可保留的命令历史的条数
	[root@localhost ~]# echo $HISTSIZE
	1000
	
	# $HISTFILE，持久保存命令历史的文件
	[root@localhost ~]# echo $HISTFILE
	/root/.bash_history
	
	# -c，删除所有条目从而清空历史列表
	[root@localhost ~]# history -c
	[root@localhost ~]# history
	    1  history
	
	# -d offset，删除指定命令历史
	# -r，从文件读取命令历史到历史列表中
	# -w，把历史列表中的命令追加到历史文件中
	# history n，显示最近 n 条命令
	# !n，再一次执行历史列表中的第 n 条命令
	# !!，再一次执行上一条命令
	# !x，再一次执行命令历史列表中最近一个以 x 开头的命令
	# esc + . ，调用上一条命令的最后一个参数
	# !$，表示上一条命令的最后一个参数
	# $HISTCONTROL，ignoredups：忽略重复性，ignorespace：忽略以空白字符开头的命令，ignoreboth：两者
	[root@localhost ~]# echo $HISTCONTROL
	ignoredups
	
	命令补全
	shell 程序在接收到命令用户执行命令的请求，分析完成之后，最左侧的字符串会被当成命令
	命令查找机制：查找内部命令，根据 PATH 环境变量中设定的命令，自左而右逐个搜索目录下的文件名
	输入给定的打头字符串，如果唯一标识命令按 Tab则自动补全，如果不唯一，则按两次 Tab 键能显示所有打头列表
	
	路径补全
	根据给定的起始路径下，以对应路径下的打头字符串来逐一匹配起始路径下的每个文件，如果唯一标识按 Tab则自动补全，如果不唯一，则按两次 Tab 键能显示所有打头列表
	
	命令行展开
	~，自动展开位用户的家目录，或指定的用户的家目录，{}，可承载一个逗号分隔的路径列表，并能够将其展开位多个路径，如：/tmp/{a,b} 相当于 /tmp/a /tmp/b
	
	命令行状态结果
	bash通过状态返回值来输出此结果，命令执行完成之后，其返回值保存于 bash 的特殊变量 $? 中，命令正确执行时，有的返回有命令返回值，不同命令，结果各不相同，$COMMAND，引用命令的执行结果
	
	引用
	强引用：''，弱引用：""，命令引用：``
	
	快捷键
	Ctrl+a：跳转至命令行行首
	Ctrl+e：跳转至命令行行尾
	Ctrl+u：删除行首至光标所在处之间的所有字符
	Ctrl+k：删除光标所在处至行尾的所有字符
	Ctrl+l：清屏，相当于 clear
	
	文件名通配
	*：匹配任意长度的任意字符
	?：匹配任意单个字符
	[]：匹配指定范围内的任意单个字符
		[a-z]，[A-Z]， [0-9]，[a-z0-9]
		[[:upper:]]：所有大写字母
		[[:lower:]]：所有小写字母
		[[:alpha:]]：所有字母
		[[:digit:]]：所有数字
		[[:alnum:]]：所有的字母和数字
		[[:space:]]：所有空白字符
		[[:punct:]]：所有标点符号
	[^]：匹配指定范围外的任意单个字符
		[^[:upper:]]：匹配非大写字符
		[^0-9]：匹配非数字
		[^[:alnum:]]：匹配非字母和数字
	
	命令hash
		[root@study /]# hash
		命中    命令
		   4    /usr/bin/chmod
		   1    /usr/bin/cat
		   4    /usr/bin/man
		  10    /usr/bin/ls
	
		# hash -d COMMAND 删除
		# hash -r 清空
	
	变量
		将所有变量视作字符型，无需事先声明
		本地变量：作用域仅为当前 shell 进程
			# 变量赋值：name=value
			# 变量引用：${variable}，$variable
			# 撤销变量 unset name
		环境变量：作用域为当前 shell 进程及其子进程
			# 变量赋值
			# export name=value
			# name=value
			# export name
			# declare -x name=value
	
			# 变量引用：${variable}，$variable
			# bash 内嵌了许多环境变量用于定义 bash 的工作环境
			# export、declare -x、printenv、env，查看所有环境变量
		局部变量：作用域仅为某代码片段
		位置参数变量：当执行脚本的 shell 进程传递的参数
		特殊变量：shell 内置的有特殊功能的变量
		只读变量
			# 只读
			readonly name
			declare -r name
	
		多命令执行
			COMMAND1;COMMAND2;COMMAND3;......
	
			# 当命令1失败则不再运行命令2
			COMMAND1 && COMMAND2
	
			# 当命令1成功则不再运行命令2
			COMMAND1 || COMMAND2

#### 8.目录管理命令

​	mkdir
​		# 创建目录，mkdir [OPTION]... DIRECTORY...
​		# -p，自动按需创建父目录
​		# -v，verbose，显示详细过程
​		# -m MODE，创建目录时直接给定权限

		# 创建/tmp/x/y/z，并显示创建详情
		[root@localhost ~]# mkdir -p -v /tmp/x/y/z
		mkdir: 已创建目录 "/tmp/x"
		mkdir: 已创建目录 "/tmp/x/y"
		mkdir: 已创建目录 "/tmp/x/y/z"
	
		# 利用bash命令行展开特性，一条语句创建/tmp/x/y1, /tmp/x/y2, /tmp/x/y1/a, /tmp/x/y1/b
		[root@localhost ~]# mkdir -p -v /tmp/x/{y1/{a,b},y2}
		mkdir: 已创建目录 "/tmp/x/y1"
		mkdir: 已创建目录 "/tmp/x/y1/a"
		mkdir: 已创建目录 "/tmp/x/y1/b"
		mkdir: 已创建目录 "/tmp/x/y2"
	
		# 利用bash命令行展开特性，一条语句创建a_c, a_d, b_c, b_d
		[root@localhost ~]# mkdir -pv {a,b}_{c,d}
		mkdir: 已创建目录 "a_c"
		mkdir: 已创建目录 "a_d"
		mkdir: 已创建目录 "b_c"
		mkdir: 已创建目录 "b_d"
	
	rmdir
		# 只删除空文件目录，不常用，rmdir [OPTION]... DIRECTORY...
		# -p，删除某目录之后，如果其父目录为空，则一并删除
		# -v，显示详细过程
	
		# 一并删除所有空目录 /tmp/x/y/z
		[root@localhost ~]# rmdir -pv /tmp/x/y/z
		rmdir: 正在删除目录 "/tmp/x/y/z"
		rmdir: 正在删除目录 "/tmp/x/y"
		rmdir: 正在删除目录 "/tmp/x"
	
	tree
		# 安装 tree命令，yum install tree
		# 显示目录层级结构，tree [options] [directory]
		# -L，指定显示的层级

#### 9.文本查看命令

​	cat
​		# 文件内容查看，-n显示行号，-E显示行尾结束符

	tac
		# 与 cat 相反，逆行序打印文本内容
	
	more
		# 分屏查看，more FILE
	
	less
		# 与 more 相反，less FILE
	
	head
		# 查看文件的前 n 行，默认 10 行，head [options] FILE
		[root@localhost ~]# head -n 5 /etc/sysconfig/network-scripts/ifcfg-ens33 
		TYPE="Ethernet"
		PROXY_METHOD="none"
		BROWSER_ONLY="no"
		BOOTPROTO="dhcp"
		DEFROUTE="yes"
	
	tail
		# 查看文件的后 n 行，tail [options] FILE
		[root@localhost ~]# tail -n 5 /etc/sysconfig/network-scripts/ifcfg-ens33 
		IPV6_ADDR_GEN_MODE="stable-privacy"
		NAME="ens33"
		UUID="6ae13078-476b-45b6-a9d9-1fac0480b8a5"
		DEVICE="ens33"
		ONBOOT="yes"
	
		# -f，查看文件尾部内容结束后不退出，跟随显示新增的行，监控log
	
	stat
		# display file or file system status，stat FILE...
		[root@localhost ~]# stat /tmp/yum.log 
		  文件："/tmp/yum.log"
		  大小：0         	块：0          IO 块：4096   普通空文件
		设备：fd00h/64768d	Inode：67160137    硬链接：1
		权限：(0600/-rw-------)  Uid：(    0/    root)   Gid：(    0/    root)
		环境：system_u:object_r:initrc_tmp_t:s0
		最近访问：2021-01-18 11:17:06.195300428 +0800
		最近更改：2021-01-18 11:17:06.195300428 +0800
		最近改动：2021-01-18 11:17:06.195300428 +0800
		创建时间：-
	
		# 最近访问：access time
		# 最近更改：modify time
		# 最近改动：change time
	
	touch
		# change file timestamps,touch [OPTION]... FILE...
		# -c，指定的文件路径不存在时不予创建
		# -a：仅修改access time
		# -m：仅修改吗，modify time
		# -t STAMP：指定时间戳，[[CC]YY]MMDDhhmm[.ss]

#### 10.文件管理命令

​	cp
​		# copy files and directories，
​		# 单源复制：cp [OPTION]... [-T] SOURCE DEST
​		# 多源复制：cp [OPTION]... SOURCE... DIRECTORY，cp [OPTION]... -t DIRECTORY SOURCE...
​		# -i：交互式复制，即覆盖之前提醒用户确认
​		# -f：强制覆盖目标文件
​		# -r：递归目录
​		# -d：复制符号链接本身，而非其指向的源文件
​		# -a：等价于 -dR --preserve=all，用于归档
​		# --perserve=...：指定保留对应属性，默认：mode,ownership,timestamps，额外：context, links, xattr, all

	rm
		# remove files or directories
		# rm [OPTION]... FILE...
		# -i：交互式复制，即覆盖之前提醒用户确认
		# -f：强制覆盖目标文件
		# -r：递归目录
		# 所有不用文件建议不用删除，而是移动到固定目录

#### 11.IO 重定向与管道

​	输出重定向
​		# >，覆盖输出
​		[root@localhost /]# head -n 5 /etc/passwd
​		root:x:0:0:root:/root:/bin/bash
​		bin:x:1:1:bin:/bin:/sbin/nologin
​		daemon:x:2:2:daemon:/sbin:/sbin/nologin
​		adm:x:3:4:adm:/var/adm:/sbin/nologin
​		lp:x:4:7:lp:/var/spool/lpd:/sbin/nologin

		[root@localhost /]# head -n 5 /etc/passwd > /tmp/head_5_etc_passwd.txt
	
		[root@localhost /]# cat /tmp/head_5_etc_passwd.txt
		root:x:0:0:root:/root:/bin/bash
		bin:x:1:1:bin:/bin:/sbin/nologin
		daemon:x:2:2:daemon:/sbin:/sbin/nologin
		adm:x:3:4:adm:/var/adm:/sbin/nologin
		lp:x:4:7:lp:/var/spool/lpd:/sbin/nologin
	
		# >>，追加输出
		[root@localhost /]# tail -n 5 /etc/passwd
		dbus:x:81:81:System message bus:/:/sbin/nologin
		polkitd:x:999:998:User for polkitd:/:/sbin/nologin
		sshd:x:74:74:Privilege-separated SSH:/var/empty/sshd:/sbin/nologin
		postfix:x:89:89::/var/spool/postfix:/sbin/nologin
		chrony:x:998:996::/var/lib/chrony:/sbin/nologin
	
		[root@localhost /]# tail -n 5 /etc/passwd >> /tmp/head_5_etc_passwd.txt
	
		[root@localhost /]# cat /tmp/head_5_etc_passwd.txt
		root:x:0:0:root:/root:/bin/bash
		bin:x:1:1:bin:/bin:/sbin/nologin
		daemon:x:2:2:daemon:/sbin:/sbin/nologin
		adm:x:3:4:adm:/var/adm:/sbin/nologin
		lp:x:4:7:lp:/var/spool/lpd:/sbin/nologin
		dbus:x:81:81:System message bus:/:/sbin/nologin
		polkitd:x:999:998:User for polkitd:/:/sbin/nologin
		sshd:x:74:74:Privilege-separated SSH:/var/empty/sshd:/sbin/nologin
		postfix:x:89:89::/var/spool/postfix:/sbin/nologin
		chrony:x:998:996::/var/lib/chrony:/sbin/nologin
	
		# set -C，禁止覆盖输出重定向至已存在的文件
		# set +C，关闭上述功能
		# 错误输出重定向：2>，2>>
		# 合并正常输出流和错误输出流：(1) &>，&>>（2） COMMAND > /path/to/somefile 2>&1
	
	输入重定向
		# <，通过输入设备键入输出
		# tr，tr [OPTION]... SET1 [SET2]
		# 把输入的数据当中的字符，凡是在SET1定义范围内出现的，通通对位转换为SET2出现的字符，不修改原文件
		# tr SET1 SET2 < /PATH/FROM/SOMEFILE
		# tr -d SET1 < /PATH/FROM/SOMEFILE，指定移除指定SET1定义范围内出现得字符
		[root@localhost /]# tr [a-z] [A-Z]
		where are you?
		WHERE ARE YOU?
		How are you?    
		HOW ARE YOU?
		^C
	
		# <<，指定分界符，一般为EOF
		[root@localhost /]# cat > /tmp/cat.out << EOF
		> where are you?
		> how are you
		> EOF
		[root@localhost /]# cat ^C
		[root@localhost /]# cat /tmp/cat.out 
		where are you?
		how are you
	
	管道
		# |，COMMAND1 | COMMAND2 | COMMAND3 | ...
		# 连接程序，实现将前一个命令的输出直接定向后一个程序当作输入数据流
		[root@localhost /]# cat /etc/issue | tr 'a-z' 'A-Z'
		\S
		KERNEL \R ON AN \M
	
		# tee，COMMAND | tee /PATH/TO/SOMEFILE
		[root@localhost /]# cat /etc/issue | tee /tmp/issue.tee | tr 'a-z' 'A-Z'
		\S
		KERNEL \R ON AN \M
	
		[root@localhost /]# cat /tmp/issue.tee
		\S
		Kernel \r on an \m

#### 12.用户与组

​	用户类别
​		管理员：0
​		普通用户：1-65635
​			系统用户：1-499(CentOS6), 1-999(CentOS7)
​			登录用户：500-60000(CentOS6), 1000-60000(CentOS7)
​	名称解析：Username <–> UID 名称解析库：/etc/passwd

	组类别
		管理员组：0
		普通用户组：1-65635
			系统组：1-499(CentOS6), 1-999(CentOS7)
			登录组：500-60000(CentOS6), 1000-60000(CentOS7)
		用户的基本组
		用户的附加组
		私有组：组名同用户名，且只包含一个用户
		公共组：组内包含了多个用户
	名称解析：groupname <–> gid 名称解析库：/etc/group
	
	认证信息
	通过比对事先存储的，与登录时提供的信息是否一致
	密码使用策略：
		①使用随机密码
		②最短长度不要低于8位
		③应该使用大写字母、小写字母、数字和标点符号四类字符中至少三类
		④定期更换
	加密算法：
		①对称加密：加密和解密使用同一个密码
		②非对称加密：加密和解密使用的一对儿密钥
		③单向加密：只能加密，不能解密；提取数据特征码
		算法：
			md5: message digest, 128bits
			sha：secure hash algorithm, 160bits
			sha224
			sha256
			sha384
			sha512
		etc/passwd：用户的信息库
	
		格式为 ：name:password:UID:GID:GECOS:directory:shell
		name: 用户名
		password：可以是加密的密码，也可是占位符x；
		UID：用户ID；
		GID：用户所属的主组的ID号；
		GECOS：注释信息
		directory：用户的家目录；
		shell：用户的默认shell，登录时默认shell程序；
	
		/etc/shadow：用户密码
		用户名:加密的密码:最近一次修改密码的时间:最短使用期限:最长使用期限:警告期段:过期期限:保留字段
	
		/etc/group：组的信息库
		格式为：group_name:password:GID:user_list
		user_list：该组的用户成员；以此组为附加组的用户的用户列表
	
	groupadd
		# 添加组，groupadd [选项] group_name
		# -g GID：指定GID；默认是上一个组的GID+1
		# -r: 创建系统组
	
	groupmod
		# 修改组属性，groupmod [选项] GROUP
		# -g GID：修改GID；
		# -n new_name：修改组名；
	
	groupdel
		# 删除组，groupdel [选项] GROUP
	
	useradd
		# 创建用户,useradd [选项] 登录名
		# 创建用户时的诸多默认设定配置文件为/etc/login.defs	
		# -u, --uid UID：指定UID；
		# -g, --gid GROUP：指定基本组ID，此组得事先存在；
		# -G, --groups GROUP1[,GROUP2,...[,GROUPN]]]：指明用户所属的附加组，多个组之间用逗号分隔；
		# -c, --comment COMMENT：指明注释信息；
		# -d, --home HOME_DIR：以指定的路径为用户的家目录；通过复制/etc/skel此目录并重命名实现；指定的家目录路径如果事先存在，则不会为用户复制环境配置文件；
		# -s, --shell SHELL：指定用户的默认shell，可用的所有shell列表存储在/etc/shells文件中；
		# -r, --system：创建系统用户；
		# useradd -D：显示创建用户的默认配置；
		# useradd -D 选项: 修改默认选项的值，修改的结果保存于/etc/default/useradd文件中；
	
	usermod
		# 修改用户属性，usermod [选项] 登录名
		# -u, --uid UID：修改用户的ID为此处指定的新UID；
		# -g, --gid GROUP：修改用户所属的基本组；
		# -G, --groups GROUP1[,GROUP2,...[,GROUPN]]]：修改用户所属的附加组；原来的附加组会被覆盖；
		# -a, --append：与-G一同使用，用于为用户追加新的附加组；
		# -c, --comment COMMENT：修改注释信息；
		# -d, --home HOME_DIR：修改用户的家目录；用户原有的文件不会被转移至新位置；
		# -m, --move-home：只能与-d选项一同使用，用于将原来的家目录移动为新的家目录；
		# -l, --login NEW_LOGIN：修改用户名；
		# -s, --shell SHELL：修改用户的默认shell；
		# -L, --lock：锁定用户密码；即在用户原来的密码字符串之前添加一个"!"；
		# -U, --unlock：解锁用户的密码；
	
	userdel
		# 删除用户,userdel [选项] 登录
		# -r：删除用户时一并删除其家目录；
	
	passwd
		# passwd：修改用户自己的密码；
		# passwd USERNAME：修改指定用户的密码，但仅root有此权限；
		# -l, -u：锁定和解锁用户；
		# -d：清除用户密码串；
		# -e DATE: 过期期限，日期；
		# -i DAYS：非活动期限；
		# -n DAYS：密码的最短使用期限；
		# -x DAYS：密码的最长使用期限；
		# -w DAYS：警告期限；
		# --stdin：echo "PASSWORD" | passwd --stdin USERNAME
	
	gpasswd
		# 组密码文件：/etc/gshadow，gpasswd [选项] group
		# -a USERNAME：向组中添加用户
		# -d USERNAME：从组中移除用户
	
	newgrp
		# 临时切换指定的组为基本组，newgrp [-] [group]
		# -: 会模拟用户重新登录以实现重新初始化其工作环境；
	
	chage
		# 更改用户密码过期信息，chage [选项] 登录名
		# -d，-E，-W，-m，-M
	
	id
		# 显示用户的真和有效ID，id [OPTION]... [USER]
		# -u: 仅显示有效的UID；
		# -g: 仅显示用户的基本组ID; 
		# -G：仅显示用户所属的所有组的ID；
		# -n: 显示名字而非ID；
	
	su
		# switch user
		# 登录式切换：会通过读取目标用户的配置文件来重新初始化
		su - USERNAME
		su -l USERNAME
		# 非登录式切换：不会读取目标用户的配置文件进行初始化
		su USERNAME
		# 注意：管理员可无密码切换至其它任何用户；
		#  -c 'COMMAND'：仅以指定用户的身份运行此处指定的命令；
		su - USERNAME -c 'COMMAND'

#### 13.权限管理

​	mode：rwxrwxrwx
​		左三位：定义 user（owner）的权限
​		中三位：定义 group 的权限
​		右三位：定义 other 的权限
​	r：readable，读权限
​	w：writable，写权限
​	x：excutable，执行权限
​	ownership：user,group,other

	权限组合机制
		---、000、0
		--x、001、1
		-w-、010、2
		-wx、011、3
		r--、100、4
		r-x、101、5
		rw-、110、6
		rwx、111、7
	
	chmod
		# 更改权限，u:属主、g:属组、o:其他、a:所有
		# chmod [options] mode file...
		# 用户只能修改属主为自己的文件
	
		# 指定所有权限设置，u=,g=,o=,a=
		[root@study /]# ll txt
		-rw-r--r--. 1 root root 541 4月   9 21:34 txt
		[root@study /]# chmod g=rw- txt
		[root@study /]# ll txt
		-rw-rw-r--. 1 root root 541 4月   9 21:34 txt
	
		# 指定单个权限设置，u-r,o+x,g-w,+r,+x
		[root@study /]# chmod g-w txt
		[root@study /]# ll txt
		-rw-r--r--. 1 root root 541 4月   9 21:34 txt
		[root@study /]# chmod +x txt
		[root@study /]# ll txt
		-rwxr-xr-x. 1 root root 541 4月   9 21:34 txt
	
		# 进制表示权限修改
		[root@study /]# chmod 660 txt
		[root@study /]# ll txt
		-rw-rw----. 1 root root 541 4月   9 21:34 txt
	
		# -R，--recursive，递归修改
	
	chown
		# 修改属主
	
	chgrp
		# 修改数组
	
	umask
		# 文件权限反向掩码，遮罩码
		# 文件默认不能拥有执行权限，如果减得的结果中有执行权限则需要加1
		[root@study /]# umask
		0022
		# umask MASK 设置 umask
	
	install
		# 复制文件并设置权限
		install [options] [-s] [--strip] source dest
		install [options] [-s] [--strip] source... directory
		install [options] [-d,--directory] directory...
	
		# -d, --directory
		# -m mode, --mode=mode
		# -o owner, --owner=owner
		# -g group, --group=group
	
	mktemp
		# 创建临时文件或临时目录
		[root@study /]# mytemp=$(mktemp /tmp/XXXXXXX)
		[root@study /]# echo $mytemp
		/tmp/ptfJw0h
	
		# -d 创建临时目录

#### 14.Shell编程

​	编写脚本
​	第一行指定解释器路径，用于指明解释执行当前脚本的解释器程序文件，常用的有：#!/bin/bash、#!/usr/bin/python、#!/usr/bin/perl，脚本文件为 .sh

	解释器会自动忽略空白行和加 # 的注释行
	
	运行脚本
	第一种：chmod +x file.sh、./file.sh
	第二种：bash file.sh
	
	bash的配置文件
	
	profile
		为交互式登录的 shell 进程提供配置
		仅管理员可修改
		全局：/etc/profile, /etc/profile.d/*.sh
		用户：~/.bash_profile
	
		功能:
			用于定义环境变量
			运行命令或脚本
	bashrc
	为非交互式登录的 shell 进程提供配置
	全局：/etc/bashrc
	用户：~/.bashrc
	
	功能:
		定义本地变量
		定义命令别名
	
	交互式登录
		直接通过某终端输入账号密码打开的 shell 进程
		使用 su 命令: su - USERNAME,或者使用 su -l USERNAME执行的登录切换
	
	非交互式登录
		su USERNAME 执行的登录切换
		图形界面下打开的终端
		运行脚本

#### 15.文本处理

​	grep
​		# grep, egrep, fgrep - 打印匹配给定模式的行
​		grep [options] PATTERN [FILE...]
​		grep [options] [-e PATTERN | -f FILE] [FILE...]

		[reajason@study ~]$ grep "UUID" /etc/fstab
		UUID=4da0c9b1-d833-434b-a700-6abe448403ec /boot
	
		# --color=auto，匹配到的文本高亮显示
		# -i, --ignore-case 忽略模式 PATTERN 和输入文件中的大小写的分别
		# -o, --only-matching 只显示匹配的行中与 PATTERN 相匹配的部分
		# -v, --invert-match 变匹配的意义，只选择不匹配的行
		# -q, --quiet, --silent 不向标准输出写任何东西，只返回状态吗，echo $?
		# -A NUM, --after-context=NUM 打印出紧随匹配的行之后的下文 NUM 行
		# -B NUM, --before-context=NUM 打印出匹配的行之前的上文  NUM 行
		# -C NUM, --context=NUM 打印出匹配的行的上下文前后各 NUM 行
	
		[reajason@study ~]$ grep "root" /etc/passwd
		root:x:0:0:root:/root:/bin/bash
		operator:x:11:0:operator:/root:/sbin/nologin
		[reajason@study ~]$ grep -A 1 "root" /etc/passwd
		root:x:0:0:root:/root:/bin/bash
		bin:x:1:1:bin:/bin:/sbin/nologin
		--
		operator:x:11:0:operator:/root:/sbin/nologin
		games:x:12:100:games:/usr/games:/sbin/nologin
		[reajason@study ~]$ grep -B 1 "root" /etc/passwd
		root:x:0:0:root:/root:/bin/bash
		--
		mail:x:8:12:mail:/var/spool/mail:/sbin/nologin
		operator:x:11:0:operator:/root:/sbin/nologin
		[reajason@study ~]$ grep -C 1 "root" /etc/passwd
		root:x:0:0:root:/root:/bin/bash
		bin:x:1:1:bin:/bin:/sbin/nologin
		--
		mail:x:8:12:mail:/var/spool/mail:/sbin/nologin
		operator:x:11:0:operator:/root:/sbin/nologin
		games:x:12:100:games:/usr/games:/sbin/nologin
	
	wc
		# 输出文件中的行数、单词数、字节数
		[reajason@study ~]$ wc /etc/passwd
		  43   87 2271 /etc/passwd
	
		# -l: lines
		# -w：words
		# -c: bytes
	
	cut
		# 在文件的每一行中提取片断
		# -d CHAR：以指定的字符为分隔符
		# -f FIELDS：挑选出的字段
	
	sort
		# 对文本文件的行排序
		# -n：基于数值大小而非字符进行排序；
		# -t CHAR：指定分隔符；
		# -k #：用于排序比较的字段；
		# -r：逆序排序；
		# -f：忽略字符大小写
		# -u：重复的行只保留一份；


	uniq
		# 删除排序文件中的重复行
		# -c：显示每行的重复次数；
		# -d：仅显示重复过的的行；


	diff
		# 文件比较
		# diff  /PATH/TO/OLDFILE  /PATH/TO/NEWFILE > /PATH/TO/PATCH_FILE
	
		# -u：使用unfied机制，即显示要修改的行的上下文，默认为3行；
	
	patch
		# 向文件打补丁
		# patch [OPTIONS] -i /PATH/TO/PATCH_FILE /PATH/TO/OLDFILE
		# patch /PATH/TO/OLDFILE < /PATH/TO/PATCH_FILE

#### 16.Vim编辑器

​	基本模式
​		①编辑模式
​		②输入模式
​		③末行模式

	打开文件
	
	模式转换
	编辑模式 -> 输入模式
		i：insert，在光标处输入
		a：append，在光标后方一个字符输入
		o：在光标所在处下方新的一行输入
		I：在光标所在处的行首输入
		A：在光标所在处的行尾输入
		O：在光标所在处上方新的一行输入
	
	输入模式 -> 编辑模式
	Esc
	
	编辑模式 -> 末行模式
	:
	
	末行模式 -> 编辑模式
	Esc
	
	打开文件
	vim file、vim +n file、vim +/reg file
	
	关闭文件
		ZZ   保存并退出
		:q   退出
		:q!  强制退出，不保存
		:wq! 保存并退出
		:x   保存并退出
		:w /PATH/FILE  另存为
	
	光标跳转
	字符间跳转
		h: ←
		j: ↓
		k: ↑
		l: →
	
	单词间跳转
		w: 下一个单词的词首
	e: 当前或后一个单词词尾
	b: 当前或前一个单词词首
	
	行首行尾跳转
		^: 跳转到行首第一个非空白字符
		0: 跳转至行首
		$: 跳转至行尾
	
	行间跳转	
		nG: 跳转到第 n 行
		gg: 跳转第一行
		G:  跳转最后一行
	
	句间跳转
		()
	
	段间跳转
		{}
	
	翻屏
		Ctrl+f: 向下翻一屏
		Ctrl+b: 向上翻一屏
		Ctrl+d: 向下翻半屏
		Ctrl+u: 向上翻半屏
		Enter: 按行向后翻
	
	编辑命令
	
	字符编辑
		x:  删除光标所在处的字符
		#x: 删除光标处起始的#个字符
		xp: 交换光标所在处的字符与其后面的字符的位置
	
	替换命令
		r: 替换光标所在处的字符
	
	删除命令
		d$: 删除光标所在处到行尾
		d^: 删除光标所在处到行首
		dw: 
		de: 
		db: 
		dd: 删除光标所在一整行
		#dd: 删除光标所在处的行起始的共#行
	
	粘贴命令
		p: 整行粘贴则在下方另起一行，否则直接粘贴在其后
		P: 整行粘贴则在上方另起一行，否则直接粘贴在其前
	
	复制命令
		y: 复制，类似于 d 命令
		yy: 复制一整行
	
	改变命令
		c: 删除并进入输入模式
	
	可视化模式
		v: 按字符选定
		V: 按行选定
	
	撤销操作
		u: 撤销
		Ctrl+r: 取消撤销
	
	末行模式
		内建命令行接口
	
	地址定界
		#：第 # 行
		#,#：第 # 到 # 行
		#,+#：第 # 行 偏移 # 行
		%：全文
		/reg/：第一次被匹配的文本 
	
	查找
		/reg：从光标所在处往后查找所有匹配字符
		?reg：从光标所在处往前查找所有匹配字符
		n：下一个
		N：上一个
	
	查找并替换
		s/要查找的内容/替换的内容/修饰符
	
		修饰符：i：忽略大小写
			   g：全局替换
	
	多文件模式
		:next 下一个文件
		:prev 前一个文件
		:last 最后一个
	
		:wqall 保存所有文件并推出
		:wall 保存所有文件
		:qall 退出所有文件
	
	多窗口机制
		-o：水平分割窗口
		-O：垂直分割窗口
		Ctrl+w,arrow：切换
		Ctrl+w,s：水平分割
		Ctrl+w,v：垂直分割
	
	定制Vim
		set number（set nu）：显示行号
		set nomber（set nonu）：取消显示行号
		set ai：自动缩进
		set noai：取消自动缩进
		set hlsearch：高亮搜索
		set nohlsearch：取消高亮搜索
		syntax on：语法高亮
		syntax off：取消语法高亮
		set lc：忽略大小写
		set nocl：取消忽略大小写 

#### 17.文件查找

​	locate
​	依赖于事先构建好的索引库
​		系统自动实现（周期性任务）
​		手动更新数据库（updatedb）
​	工作特性
​		查找速度快
​		模糊查找
​		非实时查找
​			-b：只匹配 basename
​			-c：统计出多少符合条件的数量
​			-r：使用正则表达式

	find
	工作特性
		查找速度慢
		精确查找
		实时查找
		find [OPTION] [查找起始路径][查找条件][处理动作]
	
		-name pattern：查找文件名（精确查找） pattern 为 gloB 通配符
		-iname pattern：不区分大小写
		-user USERNAEM：查找属主
		-group GROUPNAME：查找属组
		-uid UID：查找属主 uid
		-gid GID：查找属组 gid
		-nouser：查找没有属主
		-nogroup：查找没有属组
		-type TYPE：查找文件类型
			f:普通文件
			d:目录文件
			l:符号链接文件
			b:块设备文件
			c:字符设备文件
			p:管道文件
			s:套接字文件
		-size [+|-]#
			#为单位，k,M,G
		-atime/-amin [+|-]#
			根据访问时间查找
		-mtime/-mmin [+|-]#
			根据修改时间查找
		-ctime/-cmin [+|-]#
			根据改变时间查找
	
		-prem mode
	
		处理动作：
	
		默认动作为 -print
		-ls：输出文件详细信息
		-delete：删除查找的文件
		-fls /PATH：ls的输出信息保存指定文件
		-ok COMMAND {}\; ：对查找的每个文件执行命令

#### 18.特殊权限

​	SUID
​	默认情况下，用户发起的进程，进程的属主是其发起者，因此其以发起者的身份在运行。如果此程序拥有 SUID 权限（u=rws），则程序运行为进程时，进程的属主不是发起者，而是程序文件自己的属主。
​		chmod u+s FILE

	SGID
	当目录属组有写权限时，且有 SGID 权限时（g=rws），那么所有属于此目录的属组，且以属组身份在此目录中新建文件或目录时，新文件的属组不是用户的基本组，而是此目录的属组。
		chmod u+s FILE
	
	STIKY
	对于属组或全局可写得到目录，组内的所有用户或系统上的所有系统的所有用户对此目录中都能创建新文件或删除所有的已有文件，如果此类目录设置 Stiky 权限（o=rwt），则每个用户能创建新文件，且只能删除自己的文件。
		chmod o+t FILE
	
	facl
	额外赋权机制，另一层让普通用户能控制赋权给另外的用户或租的赋权机制
		# 获取文件的 facl 权限
		getfacl FLIE
	
		# 设置文件的 facl 权限
		setfacl -m u:USERNAME:MODE FILE
		setfacl -m g:GROUPNAME:MODE FILE
	
		# 撤销赋权
		setfacl -x u:USERNAME FILE
		setfacl -x g:GROUPNAME FILE
