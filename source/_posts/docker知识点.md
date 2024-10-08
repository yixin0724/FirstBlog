---
title: docker知识点
date: 2022-12-02 16:44:27
tags: 
  - Docker
  - Linux
cover: https://pic.imgdb.cn/item/66ebef22f21886ccc0d0c9c3.jpg
categories: 技术记录
description: 这就是一键部署，容器化技术吗，方便这词确实有点形容不了docker啦。
---





# 前言

​	概述：Docker 是一个开源的应用容器引擎，它允许开发者将应用及其依赖打包到一个可移植的容器中，然后发布到任何流行的 Linux 机器上，也可以实现虚拟化。Docker 的核心理念是“构建一次，随处运行”，这意味着开发者可以将应用和所有依赖项打包到一个容器中，这个容器可以在任何支持 Docker 的环境中运行，无论是在本地开发机器、测试服务器还是生产服务器上。

​	简单来说：一个快速构建，运行，管理应用的工具。



## 安装

​	①如果系统中已经存在旧的Docker，则先卸载：

```shell
yum remove docker \
    docker-client \
    docker-client-latest \
    docker-common \
    docker-latest \
    docker-latest-logrotate \
    docker-logrotate \
    docker-engine \
    docker-selinux 
//复制粘贴以上命令然后回车即可
```

​	②配置docker的yum库

```
//首先要安装一个yum工具
sudo yum install -y yum-utils device-mapper-persistent-data lvm2

//安装成功后，执行命令，配置Docker的yum源（已更新为阿里云源）：
sudo yum-config-manager --add-repo https://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo

sudo sed -i 's+download.docker.com+mirrors.aliyun.com/docker-ce+' /etc/yum.repos.d/docker-ce.repo

//更新yum，建立缓存
sudo yum makecache fast
```

​	③安装docker

```
yum install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```

​	④检验

```
docker -v
docker images
```

​	⑤设置开机自启

```
# Docker开机自启
systemctl enable docker

# Docker容器开机自启
docker update --restart=always [容器名/容器id]
```





注意事项

```
问题一：yum安装出现问题：
1、 使用阿里云更新yum源：curl -o /etc/yum.repos.d/CentOS-Base.repo http://mirrors.aliyun.com/repo/Centos-7.repo
2、 清除yum：yum clean all
3、 更新缓存：yum makecache
```





## 启动和校验

```bash
# 启动Docker
systemctl start docker

# 停止Docker
systemctl stop docker

# 重启
systemctl restart docker

# 设置开机自启
systemctl enable docker

# 执行docker ps命令，如果不报错，说明安装启动成功
docker ps
```





## 配置镜像加速

​	前景：因docker服务器在国外，下载东西很慢，所以需要配置镜像加速

​	①可以选择阿里云的镜像加速，点击产品中的容器，再点击容器镜像服务ACR，进入控制台，找到镜像加速器的地址即可。

```bash
# 创建目录
mkdir -p /etc/docker

# 复制内容，注意把其中的镜像加速地址改成你自己的
tee /etc/docker/daemon.json <<-'EOF'
{
  "registry-mirrors": ["https://xxxx.mirror.aliyuncs.com"]
}
EOF

# 重新加载配置
systemctl daemon-reload

# 重启Docker
systemctl restart docker
```

​	②参照阿里云文档即可







## 部署Mysql

```
docker run -d \
  --name mysql \
  -p 3306:3306 \
  -e TZ=Asia/Shanghai \
  -e MYSQL_ROOT_PASSWORD=123 \
  mysql
```







# 基础知识

## 镜像和容器

​	镜像：当我们利用Docker安装应用时，Docker会 自动搜索并下载应用镜像(image) 。镜像不仅包含应用本身，还包含应用运行所需要的环境、配置、系统函数库。

​	容器：Docker会在运行镜像时创建一个隔离环境，称为容器( container)。容器有自己独立的环境，比如独立的ip，网络等等，可以跨系统运行。

​	镜像仓库：Docker官方提供了一个专门管理、存储镜像的网站，并对外开放了镜像上传、下载的权利。https://hub.docker.com/



## 命令解读

```
如下命令
docker run -d \
  --name mysql \
  -p 3306:3306 \
  -e TZ=Asia/Shanghai \
  -e MYSQL_ROOT_PASSWORD=123 \
  mysql:5.7

docker run :创建并运行一个容器，-d 是让容器在后台运行
--name mysql :给容器起个名字,必须唯一
-p 宿主机端口:容器内端口 :设置端口映射，也就是内部容器的端口是3306，但是外部访问不到，所以做一个映射，让外部通过第二个参数值3306去访问。原理就是访问虚拟机的ip地址的3306端口会转到内部容器的3306端口，以此来访问容器内部的mysql。如果要创建第二个mysql，记得宿主机端口要变，容器内不用。
-e KEY=VALUE：是设置环境变量。不同的镜像是不一样的，可以在官方文档查看如何填写
mysql:5.7：指定运行的镜像的名字和版本

镜像命名规范
镜像名称一般分两部分组成: [repository]:[tag]。
	repository就是镜像名
	tag是镜像的版本,若没有指定版本，则默认最新的
```





## 镜像常见命令

​	Docker最常见的命令就是操作镜像、容器的命令，详见官方文档：https://docs.docker.com/中的reference中的Command-line reference就有各种命令解释。

​	都可以在命令后面使用 --help参数查看帮助文档

### docker pull

​	作用：把镜像仓库对应的镜像拉取到本地

### docker images

​	作用：查看所有的本地镜像

### docker rmi

​	作用：删除镜像

### docker build

​	作用：构建镜像，需要先创建一个dockerfile。

### docker save

​	作用：将自己打包的镜像保存到本地的一个压缩文件

### docker load

​	作用：将打包的镜像压缩文件加载到本地镜像库

### docker push

​	作用：将本地镜像推送到镜像仓库(有公用和私用)





## 容器常见命令

### docker run

```
作用：创建一个独立的容器，并在容器内运行docker镜像

在执行docker run命令时，使用-v数据卷:容器内目录可以完成数据卷挂载
当创建容器时，如果挂载了数据卷且数据卷不存在，会自动创建数据卷
```

​	

### docker stop

​	作用：停止docker镜像，本质是停止的内部进程，容器还在。

### docker start

​	作用：启动docker镜像，本质也是启动原本被停止的进程

### docker ps

```
作用：查看目前所有容器内正在运行的所有进程
默认是查看运行中的容器
-a：查看所有容器包括停止进程的容器
同时也可也格式化输出，看着更简洁
docker ps --format "table {{.ID}}\t{{.Image}}\t{{.Ports}}\t{{.Status}}\t{{.Names}}"
```



### docker rm

​	作用：删除容器

### docker logs

```
作用：查看日志
-f：持续跟进日志，会占控制台
docker logs -f nginx
```

​	

### docker exec

```
作用：可以执行一些命令让他执行到容器内部
docker exec -it nginx bash		//进入容器内部进行操作
使用exit退出容器内部
注意：容器内部只有必要的运行环境，例如vi是没有的
```

​	

### docker inspect

```
作用：查看容器详细信息
```

​	

注意：拉取镜像前，一定要先去官方文档查看相关说明





## 命令起别名

```
vi ~/.bashrc	//编辑该文件

添加内容
alias dps=docker ps --format "table {{.ID}}\t{{.Image}}\t{{.Ports}}\t{{.Status}}\t{{.Names}}"

source ~/.bashrc		//重新加载文件
```

​	

## 数据卷

​	

```
概述：数据卷(volume) 是一个虚拟目录，是容器内目录与宿主机目录之间映射的桥梁。

实现：在创建一个数据卷，并指定容器内部文件后，他会在对应宿主文件系统的/var/lib/docker/volumes/虚拟文件名/_data下创建实质性文件。
然后docker会将这个实质性文件与容器内部文件作双向绑定，这个过程我们称为挂载。
/var/lib/docker/volumes这个目录就是默认的存放所有容器数据卷的目录，其下再根据数据卷名称创建新目录，格式为/数据卷名/_data。

相关命令
    docker volume create；创建数据卷
    docker volume ls：查看所有数据卷
    docker volume rm：删除指定数据卷
    docker volume inspect：查看某个数据卷的详情
    docker volume prune：清除数据卷

注意：
    在执行docker run命令时，使用-v数据卷:容器内目录可以完成数据卷挂载
    当创建容器时，如果挂载了数据卷且数据卷不存在，会自动创建数据卷
    容器创建以后不能再挂载了。
例如：
	//创建容器并指定数据卷，注意通过 -v 参数来指定数据卷
docker run -d --name nginx -p 80:80 -v 数据卷名:/usr/share/nginx/html nginx


为什么不让容器目录直接指向宿主机目录呢？
    因为直接指向宿主机目录就与宿主机强耦合了，如果切换了环境，宿主机目录就可能发生改变了。由于容器一旦创建，目录挂载就无法修改，这样容器就无法正常工作了。
    但是容器指向数据卷，一个逻辑名称，而数据卷再指向宿主机目录，就不存在强耦合。如果宿主机目录发生改变，只要改变数据卷与宿主机目录之间的映射关系即可。
```

​	



### 本地目录挂载

```
前言：之前是创建数据卷进行挂载，数据卷的位置太深不方便找，现在可以在宿主机也就是本地任意目录进行挂载。

# 挂载本地目录
	-v 本地目录:容器内目录
# 挂载本地文件
	-v 本地文件:容器内文件
在执行docker run命令时，使用-v本地目录:容器内目录可以完成本地目录挂载

注意：
	本地目录必须以"/” 或"./"开头，如果直接以名称开头，会被识别为数据卷而非本地目录
        -v mysql:/var/lib/mysql会被识别为一个数据卷叫mysql
        -v ./mysql:/var/lib/mysql会被识别为当前目录下的mysq|目录

例子：
mysql在创建并运行镜像的时候，会自动创建一个匿名卷与/var/lib/mysql(数据存储的文件)挂载。匿名卷的名字是一串hash值
docker inspect mysql	//查看MySQL容器详细信息
一般mysql会与本地目录挂载，与数据文件，配置文件，初始化脚本挂载
    ①挂载/root/mysql/data到容器内的/var/lib/mysql目录
    ②挂载/root/mysql/init到容器内的/docker-entrypoint-initdb.d目录,本地目录下可以先放好的SQL脚本
    ③挂载/root/mysql/conf到容器内的/etc/mysql/conf.d目录,本地目录下可以先好的配置文件

先把本地目录创建好，然后执行如下命令
docker run -d \
  --name mysql \
  -p 3306:3306 \
  -e TZ=Asia/Shanghai \
  -e MYSQL_ROOT_PASSWORD=123 \
  -v ./mysql/data:/var/lib/mysql \
  -v ./mysql/conf:/etc/mysql/conf.d \
  -v ./mysql/init:/docker-entrypoint-initdb.d \
  mysql
```

​	





## 自定义镜像

```
概述：镜像就是包含了应用程序、程序运行的系统函数库、运行配置等文件的文件包。构建镜像的过程其实就是把上述文件打包的过程。
自定义镜像本质就是依次准备好程序运行的基础环境、依赖、应用本身、运行配置等文件，并且打包而成。

构建一个Java镜像的步骤:
	①准备一个Linux运行环境
    ②安装JRE并配置环境变量
    ③拷则jar包
    ④编写运行脚本
上述步骤中的每一次操作其实都是在生产一些文件（系统运行环境、函数库、配置最终都是磁盘文件），所以镜像就是一堆文件的集合。
镜像按照操作的步骤分层叠加而成，每一层形成的文件都会单独打包并标记一个唯一id，称为层
    
镜像结构
	入口Entrypoint：镜像运行入口，一般是程序启动的脚本和参数
    层Layer：添加安装包、依赖、配置等，每次操作都形成新的一层。
    基础镜像BaseImage：应用依赖的系统函数库、环境、配置、文件等

```

​	



### DockerFile

```
概述：Dockerfile就是一个文本文件， 其中包含一个个的指令(Instruction), 用指令来说明要执行什么操作来构建镜像。将来Docker可以根据Dockerfile帮我们构建镜像。
作用：编辑相关语法的代码，实现自动打包镜像的功能
常见指令如下:
    FROM：指定基础镜像。例如：FROM centos:6
    ENV：设置环境变量，可在后面指令使用。例如：ENV key value 
    COPY：拷贝本地文件到镜像的指定目录。例如：COPY ./jrell.tar.gz /tmp
    RUN：执行Linux的shell命令，一般是安装过程的命令。例如：RUN tar -zxvf /tmp/jre11.tar.gz && EXPORTS path=/tmp/jrell:$path
    EXPOSE：指定容器运行时监听的端口，是给镜像使用者看的。例如：EXPOSE 8080
    ENTRYPOINT：镜像中应用的启动命令，容器运行时调用。例如：ENTRYPOINT java -jar xx.jar
更多指令参考：https://dqcs.docker.com/engine/reference/builder
```

```
例如要基于Ubuntu镜像来构建一个Java应用，其Dockerfile内容如下：
# 指定基础镜像
FROM ubuntu:16.04
# 配置环境变量，JDK的安装目录、容器内时区
ENV JAVA_DIR=/usr/local
ENV TZ=Asia/Shanghai
# 拷贝jdk和java项目的包
COPY ./jdk8.tar.gz $JAVA_DIR/
COPY ./docker-demo.jar /tmp/app.jar
# 设定时区
RUN ln -snf /usr/share/zoneinfo/$TZ /etc/localtime && echo $TZ > /etc/timezone
# 安装JDK
RUN cd $JAVA_DIR \
 && tar -xf ./jdk8.tar.gz \
 && mv ./jdk1.8.0_144 ./java8
# 配置环境变量
ENV JAVA_HOME=$JAVA_DIR/java8
ENV PATH=$PATH:$JAVA_HOME/bin
# 指定项目监听的端口
EXPOSE 8080
# 入口，java项目的启动命令
ENTRYPOINT ["java", "-jar", "/app.jar"]


简化后：
# 基础镜像
FROM openjdk:11.0-jre-buster
# 设定时区
ENV TZ=Asia/Shanghai
RUN ln -snf /usr/share/zoneinfo/$TZ /etc/localtime && echo $TZ > /etc/timezone
# 拷贝jar包
COPY docker-demo.jar /app.jar
# 入口
ENTRYPOINT ["java", "-jar", "/app.jar"]
```



### 构建镜像

```
前景：编辑好dockerfile之后，就需要构建镜像了
命令：docker build -t 镜像名字:1.0 .
	-t:是给镜像起名，格式依然是repository:tag的格式，不指定tag时,默认为latest
	注意结尾的.：它是指定Dockerfile所在目录，如果就在当前目录则指定为"."，也可以直接指定Dockerfile目录
```

​	



## 网络

```
默认情况下，所有容器都是以bridge方式连接到Docker的一个虚拟网桥上。
docker会默认创建一个名为docker0的网卡，可以通过ip addr查看
加入自定义网络的容器才可以通过容器名互相访问
Docker的网络操作命令如下:
    docker network create：创建一个网络
    docker network 1s：查看所有网络
    docker network rm：删除指定网络
    docker network prune：清除未使用的网络
    docker network connect：使指定容器连接加入某网络
    docker network di sconnect：使指定容器连接离开某网络
    docker network inspect：查看网络详细信息

```







## 项目部署



### DockerCompose

​	概述：Docker Compose通过一个单独的docker-compose.yml模板文件(YAML格式)来定义一组相关联的应用容器，帮助我们实现多个相互关联的Docker容器的快速部署。

​	作用：做项目或者集群的一键部署。

```
docker compose的命令格式如下:
	docker compose [OPTIONS] [COMMAND]

Options
	-f：指定compose文件的路径和名称
	-p：指定project名称
Commands
	up：创建并启动所有service容器
    down：停止并移除所有容器、网络
    ps：列出所有启动的容器
    logs：查看指定容器的日志
    stop：停止容器
    start：启动容器
    restart：重启容器
    top：查看运行的进程
    exec：在指定的运行中容器中执行命令

```

```
例如部署一个java应用的dockercompose.yaml文件：
version: "3.8"

services:
  mysql:
    image: mysql
    container_name: mysql
    ports:
      - "3306:3306"
    environment:
      TZ: Asia/Shanghai
      MYSQL_ROOT_PASSWORD: 123
    volumes:
      - "./mysql/conf:/etc/mysql/conf.d"
      - "./mysql/data:/var/lib/mysql"
      - "./mysql/init:/docker-entrypoint-initdb.d"
    networks:
      - hm-net
  hmall:
    build: 
      context: .
      dockerfile: Dockerfile
    container_name: hmall
    ports:
      - "8080:8080"
    networks:
      - hm-net
    depends_on:
      - mysql
  nginx:
    image: nginx
    container_name: nginx
    ports:
      - "18080:18080"
      - "18081:18081"
    volumes:
      - "./nginx/nginx.conf:/etc/nginx/nginx.conf"
      - "./nginx/html:/usr/share/nginx/html"
    depends_on:
      - hmall
    networks:
      - hm-net
networks:
  hm-net:
    name: hmall
```


