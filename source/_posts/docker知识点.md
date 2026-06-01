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


# 常用积累

| 操作     | 命令                                                         |
| -------- | ------------------------------------------------------------ |
| 拉取镜像 | `docker pull [image]`                                        |
| 运行容器 | `docker run -d -p [host_port]:[container_port] --name [name] [image]` |
| 查看容器 | `docker ps`                                                  |
| 进入容器 | `docker exec -it [container_name] /bin/bash`                 |
| 查看日志 | `docker logs [container_name]`                               |
| 构建镜像 | `docker build -t [image_name] .`                             |
| 删除容器 | `docker rm [container_name]`                                 |
| 删除镜像 | `docker rmi [image_name]`                                    |

```
docker默认需求root权限
    sudo usermod -aG docker $USER
    newgrp docker
    # 退出当前终端并重新登录以生效

准备项目代码，确保项目目录中包含以下内容：
    项目源码（如 app.py、package.json、Dockerfile 等）。
    依赖文件（如 requirements.txt、package-lock.json）。
    
一般在项目根目录创建 Dockerfile，定义镜像构建步骤。

默认情况下，Docker 容器没有设置自动重启策略（--restart=no），因此服务器重启后，容器处于停止状态。
	选择创建容器时配置：docker update -
	-restart=unless-stopped my_container
	已有容器更新配置：docker update --restart=unless-stopped my_container
	查看当前容器重启策略：docker inspect -f '{{.HostConfig.RestartPolicy}}' my_container
    确保docker是自动启动
        sudo systemctl enable docker  # 启用 Docker 开机自启
        sudo systemctl is-enabled docker  # 检查是否已启用

清理构建缓存
	docker builder prune -a

加载镜像到本地仓库
	sudo docker load < cloud_api_sample_docker_v1.0.0.tar

检查容器22端口
	docker ps --filter "publish=22"

docker网络配置
    docker network ls
    docker network inspect 配置名称
    docker network rm 配置id
    docker network prune：清理未使用的网络配置

docker compose相关
	docker-compose up -d：加上-d表示后台启动
	docker-compose ps
	docker-compose down	#停止并删除由 docker-compose up 启动的容器、网络等资源。当加上-v后会删除数据卷，用于彻底清理
	docker-compose up --scale web=3 -d：扩展web服务到3个
	docker-compose logs 容器名称：查看日志，加上-f表示实时查看
	
	
进入容器
	docker exec -it 容器名称 bash

修改镜像名字
    docker tag minio/minio:latest uav-minio:latest	#复制新的镜像并重命名
    docker rmi minio/minio:latest	#删除原本的
	
修改容器内部的配置文件
方法一：
	docker cp <container_id>:/opt/emqx/etc/emqx.conf /home/yixin/	#将容器内的配置文件复制到本地
	docker cp /home/yixin/emqx.conf <container_id>:/opt/emqx/etc/emqx.conf	#再复制到容器内进行覆盖
方法二：
	挂载
```

```bash
# 启动 Docker 服务
sudo systemctl start docker
# 停止 Docker 服务
sudo systemctl stop docker
# 重启 Docker 服务
sudo systemctl restart docker
# 查看 Docker 状态
sudo systemctl status docker
# 设置 Docker 开机自启
sudo systemctl enable docker

# 查看所有 Docker 命令
docker --help
# 查看具体命令的帮助（如 docker run）
docker run --help
```

## 开发流程

| 步骤 | 操作            | 命令                                                         |
| ---- | --------------- | ------------------------------------------------------------ |
| 1    | 编写 Dockerfile | `vim Dockerfile`                                             |
| 2    | 构建镜像        | `docker build -t my-app:1.0 .`                               |
| 3    | 运行容器        | `docker run -d -p 8080:3000 --name my-app-container my-app:1.0` |
| 4    | 查看运行状态    | `docker ps`                                                  |
| 5    | 访问应用        | `curl http://localhost:8080`                                 |
| 6    | 查看日志        | `docker logs my-app-container`                               |

### **1. 环境准备**

```bash
# 查看 Docker 服务
sudo systemctl status docker
sudo systemctl start docker
```

确保项目目录中包含以下内容：

- 项目源码（如 `app.py`、`package.json`、`Dockerfile` 等）。
- 依赖文件（如 `requirements.txt`、`package-lock.json`）。

### **2. 编写 Dockerfile**

如果只是需要一个简单的容器，如只需要python，就可以直接pull拉取镜像即可，但是复杂的镜像就需要自己通过Dockerfile进行构建镜像。

`Dockerfile`是一个文本文件，是构建镜像的蓝图，包含一系列指令（Instruction），用于定义构建 Docker 镜像的步骤。每个指令对应镜像的一层（Layer）。

#### 核心指令

| **指令**      | **作用**                                                 |
| ------------- | -------------------------------------------------------- |
| `FROM`        | 指定基础镜像（必需）。                                   |
| `MAINTAINER`  | 指定镜像维护者（已弃用，推荐用 `LABEL` 替代）。          |
| `RUN`         | 在镜像中执行命令（构建时运行）。                         |
| `CMD`         | 容器启动时默认执行的命令（可被 `docker run` 参数覆盖）。 |
| `ENTRYPOINT`  | 容器启动时执行的命令（不可被覆盖，可追加参数）。         |
| `COPY`        | 将本地文件复制到镜像中（不支持自动解压）。               |
| `ADD`         | 类似 `COPY`，但支持自动解压和远程 URL 下载。             |
| `WORKDIR`     | 设置工作目录（后续指令默认在此目录执行）。               |
| `ENV`         | 设置环境变量（持久化保存）。                             |
| `EXPOSE`      | 声明容器监听的端口（需配合 `docker run -p` 使用）。      |
| `VOLUME`      | 创建挂载点（用于持久化数据）。                           |
| `LABEL`       | 为镜像添加元数据（如作者、版本）。                       |
| `USER`        | 指定后续指令运行的用户（提升安全性）。                   |
| `HEALTHCHECK` | 配置健康检查（监控容器状态）。                           |
| `ARG`         | 定义构建时使用的变量（不保留在最终镜像中）。             |

#### 编写步骤

- 选择基础镜像

- ```
  FROM ubuntu:22.04
  
  建议：
      优先使用官方镜像（如 nginx、python、openjdk）。
      使用轻量版镜像（如 alpine 或 slim）以减少体积。
  ```

- 设置工作目录

- ```
  WORKDIR /app
  
  作用：后续指令默认在此目录下执行。
  ```

- 安装依赖

- ```
  RUN apt update && apt install -y python3
  
  优化技巧：
  	合并多个 RUN 指令以减少镜像层数，如下：
  RUN apt update && \
      apt install -y python3 && \
      apt install -y curl
  ```

- 复制文件到镜像

- ```
  COPY . /app
  
  区别：
      COPY：直接复制文件（推荐用于本地文件）。
      ADD：支持自动解压和远程 URL（谨慎使用，避免意外行为）。
  ```

- 设置环境变量

- ```
  ENV PATH="/app/bin:${PATH}"
  ```

- 暴露端口

- ```
  EXPOSE 80
  
  注意：需在运行容器时用 -p 映射端口（如 docker run -p 8080:80）。
  ```

- 定义启动命令

- ```
  CMD ["python", "app.py"]
  
  与 ENTRYPOINT 的区别：
      CMD 可被 docker run 参数覆盖，适合默认命令。
      ENTRYPOINT 不可被覆盖，适合固定入口程序（如 Java 应用）。
  ```

#### 技巧

**1. 使用 `.dockerignore` 文件**

- **作用**：排除不需要的文件，减少镜像体积。

  ```
  .git
  *.log
  __pycache__
  ```

**2. 多阶段构建（Multi-Stage Build）**

- **场景**：减少最终镜像体积（适合编译型语言如 Java、Go）。

  ```
  # 第一阶段：构建
  FROM maven:3.8.6-jdk-17 AS build
  WORKDIR /app
  COPY pom.xml .
  RUN mvn dependency:go-offline
  COPY src ./src
  RUN mvn package
  
  # 第二阶段：运行
  FROM openjdk:17-jdk-slim
  WORKDIR /app
  COPY --from=build /app/target/app.jar app.jar
  CMD ["java", "-jar", "app.jar"]
  ```

**3. 使用非 Root 用户**

- 安全建议：避免以root用户运行容器。

  ```
  RUN useradd -m myuser
  USER myuser
  ```

**4. 避免敏感信息硬编码**

- 方法：通过环境变量或docker run -e 传递敏感信息（如数据库密码）。

  ```
  ENV DATABASE_URL=postgresql://user:pass@host/db
  ```

**5. 保持镜像轻量化**

- 技巧：
  - 使用 `alpine` 或 `slim` 版本的基础镜像。
  - 删除不必要的依赖包（如 `apt clean`）。



#### 示例

**Java项目**

```dockerfile
# 构建阶段
FROM maven:3.8.6-jdk-17 AS build
WORKDIR /app
COPY pom.xml .
RUN mvn dependency:go-offline
COPY src ./src
RUN mvn package

# 运行阶段
FROM openjdk:17-jdk-slim
WORKDIR /app
COPY --from=build /app/target/app.jar app.jar
CMD ["java", "-jar", "app.jar"]
```

**Node.js 项目**

```Dockerfile
# 使用 Node.js 16.x 镜像作为基础
FROM node:16
# 设置工作目录
WORKDIR /app
# 复制 package.json 和 package-lock.json
COPY package*.json ./
# 安装依赖
RUN npm install
# 复制项目代码
COPY . .
# 暴露应用端口
EXPOSE 3000
# json格式的启动命令 npm start
CMD ["npm", "start"]
```

一般还会编写的一个.dockerignore

```
node_modules
npm-debug.log
.DS_Store
.git
.env
```

**Python 项目**

```Dockerfile
# 使用 Python 3.9 镜像作为基础
FROM python:3.9-slim
# 设置工作目录
WORKDIR /app
# 安装依赖
COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt
# 复制项目代码
COPY . .
# 暴露端口并启动
EXPOSE 5000
CMD ["python", "app.py"]
```

**ubuntu项目**

```dockerfile
# 拉取基于官方的Ubuntu镜像
FROM ubuntu:20.04

#更新软件包索引，不需要升级当前已有的软件包
RUN apt-get update

# 安装JDK
RUN apt-get install -y openjdk-11-jdk
 
# 安装Python
RUN apt-get install -y python3
RUN apt-get install -y python3-pip
RUN apt-get install -y python-is-python3

# 安装opencv(源码编译安装)
RUN apt-get install -y cmake
RUN apt-get install -y build-essential 
RUN apt-get install -y libgtk2.0-dev libavcodec-dev libavformat-dev libjpeg-dev libswscale-dev libtiff5-dev
RUN apt-get install -y libcanberra-gtk-module
RUN apt-get install -y pkg-config
RUN apt-get install -y zip unzip
COPY ./opencv/opencv-4.7.0.zip /gsis_ai/opencv/opencv-4.7.0.zip
RUN unzip -d /gsis_ai/opencv/opencv-4.7.0 /gsis_ai/opencv/opencv-4.7.0.zip
RUN cd /gsis_ai/opencv/opencv-4.7.0/opencv-4.7.0
RUN mkdir build && cd build
RUN cmake -D CMAKE_BUILD_TYPE=Release -D CMAKE_INSTALL_PREFIX=/usr/local ..
RUN make -j$(nproc)
RUN make install
RUN opencv_version
RUN cd /


# 清理安装包及软件包列表
RUN apt-get clean && rm -rf /var/lib/apt/lists/

# 设置JAVA环境变量
ENV JAVA_HOME /usr/lib/jvm/java-11-openjdk-amd64/
ENV PATH $JAVA_HOME/bin:$PATH
 
# 设置工作目录
WORKDIR /app

```



### **3. 构建镜像**

```bash
在项目根目录下执行以下命令：
# 构建镜像并打标签（-t 指定镜像名和版本）
docker build -t my-app:1.0 .
```

- `my-app:1.0`：自定义的镜像名和标签。
- `.`：表示 Dockerfile 所在的当前目录。

### **4. 运行容器**

使用构建好的镜像启动容器：

```bash
# 后台运行容器，映射端口（8080:容器端口）
docker run -d -p 8080:8080 --name my-app-container my-app:1.0

# 后台运行mysql容器，并包含挂载本地配置文件
docker run -d \
  --name mysql-container \          # 容器名称
  -p 主机端口:容器内部端口 \                    # 映射端口
  -v ~/mysql/conf:/etc/mysql/conf.d \  # 挂载配置文件目录，左边时本地的目录，右边时容器内部的目录，
  -v ~/mysql/data:/var/lib/mysql \     # 挂载数据目录
  -e MYSQL_ROOT_PASSWORD=your_password \  # 设置 root 密码
  mysql:latest                      # 使用的 MySQL 镜像
```

- `-d`：后台运行。
- `-p`：映射端口（主机端口:容器端口）。
- `--name`：指定容器名称。
- `-v`：将本地的配置文件目录挂载到容器中。

### **5. 验证部署**

#### **5.1 查看运行中的容器**

```bash
docker ps
docker ps -a
```

输出示例：

```
CONTAINER ID   IMAGE         COMMAND       PORTS                    NAMES
abc123def456   my-app:1.0    "npm start"   0.0.0.0:8080->3000/tcp   my-app-container
```

#### **5.2 访问应用**

通过浏览器或命令行访问应用：

```bash
curl http://localhost:8080
```

#### **5.3 查看容器日志**

```bash
docker logs 容器名称
```

#### 5.4 查看容器具体信息

```
docker inspect 容器名称
```

### **6. 清理资源**

#### **删除容器**

```bash
docker rm my-app-container
```

#### **删除镜像**

```bash
docker rmi my-app:1.0
```

#### **清理无用资源**

```bash
# 删除所有停止的容器、未使用的镜像、网络等
docker system prune -af
```

### **7. 多容器协作Docker Compose**

如果项目需要多个服务（如数据库、缓存），可以使用 `docker-compose.yml` 管理：

```yaml
version: '3'
services:
  app:
    build: .
    ports:
      - "8080:3000"
    volumes:
      - .:/app
  db:
    image: mysql:8.0
    environment:
      MYSQL_ROOT_PASSWORD: root
```

启动所有服务：

```bash
docker-compose up -d
```



## 镜像管理

### 拉取/推送镜像
```bash
# 拉取最新版 Ubuntu 镜像
docker pull ubuntu

# 拉取指定版本的镜像（例如 Node.js 16）
docker pull node:16

# 将本地镜像推送到镜像仓库(有公用和私用)
docker push
```

### 列出本地镜像
```bash
# 列出所有本地镜像
docker images

# 只显示镜像 ID
docker images -q
```

### 删除镜像
```bash
# 删除指定镜像（通过镜像名或 ID）
docker rmi node:16

# 删除所有未使用的镜像
docker image prune -af
```

### 构建镜像

```bash
在项目根目录下执行以下命令：
# 构建镜像并打标签（-t 指定镜像名和版本）
docker build -t my-app:1.0 .
```

- `my-app:1.0`：自定义的镜像名和标签。
- `.`：表示 Dockerfile 所在的当前目录。

### 保存/加载镜像

```
docker load	# 将打包的镜像压缩文件加载到本地镜像库
	
docker save	#将自己打包的镜像保存到本地的一个压缩文件
```



## 容器管理
### **运行容器**
```bash
# 交互式运行 Ubuntu 容器并进入终端
docker run -it 容器名称如Ubuntu /bin/bash

# 后台运行 Nginx 容器并映射端口
docker run -d -p 8080:80 --name my-nginx nginx

# 参数说明：
# -d: 后台运行
# -p: 映射端口（主机端口:容器端口）
# --name: 指定容器名称
```

### 查看运行中的容器
```bash
# 查看所有运行中的容器
docker ps

# 查看所有容器（包括已停止的）
docker ps -a
```

### 停止/启动/重启容器
```bash
# 停止容器（通过容器名或 ID）
docker stop my-nginx

# 启动已停止的容器
docker start my-nginx

# 重启容器
docker restart my-nginx
```

### 删除容器
```bash
# 删除已停止的容器
docker rm my-nginx

# 强制删除运行中的容器
docker rm -f my-nginx

# 删除所有停止的容器
docker container prune -f
```



## 容器调试
### 进入运行中的容器
```bash
# 进入容器的 Bash 终端
docker exec -it my-nginx /bin/bash

# 在容器内执行单条命令（例如查看文件列表）
docker exec my-nginx ls /var/www/html
```

### 查看容器日志
```bash
# 查看容器日志（默认显示最新日志）
docker logs my-nginx

# 实时跟踪日志（类似 tail -f）
docker logs -f my-nginx

# 查看最近 100 行日志
docker logs --tail 100 my-nginx

# 查看容器具体信息
docker inspect 容器名称
```

## 构建和运行自定义镜像
### 编写 Dockerfile
在项目根目录创建 `Dockerfile`，示例：
```Dockerfile
nodejs项目
# 使用 Node.js 16.x 镜像作为基础
FROM node:16
# 设置工作目录
WORKDIR /app
# 复制 package.json 和 package-lock.json
COPY package*.json ./
# 安装依赖
RUN npm install
# 复制项目代码
COPY . .
# 暴露应用端口
EXPOSE 3000
# 启动命令
CMD ["npm", "start"]

或Python项目
# 使用 Python 3.9 镜像作为基础
FROM python:3.9-slim
# 设置工作目录
WORKDIR /app
# 安装依赖
COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt
# 复制项目代码
COPY . .
# 暴露端口并启动
EXPOSE 5000
CMD ["python", "app.py"]
```

实例Java项目

```Dockerfile
如简单场景的单阶段构建Dockerfile编写
# 使用 OpenJDK 作为基础镜像
FROM openjdk:17-jdk-slim
# 设置工作目录
WORKDIR /app
# 复制 JAR 文件到容器中
COPY target/your-app.jar app.jar
# 暴露应用端口（与 Spring Boot 配置一致）
EXPOSE 8080
# 启动命令
ENTRYPOINT ["java", "-jar", "app.jar"]

或者多阶段构建编写
# 第一阶段：构建阶段（使用 Maven 镜像）
FROM maven:3.8.4-openjdk-17 AS build
WORKDIR /app
COPY pom.xml .
RUN mvn dependency:go-offline
COPY src ./src
RUN mvn package -DskipTests

# 第二阶段：运行阶段（使用轻量级 JRE 镜像）
FROM openjdk:17-jre-slim
WORKDIR /app
COPY --from=build /app/target/your-app.jar app.jar
EXPOSE 8080
ENTRYPOINT ["java", "-jar", "app.jar"]
```

###  构建镜像

```bash
# 构建镜像并打标签（-t 指定镜像名和标签，替换 your-image-name 为实际名称）
docker build -t your-image-name:tag .
eg：docker build -t my-springboot-app:1.0 .

# 若出错，修复后，清除缓存后在构建
docker build --no-cache -t my-nodejs-app:1.0 .

```

### 运行自定义镜像
```bash
# 后台运行容器，映射宿主机 8080 端口到容器 8080 端口
docker run -d -p 8080:8080 --name 容器名称 your-image-name:tag
eg：docker run -d -p 8080:8080 --name my-container my-springboot-app:1.0
```

### 可选参数配置

```
挂载配置文件（动态更新配置）：
docker run -d -p 8080:8080 \
  -v /path/to/local/application.yml:/app/config/application.yml \
  --name my-container your-image-name:tag
  
设置环境变量（如数据库连接）：
docker run -d -p 8080:8080 \
  -e SPRING_DATASOURCE_URL=jdbc:mysql://db-host:3306/mydb \
  --name my-container your-image-name:tag
```

### 查看运行状态

```
docker ps

查看容器日志
docker logs my-container
```

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



## 挂载代码目录（实时同步）

```bash
# 运行容器时挂载宿主机代码目录到容器内
docker run -d -p 3000:3000 \
  -v /path/to/your/project:/app \
  --name my-app \
  my-node-app:1.0
```
- 修改宿主机的代码会自动同步到容器中，无需重新构建镜像。

## 环境变量注入
```bash
# 运行容器时设置环境变量
docker run -e MY_ENV_VAR=value my-node-app:1.0
```

## Docker Compose使用

**Docker Compose** 是 Docker 的一个子项目，用于定义和运行多容器 Docker 应用程序。通过一个 YAML 文件（`docker-compose.yml`）配置应用程序的多个服务及其依赖关系，然后使用一条命令启动、停止或管理整个应用栈。

创建 `docker-compose.yml` 文件：
```yaml
version: '3'
services:
  web:
    build: .
    ports:
      - "3000:3000"
    volumes:
      - .:/app
  db:
    image: mysql:8.0
    environment:
      MYSQL_ROOT_PASSWORD: root
```
```bash
启动所有服务：docker-compose up -d
关闭并删除所有服务：docker-compose down
```

示例，多容器

```
version: "3"
services:
  nginx:
    image: dji/nginx:latest
    networks:
      - cloud_service_bridge
    ports:
      - "8080:8080"
    volumes:
      - /etc/localtime:/etc/localtime
  cloud_api_sample:
    image: dji/cloud_api_sample:latest
    depends_on:
      - mysql
      - emqx
      - redis
    ports:
      - "6789:6789"
    volumes:
      - /etc/localtime:/etc/localtime  
      - /home/huijie/project/application.yml:/app/sample/src/main/resources/application.yml
    environment:
      - SPRING_CONFIG_LOCATION=file:/app/sample/src/main/resources/application.yml
    hostname: cloud_api_sample
    restart: "always" 
    networks:
      - cloud_service_bridge
  emqx:
    image: emqx:4.4
    ports:
      - "18083:18083"
      - "1883:1883"
      - "8083:8083"
      - "8883:8883"
      - "8084:8084"
    environment:
      - EMQX_ALLOW_ANONYMOUS=true
    hostname: emqx-broker
    networks:
      - cloud_service_bridge
  mysql:
    image: dji/mysql:latest
    networks:
      - cloud_service_bridge
    ports:
      - "3307:3306"
    volumes:
            # - /etc/group:/etc/group:ro
            # - /etc/passwd:/etc/passwd:ro
      - /etc/localtime:/etc/localtime
      - ./data/mysql:/var/lib/mysql
    environment:
      - MYSQL_ROOT_PASSWORD=root
    hostname: cloud_api_sample_mysql
  redis:
    image: redis:6.2
    restart: "always"
    hostname: cloud_api_sample_redis
    ports:
      - "6378:6379"
    networks:
      - cloud_service_bridge
    command:
      redis-server

networks:
  cloud_service_bridge:
    driver: bridge
    ipam:
      config:
        - subnet: 192.168.6.0/24

如果要手动为容器赋予静态ip时，要对所有容器都进行静态指定，不然可能会造成ip冲突问题。要在定义网络配置的ip段当中
如：
    networks:
      cloud_service_bridge:
        ipv4_address: 192.168.6.2
```

### 常用命令

| 命令                                  | 说明                                                     |
| ------------------------------------- | -------------------------------------------------------- |
| `docker-compose up`                   | 启动所有服务（默认前台运行，加 `-d` 后台运行）           |
| `docker-compose down`                 | 停止并删除容器、网络和卷                                 |
| `docker-compose ps`                   | 列出当前运行的容器                                       |
| `docker-compose logs`                 | 查看服务日志（加 `-f` 实时跟踪）                         |
| `docker-compose build`                | 构建服务镜像（需在 `docker-compose.yml` 中定义 `build`） |
| `docker-compose exec <service> <cmd>` | 在运行中的容器中执行命令（如进入 shell）                 |
| `docker-compose restart <service>`    | 重启服务                                                 |
| `docker-compose stop <service>`       | 停止服务                                                 |
| `docker-compose start <service>`      | 启动已停止的服务                                         |
| `docker-compose pause <service>`      | 暂停服务                                                 |
| `docker-compose unpause <service>`    | 恢复服务                                                 |

---

### 注意事项

#### **1. 多环境配置**

- 使用 `docker-compose.override.yml` 重写默认配置（适用于开发环境）。

- 示例：  

  ```yaml
  # docker-compose.override.yml
  services:
    db:
      environment:
        MYSQL_ROOT_PASSWORD: dev_password
  ```

#### **2. 生产环境优化**

- **资源限制**：  

  ```yaml
  services:
    web:
      deploy:
        resources:
          limits:
            cpus: "0.5"
            memory: 512M
  ```

- **健康检查**：  

  ```yaml
  services:
    db:
      healthcheck:
        test: ["CMD", "mysqladmin", "ping", "-h", "localhost"]
        interval: 10s
        timeout: 5s
        retries: 3
  ```











## **清理资源**

```bash
# 清理所有停止的容器、未使用的镜像、网络等
docker system prune -af

# 清理未使用的镜像
docker image prune -af

# 清理未使用的卷
docker volume prune -f
```

## 推送镜像到远程仓库

```
登录 Docker Hub
docker login

推送镜像
docker push your-dockerhub-username/your-image-name:tag

在远程服务器拉取并运行
docker pull your-dockerhub-username/your-image-name:tag
docker run -d -p 8080:8080 your-dockerhub-username/your-image-name:tag

使用 Docker Compose（多服务部署）
如果项目包含多个服务（如数据库、Redis），可使用 docker-compose.yml 管理：
version: '3.8'
services:
  app:
    build: .
    ports:
      - "8080:8080"
    environment:
      - SPRING_PROFILES_ACTIVE=prod
    volumes:
      - ./logs:/app/logs
    depends_on:
      - db
  db:
    image: mysql:8.0
    ports:
      - "3306:3306"
    environment:
      - MYSQL_ROOT_PASSWORD=root
    volumes:
      - db_data:/var/lib/mysql
volumes:
  db_data:
  
启动所有服务：
docker-compose up -d
```



## 构建上下文

构建上下文（Build Context）是 Docker 构建镜像时的核心概念之一，它决定了 Docker 在构建过程中可以访问哪些文件和目录。

### **1. 构建上下文的定义**

构建上下文是 Docker 客户端在执行 `docker build` 命令时，发送给 Docker 服务端（Docker 引擎）的一组文件和目录的集合。这些文件包括：

- **Dockerfile**：定义镜像构建步骤的文件。
- **其他资源文件**：Dockerfile 中引用的文件（如代码、配置文件、依赖包等）。

Docker 会将这些文件打包成一个 tar 包，并发送到 Docker 服务端进行镜像构建。因此，**Dockerfile 中引用的任何文件必须位于构建上下文中**，否则构建会失败。

### **2. 如何指定构建上下文**

构建上下文的指定是通过 `docker build` 命令的最后一个参数完成的。该参数可以是以下几种形式：

1. **本地目录路径**（最常见）：

   - 示例：`docker build -t myimage:latest .`
   - `.` 表示当前目录作为构建上下文。
   - Docker 会将当前目录及其所有子目录中的文件打包并发送到 Docker 服务端。

2. **远程 Git 仓库**：

   - 示例：`docker build https://github.com/user/repo.git`

   - Docker 会克隆 Git 仓库并将其作为构建上下文。

   - 可以通过 `#<branch>:<subdir>` 指定分支和子目录：

     ```bash
     docker build https://github.com/user/repo.git#main:subdir
     ```

3. **远程压缩包（tarball）**：

   - 示例：`docker build https://example.com/context.tar.gz`
   - Docker 会下载并解压该压缩包作为构建上下文。

4. **纯文本文件（通过管道传递 Dockerfile）**：

   - 示例：`cat Dockerfile | docker build -`
   - 将 Dockerfile 通过管道传递给 Docker，此时构建上下文为空（仅包含 Dockerfile）。

### **3. 构建上下文的作用**

- **资源访问范围**：Dockerfile 中的指令（如 `COPY`、`ADD`）只能引用构建上下文内的文件。
  - 例如，如果 Dockerfile 中有 `COPY app.py .`，则 `app.py` 必须位于构建上下文目录中。
- **构建缓存**：Docker 会基于构建上下文的内容判断是否可以使用缓存，提升构建速度。
- **安全性**：构建上下文的范围直接影响发送到 Docker 服务端的数据量，避免敏感信息泄露。

### **4. 构建上下文的常见参数**

#### **(1) `-f` 或 `--file`**

- **作用**：指定 Dockerfile 的路径（默认为当前目录下的 `Dockerfile`）。

- **示例**：

  ```bash
  docker build -f /path/to/Dockerfile -t myimage:latest .
  ```

- **注意事项**：

  - Dockerfile 必须位于构建上下文目录内。
  - 如果 Dockerfile 位于子目录，需要调整路径（如 `-f ./subdir/Dockerfile`）。

#### **(2) `.`（当前目录）**

- **作用**：表示当前目录作为构建上下文。

- **示例**：

  ```bash
  docker build -t myimage:latest .
  ```

- **隐含操作**：

  - Docker 会将当前目录及其所有子目录打包发送到 Docker 服务端。
  - 如果目录过大，可能会显著影响构建速度。

#### **(3) `.dockerignore` 文件**

- **作用**：排除不需要发送到 Docker 服务端的文件或目录，优化构建上下文大小。

- **示例**：

  ```plaintext
  # .dockerignore
  *.log
  __pycache__
  .git
  ```

- **语法**：

  - 支持通配符（如 `*.tmp`）和目录路径（如 `node_modules/`）。
  - 类似 `.gitignore` 的语法。

#### **(4) `--compress`**

- **作用**：使用 gzip 压缩构建上下文，减少传输时间。

- **示例**：

  ```bash
  docker build --compress -t myimage:latest .
  ```

#### **(5) `--build-arg`**

- **作用**：传递构建时变量（Build Args）给 Dockerfile。

- **示例**：

  ```bash
  docker build --build-arg VERSION=1.0 -t myimage:latest .
  ```

- **Dockerfile 中使用**：

  ```dockerfile
  ARG VERSION
  RUN echo "Building version $VERSION"
  ```

#### **(6) `--target`**

- **作用**：指定多阶段构建中的目标阶段（Target Stage）。

- **示例**：

  ```bash
  docker build --target builder -t myimage:builder .
  ```

- **Dockerfile 示例**：

  ```dockerfile
  FROM golang:1.21 AS builder
  WORKDIR /app
  COPY . .
  RUN go build -o myapp
  
  FROM alpine:latest
  COPY --from=builder /app/myapp /usr/local/bin/
  CMD ["myapp"]
  ```

### **5. 构建上下文的优化建议**

1. **避免使用根目录作为上下文**：

   - 示例：`docker build -t myimage:latest /` 会导致发送整个硬盘内容，极不安全且低效。
   - **推荐**：将 Dockerfile 放在项目根目录，并确保上下文仅包含必要文件。

2. **使用 `.dockerignore` 文件**：

   - 排除大型文件（如 `node_modules/`、`.git/`、`*.log`）。

   - 示例 `.dockerignore` 内容：

     ```plaintext
     node_modules/
     .git/
     *.log
     ```

3. **多阶段构建**：

   - 减少最终镜像的大小，避免将构建工具（如 `go`、`npm`）打包到最终镜像中。

   - 示例：

     ```dockerfile
     FROM golang:1.21 AS builder
     WORKDIR /app
     COPY . .
     RUN go build -o myapp
     
     FROM alpine:latest
     COPY --from=builder /app/myapp /usr/local/bin/
     CMD ["myapp"]
     ```

4. **分层构建**：

   - 通过合理设计 Dockerfile 层，利用缓存加速后续构建。

### **6. 常见问题与解决方案**

#### **Q1: 构建失败提示“no such file or directory”**

- **原因**：Dockerfile 中引用的文件不在构建上下文中。
- **解决方案**：
  - 确保文件位于构建上下文目录内。
  - 或者调整 Dockerfile 中的路径（如 `COPY ../subdir/file .`）。

#### **Q2: 构建上下文过大导致速度慢**

- **原因**：未使用 `.dockerignore` 文件，或者包含不必要的文件。
- **解决方案**：
  - 创建 `.dockerignore` 文件，排除无关文件。
  - 将项目拆分为更小的模块，分别构建镜像。

#### **Q3: 如何指定远程 Git 仓库的子目录作为上下文？**

- **示例**：

  ```bash
  docker build https://github.com/user/repo.git#main:subdir
  ```

  - `main` 是 Git 分支，`subdir` 是仓库中的子目录。

### **7. 总结**

构建上下文是 Docker 构建镜像的基础，正确指定和管理上下文可以显著提升构建效率和安全性。以下是关键点总结：

- **上下文路径**：通过 `docker build` 命令的最后一个参数指定（如 `.` 表示当前目录）。
- **Dockerfile 位置**：使用 `-f` 参数指定 Dockerfile 的路径。
- **优化工具**：使用 `.dockerignore` 文件、`--compress` 参数和多阶段构建。
- **注意事项**：避免使用根目录作为上下文，确保 Dockerfile 和所需文件位于上下文中。

通过合理配置构建上下文，可以高效地构建出符合需求的 Docker 镜像。



# 前言

## 什么是Docker

概述：Docker 是一个开源的应用容器引擎，它允许开发者将应用及其依赖打包到一个可移植的容器中，然后发布到任何流行的 Linux 机器上，也可以实现虚拟化。Docker 的核心理念是“构建一次，随处运行”(通过 **容器化技术** 实现)，这意味着开发者可以将应用和所有依赖项打包到一个容器中，这个容器可以在任何支持 Docker 的环境中运行，无论是在本地开发机器、测试服务器还是生产服务器上。

简单来说：一个快速构建，运行，管理应用的工具。

## 需求与解决

- 传统部署的痛点：应用程序依赖环境复杂（不同操作系统、库版本、配置），导致“在我的机器上能运行”（Works on My Machine）的问题。
- 虚拟化技术的局限：虚拟机（VM）需要完整的操作系统，资源占用高，启动慢，且难以快速扩展。
- Docker 的解决方案：基于 Linux 内核技术（如 `namespaces` 和 `cgroups`），提供轻量级的进程隔离，实现高效的资源利用。

- 微服务似然具备各种各样的优势，但是服务的拆分通常给部署带来了很大的麻烦
  - 分布式系统中，依赖的组件非常多，不同组件之间部署时，往往会产生一些冲突
  - 在数百，数千台服务中重复部署，环境不一定一直，会遇到各种问题

- 大型项目组件比较多，运行环境也较为复杂，部署时会碰到一些问题
  - 依赖关系复杂，容易出现兼容性问题
  - 开发、测试、生产环境有差异

- 例如一个项目中，部署时需要依赖于node.js、Redis、RabbitM、MySQL等，这些服务部署时所需要的函数库、依赖项各不相同，审核会有冲突，给部署带来了极大的困难

**Docker解决依赖兼容问题**

- 而Docker确巧妙的解决了这些问题，那么Docker是如何实现的呢？
- Docker为了解决依赖的兼容问题，采用了两个手段
  1. 将应用的函数库(libs)、依赖(Deps)、配置与应用一起打包
  2. 将每个应用放到一个隔离容器去运行，避免相互干扰
- 这样打包好的应用中，既包含了应用本身，也包含了应用所需要用到的函数库和依赖，无序在操作系统上安装这些，自然也就不存在不同应用之间的兼容问题了
- 虽然解决了不同应用的兼容问题，但是开发、测试等环节会存在差异，操作系统版本也会有差异，这些问题又该如何解决呢？

**Docker解决操作系统环境差异**

- 要解决不同操作系统环境差异问题，必须先了解操作系统结构，以一个Ubuntu操作系统为例，结构如下
  - 系统应用：操作系统本身提供的应用、函数库。这些含数据是对内核指令的封装，使用更加方便
  - 系统内核：所有Linux发行版的内核都是Linux，例如CentOS、Ubuntu、Fedora等。内核可以与计算机硬件交互，对外提供内核指令，用于操作计算机硬件。
  - 计算机硬件：例如CPU、内存、磁盘等
- 应用于计算机交互的流程如下
  1. 应用调用操作系统应用(函数库)，实现各种功能
  2. 系统函数库是对内核指令集的封装，会调用内核指令
  3. 内核指令操作计算机硬件
- Ubuntu和CentOS都是基于Linux内核，无非是系统应用不同，提供的函数库有差异
- 此时，如果将一个Ubuntu版本的MySQL应用安装到CentOS系统，MySQL在调用Ubuntu函数库时，会发现找不到或不匹配，就会报错
- Docker如何解决不同系统环境的问题？
  - Docker将用户程序所需要的系统(比如Ubuntu)函数库一起打包
  - Docker运行到不同操作系统时，直接基于打包的函数库，借助于操作系统的Linux内核来运行

**小结**

- Docker如何解决大型项目依赖关系复杂，不同组件依赖的兼容性问题？
  - Docker允许开发中将应用、依赖、函数库、配置一起`打包`，形成可移植镜像
  - Docker应用运行在容器中，使用沙箱机制，相互`隔离`
- Docker如何解决开发、测试、生产环境有差异的问题？
  - Docker景象中包含完整运行环境，包括系统函数库，仅依赖系统的Linux内核，因此可以在任意Linux操作系统上运行
- Docker是一个快速交付应用、运行应用的技术，具备以下优势
  1. 可将程序及其依赖、运行环境一起打包为一个镜像，可以迁移到任意Linux操作系统
  2. 运行时利用沙箱机制形成隔离容器，各个应用互不干扰
  3. 启动、移除都可以通过一行命令完成，方便快捷



## **工作原理**

Docker 通过 **Linux 内核技术** 实现容器化：

### **核心技术**

- Namespace（命名空间）：
  - **PID**：进程隔离，每个容器有独立的进程树。
  - **NET**：独立的网络栈（IP、端口）。
  - **Mount**：隔离的文件系统。
  - **UTS**：独立的主机名和域名。
  - **IPC**：独立的进程间通信资源。
  - **User**：用户和用户组隔离。
- Cgroups（控制组）：
  - 限制、记录和隔离进程组的资源（CPU、内存、磁盘 I/O 等）。

### **容器启动流程**

1. 用户运行 `docker run` 命令。
2. Docker 守护进程检查本地镜像，若不存在则从仓库拉取。
3. 根据镜像创建容器，并分配资源（如网络、存储）。
4. 启动容器内的主进程（通过 `CMD` 或 `ENTRYPOINT` 定义的命令）。
5. 用户可通过 `docker exec` 与容器交互。



## Docker与虚拟机的区别

- Docker可以让一个应用在任何操作系统中都十分方便的运行，而我们以前接触的虚拟机，也能在一个操作系统中，运行另外一个操作系统，保护系统中的任何应用
- 二者有什么差异呢？
  - 虚拟机(virtual machine)是在操作系统中`模拟`硬件设备，然后运行另一个操作系统。例如在Windows系统中运行CentOS系统，就可以运行任意的CentOS应用了
  - Docker仅仅是封装函数库，并没有模拟完整的操作系统

| **特性**     | **Docker 容器**         | **虚拟机（VM）**           |
| ------------ | ----------------------- | -------------------------- |
| **资源占用** | 轻量级（共享内核）      | 重型（需完整 OS）          |
| **启动时间** | 秒级                    | 分钟级                     |
| **隔离性**   | 进程级隔离              | 硬件级隔离                 |
| **适用场景** | 单应用或多进程应用      | 需完整 OS 环境的复杂应用   |
| **典型用途** | 微服务、CI/CD、开发环境 | 跨平台虚拟化、隔离敏感应用 |



## Docker架构

Docker 的架构为 **客户端-服务器（C/S）模式**。

- 服务端(server)：Docker守护进程，负责处理Docker指令，管理镜像、容器等
- 客户端(client)：通过命令或RestAPI向Docker服务端发送指令，可以在本地或远程向服务端发送指令

核心组件如下：

### 客户端Client

- 用户通过命令行（`docker` 命令）或 GUI 工具与 Docker 守护进程通信。

### 守护进程Daemon

- 运行在宿主机上的后台服务，负责：
  - 构建、运行、监控容器。
  - 管理镜像、网络和存储卷。
  - 处理来自客户端的请求。

### 镜像仓库Registry

- 存储和分发镜像的中心，典型如 Docker Hub。
- **私有仓库**：企业常用 Harbor、GitLab Registry 等。

### **容器与镜像**

- **镜像(Image)**：Docker将应用程序及其所需要的依赖、函数库、环境、配置等文件打包在一起，称为镜像
- **容器(Container)**：镜像中的应用程序形成后的进程就是`容器`，只是Docker会给容器进程做隔离，对外不可见。镜像运行起来就是容器，一个镜像可以运行多个容器。
- 一切应用最终都是代码组成，都是硬盘中的一个个字节形成的文件，只有运行时，才会加载到内存，形成进程
- 而`镜像`，就是吧一个应用在硬盘上的文件、机器运行环境、部分系统函数库文件一起打包成的文件包。这个文件包是只读的（防止你对镜像文件进行修改/污染，从而导致镜像不可用，容器从镜像中拷贝一份文件到自己的空间里来写数据）
- 而`容器`呢，就是把这些文件中编写的程序、函数加载到内存中允许形成进程，只不过要隔离起来。因此一个镜像可以启动多次，形成多个容器进程。

## DockerHub

- 一个镜像托管的服务器，类似的还有阿里云镜像服务，统称为DockerRegistry

- 开源应用程序非常多，打包这些应用往往都是重复性劳动，为了避免这些重复劳动，人们就会将自己打包的应用镜像，例如Redis、MySQL镜像放到网络上来共享使用，就像GitHub的代码共享一样
  - DockerHub：DockerHub是一个官方的Docker镜像托管平台，这样的平台称为Docker Registry。
  - 国内也有类似于DockerHub的公开服务，例如[网易云镜像服务](https://c.163yun.com/hub)、[阿里云镜像库](https://cr.console.aliyun.com/)等
- 我们一方面可以将自己的镜像共享到DockerHub，另一方面也可以从DockerHub拉取镜像

## 生态系统

- **Docker Compose**：通过 YAML 文件定义多容器应用（如微服务）。
- **Docker Swarm**：轻量级容器集群管理工具。
- **Kubernetes**：更强大的容器编排系统（Docker 容器是其基础）。
- **Docker Hub**：全球最大容器镜像仓库。
- **Portainer**：图形化管理 Docker 环境。



# 安装Docker

- Docker 分为 CE 和 EE 两大版本。CE 即社区版，免费，支持周期 7 个月；EE 即企业版，强调安全，付费使用，支持周期 24 个月。
- Docker CE 分为 `stable` `test` 和 `nightly` 三个更新频道。
- 官方网站上有各种环境下的 [安装指南](https://docs.docker.com/install/)，这里主要介绍 Docker CE 在 CentOS上的安装。
- Docker CE 支持 64 位版本 CentOS 7，并且要求内核版本不低于 3.10

CentOS 7满足最低内核要求，本文也是在CentOS 7安装Docker

### 卸载(可选)

- 如果之前安装过旧版本的Docker，可以使用下面命令卸载

```
yum remove docker \
                  docker-client \
                  docker-client-latest \
                  docker-common \
                  docker-latest \
                  docker-latest-logrotate \
                  docker-logrotate \
                  docker-selinux \
                  docker-engine-selinux \
                  docker-engine \
                  docker-ce
```

### 安装Docker

- 首先先安装yum工具

```
bash
yum install -y yum-utils \
           device-mapper-persistent-data \
           lvm2 --skip-broken
```

- 然后更新本地镜像源

```
bash
## 设置Docker镜像源
yum-config-manager \
    --add-repo \
    https://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo
    
sed -i 's/download.docker.com/mirrors.aliyun.com\/docker-ce/g' /etc/yum.repos.d/docker-ce.repo

yum makecache fast
```

- 然后安装社区版Docker

```
bash
yum install -y docker-ce
```

### 启动Docker

- Docker应用需要用到各种端口，挨个修改防火墙设置很麻烦，所以这里建议直接关闭防火墙

```
bash
## 关闭
systemctl stop firewalld
## 禁止开机启动防火墙
systemctl disable firewalld
```

- 通过命令启动Docker

```
bash
## 启动docker服务
systemctl start docker 

## 停止docker服务
systemctl stop docker

## 重启docker服务
systemctl restart docker
```

- 然后输入命令，查看docker版本

```
bash
docker -v
```

结果

```
bash
[root@localhost ~]## docker -v
Docker version 20.10.21, build baeda1f
```

### 配置镜像加速

- Docker官方镜像库网速较差，建议设置为国内镜像服务，参考阿里云的镜像加速文档：https://cr.console.aliyun.com/cn-hangzhou/instances/mirrors

```
# 创建目录
mkdir -p /etc/docker

# 复制内容，注意把其中的镜像加速地址改成你自己的
tee /etc/docker/daemon.json <<-'EOF'
{
  "registry-mirrors": ["https://xxxx.mirror.aliyuncs.com"]
}
EOF
#或直接编辑
sudo vim /etc/docker/daemon.json
{
        "data-root": "/home/docker2",
        "registry-mirrors": [
                "https://docker.1panel.live",
                "https://do cker.m.daocloud.io",
                "https://quay.mirrors.ustc.edu.cn",
                "https://hub-mirror.c.163.com/",
                "https://dockerproxy.com/"
        ]
}





# 重新加载配置
systemctl daemon-reload
	
# 重启Docker
systemctl restart docker
```





# 理论

## 镜像Image

定义：只读模板，包含运行应用程序所需的所有内容（代码、依赖、环境配置）。

​	当我们利用Docker安装应用时，Docker会 自动搜索并下载应用镜像(image) 。镜像不仅包含应用本身，还包含应用运行所需要的环境、配置、系统函数库。

特点：

- **分层结构**：由多个只读层叠加而成（如基础 OS 层 + 应用层）。
- **可重复性**：同一镜像在任何环境生成的容器行为一致。

创建方式：

- 通过 `Dockerfile` 构建（定义构建步骤）。
- 从 Docker Hub 或私有仓库拉取现成镜像（如 `nginx:latest`）。

**镜像名称**

- 首先来看下镜像的名称组成：

  - 镜像名称一般分为两部分：[repository]:[tag]

  例如`mysql:5.7`，这里的mysql就是repository，5.7就是tag，合在一起就是镜像名称，代表5.7版本的MySQL镜像

  - 在没有指定tag时，默认是latest，代表最新版本的镜像，例如`mysql:latest`

## 容器(Container)

容器：Docker会在运行镜像时创建一个隔离环境，称为容器( container)。容器有自己独立的环境，比如独立的ip，网络等等，可以跨系统运行。

镜像仓库：Docker官方提供了一个专门管理、存储镜像的网站，并对外开放了镜像上传、下载的权利。https://hub.docker.com/

定义：镜像的运行实例，具有独立的文件系统、网络、进程空间。

特点：

- **动态**：容器是运行时的实体，可启动、停止、删除。
- **轻量级**：共享宿主机内核，无需虚拟化开销。

生命周期：从 `docker run` 启动到 `docker stop` 或 `docker kill` 结束。

- 容器保护三个状态
  - 运行：进程正常运行
  - 暂停：进程暂停，CPU不再运行，不释放内存
  - 停止：进程终止，回收进程占用的内存、CPU等资源

- 暂停和停止的操作系统的处理方式不同，暂停是操作系统将容器内的进程挂起，容器关联的内存暂存起来，然后CPU不再执行这个进程，但是使用`unpause`命令恢复，内存空间被恢复，程序继续运行。

- 停止是直接将进程杀死，容器所占的内存回收，保存的仅剩容器的文件系统，也就是那些静态的资源

- `docker pause`：让一个运行的容器暂停

- `docker unpause`：让一个容器从暂停状态恢复运行

  

### 案例一

- 创建并运行nginx容器的命令

```
bash
docker run --name containerName -p 80:80 -d nginx
```

- 命令解读
  - `docker run`：创建并运行一个容器
  - `--name`：给容器起一个名字，例如叫做myNginx
  - `-p`：将宿主机端口与容器端口映射，冒号左侧是宿主机端口，右侧是容器端口
  - `-d`：后台运行容器
  - `nginx`：镜像名称，例如nginx
- 这里的`-p`参数，是将容器端口映射到宿主机端口
- 默认情况下，容器是隔离环境，我们直接访问宿主机的80端口，肯定访问不到容器中的nginx
- 现在，容器的80端口和宿主机的80端口关联了起来，当我们访问宿主机的80端口时，就会被映射到容器的80端口，这样就能访问nginx了
- 那我们再浏览器输入虚拟机ip:80就能看到nginx默认页面了

### 案例二

需求：进入Nginx容器，修改HTML文件内容，添加`Welcome To My Blog！`
提示：进入容器要用到`docker exec`命令

```
bash

[root@localhost ~]## docker exec --help

Usage:  docker exec [OPTIONS] CONTAINER COMMAND [ARG...]

Run a command in a running container

Options:
  -d, --detach               Detached mode: run command in the background
      --detach-keys string   Override the key sequence for detaching a container
  -e, --env list             Set environment variables
      --env-file list        Read in a file of environment variables
  -i, --interactive          Keep STDIN open even if not attached
      --privileged           Give extended privileges to the command
  -t, --tty                  Allocate a pseudo-TTY
  -u, --user string          Username or UID (format: <name|uid>[:<group|gid>])
  -w, --workdir string       Working directory inside the container
```

1. 进入容器。进入刚刚我们创建好的nginx容器

```
bash
docker exec -it myNginx bash
```

- 命令解读
  - `docker exec`：进入容器内部，执行一个命令
  - `-it`：给当前进入的容器创建一个标准输入、输出终端，允许我们与容器交互
  - `myNginx`：要进入的容器名称
  - `bash`：进入容器后执行的命令，bash是一个linux终端交互命令

1. 进入nginx的HTML所在目录

   - 容器内部会模拟一个独立的Linux文件系统，看起来就如同一个linux服务器一样，nginx的环境、配置、运行文件全部都在这个文件系统中，包括我们要修改的html文件
   - 查看DockerHub网站中的nginx页面，可以知道nginx的html目录位置在`/usr/share/nginx/html`
   - 我们执行命令进入到该目录

   ```
   bash
   cd /usr/share/nginx/html
   ```

   查看目录下的文件

   ```
   bash
   root@310016c9b413:/usr/share/nginx/html## ls
   50x.html  index.html
   ```

2. 修改index.html的内容

   - 容器内没有vi命令，无法直接修改，我们使用下面的命令来修改

   ```
   bash
   sed -i -e 's#Welcome to nginx#Welcome To My Blog#g' index.html
   ```

3. 在浏览器访问自己的虚拟机ip:80，即可看到结果（80端口可以不写）

### 小结

- `docker run`命令常见的参数有哪些？
  - `--name`：指定容器名称
  - `-p`：指定端口映射
  - `-d`：让容器后台运行
- 查看容器日志的命令
  - `docker logs`
  - 添加`-f`参数可以持续查看日志
- 查看容器状态：
  - `docker ps`
  - `docker ps -a` 查看所有容器，包括已停止的

现在是不是感觉修改文件好麻烦，因为没给提供vi命令，不能直接编辑，所以这就要用到我们下面说的数据卷了



## 仓库（Repository）

**定义**：集中存储镜像的场所，分为 **公有仓库**（如 Docker Hub）和 **私有仓库**（如 Harbor）。

作用：

- **共享镜像**：开发者可上传或下载镜像。
- **版本管理**：通过标签（Tag）区分不同版本（如 `nginx:1.21` 和 `nginx:latest`）。



## 数据卷

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

- 在之前的nginx案例中，修改nginx的html页面时，需要进入nginx内部。并且因为没有编译器，修改文件也很麻烦，这就是容器与数据（容器内文件）耦合带来的后果，如果我们另外运行一台新的nginx容器，那么这台新的nginx容器也不能直接使用我们修改好的html文件，具有很多缺点
  1. 不便于修改：当我们要修改nginx的html内容时，需要进入容器内部修改，很不方便
  2. 数据不可服用：由于容器内的修改对外是不可见的，所有的修改对新创建的容器也是不可复用的
  3. 升级维护困难：数据在容器内，如果要升级容器必然删除旧容器，那么旧容器中的所有数据也跟着被删除了（包括改好的html页面）
- 要解决这个问题，必须将数据和容器解耦，这就要用到数据卷了

- 数据卷（volume）是一个虚拟目录，指向宿主机文件系统中的某个目录
- 一旦完成数据卷挂载，对容器的一切操作都会作用在对应的宿主机目录了。这样我们操作宿主机的/var/lib/docker/volumes/html目录，就等同于操作容器内的/usr/share/nginx/html目录了

### 数据集操作命令

- 数据卷操作的基本语法如下

```
bash
docker volume [COMMAND]
```

- ```
  docker volume
  ```

  命令是数据卷操作，根据命令后跟随的

  ```
  command
  ```

  来确定下一步的操作

  - `create`：创建一个volume
  - `inspect`：显示一个或多个volume的信息
  - `ls`：列出所有的volume
  - `prune`：删除未使用的volume
  - `rm`：删除一个或多个指定的volume

### 创建和查看数据集

需求：创建一个数据卷，并查看数据卷在宿主机的目录位置

1. 创建数据卷

```
bash
docker volume create html
```

1. 查看所有数据

```
bash
docker volume ls
```

结果

```
bash
[root@localhost ~]## docker volume ls
DRIVER    VOLUME NAME
local     html
```

1. 查看数据卷详细信息卷

```
bash
docker volume inspect html
```

结果

```
bash

[root@localhost ~]## docker volume inspect html
[
    {
        "CreatedAt": "2022-12-19T12:51:54+08:00",
        "Driver": "local",
        "Labels": {},
        "Mountpoint": "/var/lib/docker/volumes/html/_data",
        "Name": "html",
        "Options": {},
        "Scope": "local"
    }
]
```

可以看到我们创建的html这个数据卷关联的宿主机目录为`/var/lib/docker/volumes/html/_data`

- 小结：
  - 数据卷的作用
    - 将容器与数据分离，解耦合，方便操作容器内数据，保证数据安全
  - 数据卷操作：
    - `docker volume create`：创建数据卷
    - `docker volume ls`：查看所有数据卷
    - `docker volume inspect`：查看数据卷详细信息，包括关联的宿主机目录位置
    - `docker volume rm`：删除指定数据卷
    - `rocker volume prune`：删除所有未使用的数据卷

### 挂载数据卷

本地目录挂载

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

- 我们在创建容器时，可以通过-v参数来挂载一个数据卷到某个容器内目录，命令格式如下

```
docker run \
    -- name myNginx \
    -v html:/root/html \
    -p 8080:80 \
    nginx \
```

- 这里的-v就是挂载数据卷的命令
  - `-v html:/root/html`：把html数据卷挂载到容器内的/root/html这个目录中

### 案例一

需求：创建一个nginx容器，修改容器内的`html`目录的`index.html`内容
分析：上个案例中，我们进入nginx容器内部，已经知道了nginx的html目录所在位置`/usr/share/nginx/html`，我们需要把这个目录挂载到`html`这个数据卷上，方便操作其中的内容
提示：运行容器时，使用`-v`参数挂载数据卷

1. 创建容器并挂载数据卷到容器内的HTML目录

```
bash
docker run --name myNginx -v html:/usr/share/nginx/html -p 80:80 -d nginx
```

1. 进入html数据卷所在位置，并修改HTML内容

```
bash
## 查看数据卷位置
docker volume inspect html
## 进入该目录
cd /var/lib/docker/volumes/html/_data
## 修改文件
vi index.html
## 也可以在FinalShell中使用外部编译器（例如VSCode）来修改文件
```

### 案例二

- 容器不仅仅可以挂载数据卷，也可以直接挂载到宿主机目录上，关系如下
  - 带数据卷模式：宿主机目录 --> 数据卷 --> 容器内目录
  - 直接挂载模式：宿主机目录 --> 容器内目录
    [![img](data:image/gif;base64,R0lGODlhAQABAIAAAAAAAP///yH5BAEAAAAALAAAAAABAAEAAAIBRAA7)](https://pic.imgdb.cn/item/63a015ceb1fccdcd36544958.jpg)
- 目录挂载和数据卷挂载的语法是类似的
  - -v [宿主机目录]:[容器内目录]
  - -v [宿主机文件]:[容器内文件]

需求：创建并运行一个MySQL容器，将宿主机目录直接挂载到容器

1. 从DockerHub中拉取一个MySQL的镜像

```
bash
docker pull mysql
```

1. 创建目录`/tmp/mysql/data`

```
bash
mkdir -p /tmp/mysql/data
```

1. 创建目录`/tmp/mysql/conf`，将`myCnf.cnf`文件上传到`/tmp/mysql/conf`

- 命令
- myCnf.cnf

```
bash
mkdir -p /tmp/mysql/conf
```



1. 去DockerHub中查阅资料，找到mysql容器内的conf目录和data目录的位置
   容器中conf目录的位置是：`/etc/mysql/conf.d`
   容器中存储数据的目录为：`/var/lib/mysql`

2. 创建并运行MySQL容器，要求

   - 挂载/tmp/mysql/data到mysql容器内数据存储目录
   - 挂载/tmp/mysql/conf/myCnf.cnf到mysql容器的配置文件
   - 设置MySQL密码

   ```
   bash
   docker run \
   --name mysql \ 
   -e MYSQL_ROOT_PASSWORD=root \
   -v /tmp/mysql/conf:/etc/mysql/conf.d \
   -v /tmp/mysql/data:/var/lib/mysql \
   -p 3306:3306 \
   -d \
   mysql
   ```

3. 尝试使用Navicat连接数据库，注意自己设置的密码

### 小结

- `docker run`的命令中通过-v参数挂载文件或目录到容器中
  - `-v [volume名称]:[容器内目录]`
  - `-v [宿主机文件]:[容器内文件]`
  - `-v [宿主机目录]:[容器内目录]`
- 数据卷挂载与目录直接挂载的区别
  - 数据卷挂载耦合度低，由docker来管理目录，但是目录较深，不好找
  - 目录挂载耦合度高，需要我们自己管理目录，不过目录容易寻找查看



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







# Dockerfile自定义镜像

- 常见的镜像在DockerHub就能找到，但是我们自己写的项目就必须构建镜像了。而要自定义镜像，则必须先连接镜像的结构才行。

### 镜像结构

- 镜像是将应用程序及其需要的系统函数库，环境、配置、依赖打包而成
- 以MySQL为例，来看看它的镜像组成结构
- 简单来说，镜像就是在系统函数库、运行环境的基础上，添加应用程序文件、配置文件、依赖文件等组合，然后编写好启动脚本打包在一起形成的文件
- 我们要构建镜像，其实就是实现上述打包的过程

	每一次操作其实都是在生产一些文件（系统运行环境、函数库、配置最终都是磁盘文件），所以镜像就是一堆文件的集合。
	镜像按照操作的步骤分层叠加而成，每一层形成的文件都会单独打包并标记一个唯一id，称为层
	
	镜像结构
	    入口Entrypoint：镜像运行入口，一般是程序启动的脚本和参数
	    层Layer：添加安装包、依赖、配置等，每次操作都形成新的一层。
	    基础镜像BaseImage：应用依赖的系统函数库、环境、配置、文件等

### Dockerfile语法

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

- 构建自定义镜像时，并不需要一个个文件去拷贝，打包。
- 我们只需要告诉Docker我们的镜像组成，需要哪些BaseImage、需要拷贝什么文件、需要安装什么依赖、启动脚本是什么，将来Docker会帮助我们构建镜像
- 而描述上述信息的就是Dockerfile文件。
- Dockerfile就是一个文本文件，其中包含一个个指令(Instruction)，用指令说明要执行什么操作来构建镜像，每一个指令都会形成一层Layer。

|    指令    |                     说明                     |           示例            |
| :--------: | :------------------------------------------: | :-----------------------: |
|    FROM    |                 指定基础镜像                 |       FROM centos:6       |
|    ENV     |        设置环境变量，可在后面指令使用        |       ENV key value       |
|    COPY    |         拷贝本地文件到镜像的指定目录         | COPY ./mysql-5.7.rpm /tmp |
|    RUN     |  执行Linux的shell命令，一般是安装过程的命令  |    RUN yum install gcc    |
|   EXPOSE   | 指定容器运行时监听的端口，是给镜像使用者看的 |        EXPOSE 8080        |
| ENTRYPOINT |     镜像中应用的启动命令，容器运行时调用     | ENTRYPOINTjava -jar xxjar |

示例

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

### 构建Java项目

#### 基于Ubuntu构建Java项目

需求：基于Ubuntu镜像构建一个新镜像，运行一个Java项目

1. 创建一个空文件夹docker-demo

```
bash
mkdir /tmp/docker-demo
```

1. 将docker-demo.jar文件拷贝到docker-demo这个目录
2. 拷贝jdk8.tar.gz文件到docker-demo这个目录
3. 在docker-demo目录下新建Dockerfile，并写入以下内容

```
bash

## 指定基础镜像
FROM ubuntu:16.04

## 配置环境变量，JDK的安装目录
ENV JAVA_DIR=/usr/local

## 拷贝jdk的到JAVA_DIR目录下
COPY ./jdk8.tar.gz $JAVA_DIR/

## 安装JDK
RUN cd $JAVA_DIR && tar -xf ./jdk8.tar.gz && mv ./jdk1.8.0_44 ./java8

## 配置环境变量
ENV JAVA_HOME=$JAVA_DIR/java8
ENV PATH=$PATH:$JAVA_HOME/bin

## 拷贝java项目的包到指定目录下，我这里是/tmp/app.jar
COPY ./docker-demo.jar /tmp/app.jar

## 暴露端口，注意这里是8090端口，如果你之前没有关闭防火墙，请关闭防火墙或打开对应端口，云服务器同理
EXPOSE 8090

## 入口，java项目的启动命令
ENTERPOINT java -jar /tmp/app.jar
```

1. 在docker-demo目录下使用`docker build`命令构建镜像

```
bash
docker build -t docker_demo:1.0 .
```

1. 使用docker images命令，查看镜像

```
bash
[root@localhost docker-demo]## docker images
REPOSITORY    TAG       IMAGE ID       CREATED              SIZE
docker_demo   1.0       c8acd2dd02cf   About a minute ago   722MB
redis         latest    29ab4501eac3   2 days ago           117MB
nginx         latest    3964ce7b8458   5 days ago           142MB
ubuntu        16.04     b6f507652425   15 months ago        135MB
mysql         5.7.25    98455b9624a9   3 years ago          372MB
```

1. 创建并运行一个docker_demo容器

```
bash
docker run --name testDemo -p 8090:8090 -d docker_demo:1.0
```

1. 浏览器访问http://192.168.128.130:8090/hello/count，即可看到页面效果(注意修改虚拟机ip)
   [![img](data:image/gif;base64,R0lGODlhAQABAIAAAAAAAP///yH5BAEAAAAALAAAAAABAAEAAAIBRAA7)](https://pic.imgdb.cn/item/63a070d2b1fccdcd360c9ad5.jpg)

#### 基于Java8构建Java项目

- 虽然我们可以基于Ubuntu基础镜像，添加任意自己需要的安装包来构建镜像，但是却比较麻烦。所以大多数情况下，我们都可以在一些安装了部分软件的基础镜像上做改造。
- 我们刚刚构建的Java项目有一个固定的死步骤，那就是安装JDK并配置环境变量，我们每次构建Java项目的镜像的时候，都需要完成这个步骤，所以我们可以找一个已经安装好了JDK的基础镜像，然后在其基础上来构建我们的Java项目的镜像

需求：基于java:8-alpine镜像，将一个Java项目构建为镜像

1. 新建一个空目录(或者继续使用`/tmp/docker-demo`目录)
2. 将docker-demo.jar复制到该目录下(继续使用刚刚的目录就不用管)
3. 在目录中新建一个文件，命名为Dockerfile，并编写该文件(修改为如下样子就好)

```
bash
## 将openjdk:8作为基础镜像
FROM openjdk:8
## 拷贝java项目的包到指定目录下，我这里是/tmp/app.jar
COPY ./docker-demo.jar /tmp/app.jar
## 暴露端口
EXPOSE 8090
## 入口
ENTRYPOINT java -jar /tmp/app.jar
```

1. 构建镜像

```
bash
docker build -t docker_demo:2.0 .
```

1. 创建并运行一个docker_demo容器(在此之前停止之前的docker_demo容器)

```
bash
docker run --name testDemo02 -p 8090:8090 -d docker_demo:2.0
```

1. 浏览器访问http://192.168.128.130:8090/hello/count，即可看到页面效果

### 小结

1. Dockerfile本质就是一个文件，通过指令描述镜像的构建过程
2. Dockerfile的第一行必须是FROM，从一个基础镜像来构建
3. 基础镜像可以使基本操作系统，如Ubunut，也可以是其他人制作好的镜像，例如openjdk:8

# Docker-Compose

**Docker Compose** 是 Docker 的一个子项目，用于定义和运行多容器 Docker 应用程序。通过一个 YAML 文件（`docker-compose.yml`）配置应用程序的多个服务及其依赖关系，然后使用一条命令启动、停止或管理整个应用栈。

## **核心功能**
1. **服务定义**  
   - 在 `docker-compose.yml` 中定义多个服务（如 Web 服务、数据库、缓存等）。
   - 每个服务可指定镜像、启动命令、环境变量、端口映射、卷挂载等。
   - 示例：  
     ```yaml
     services:
       web:
         image: nginx:latest
         ports:
           - "80:80"
       db:
         image: mysql:5.7
         environment:
           MYSQL_ROOT_PASSWORD: example
     ```

2. **依赖管理**  
   - 通过 `depends_on` 确保服务按顺序启动（如数据库先于 Web 服务启动）。
   - 示例：  
     ```yaml
     services:
       web:
         depends_on:
           - db
       db:
         image: mysql:5.7
     ```

3. **网络配置**  
   - 自动创建默认网络，服务可通过名称互相访问。
   - 可自定义网络并指定 IP 地址。
   - 示例：  
     ```yaml
     networks:
       app-network:
         driver: bridge
     services:
       web:
         networks:
           - app-network
     ```

4. **卷挂载**  
   - 挂载主机目录或 Docker 卷，实现数据持久化。
   - 示例：  
     ```yaml
     volumes:
       - ./data:/var/lib/mysql
     ```

## 使用流程
#### **1. 创建 `docker-compose.yml` 文件**
- 文件结构示例：
  ```yaml
  version: '3.8'  # Compose 文件版本
  services:
    web:
      image: nginx:latest
      ports:
        - "80:80"
      volumes:
        - ./html:/usr/share/nginx/html
    db:
      image: mysql:5.7
      environment:
        MYSQL_ROOT_PASSWORD: example
      volumes:
        - db-data:/var/lib/mysql
  volumes:
    db-data:  # 定义数据卷
  ```

#### **2. 启动服务**
```bash
# 启动所有服务（后台运行）
docker-compose up -d

# 实时查看日志
docker-compose logs -f
```

#### **3. 管理服务**
- **查看运行状态**：  
  ```bash
  docker-compose ps
  ```
- **停止服务**：  
  ```bash
  docker-compose stop
  ```
- **删除容器、网络和卷**：  
  ```bash
  docker-compose down
  ```

#### **4. 多环境配置**
- 使用 `-f` 参数指定不同环境的配置文件：
  ```bash
  # 开发环境
  docker-compose -f docker-compose.yml -f docker-compose.dev.yml up
  
  # 生产环境
  docker-compose -f docker-compose.yml -f docker-compose.prod.yml up -d
  ```

概述：Docker Compose通过一个单独的docker-compose.yml模板文件(YAML格式)来定义一组相关联的应用容器，帮助我们实现多个相互关联的Docker容器的快速部署。

作用：做项目或者集群的一键部署。

- Docker Compose可以基于Compose文件帮我们快速地部署分布式英语，而无需手动一个个创建和运行容器
- 真实企业项目开发中，可能有几十个，
- 相比较于k8s，它更适合单机部署，测试环境，小型的多容器编排管理

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

示例

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



## 安装DockerCompose

- 在Linux下使用命令下载

```
bash
## 安装
curl -L https://github.com/docker/compose/releases/download/1.23.1/docker-compose-`uname -s`-`uname -m` > /usr/local/bin/docker-compose
```

- 修改文件权限

```
bash
chmod +x /usr/local/bin/docker-compose
```

- Base自动补全命令

```
bash
curl -L https://raw.githubusercontent.com/docker/compose/1.29.1/contrib/completion/bash/docker-compose > /etc/bash_completion.d/docker-compose
```

如出现错误`Failed connect to raw.githubusercontent.com:443; Connection refused`，需要修改自己的hosts文件

```
bash
echo "199.232.68.133 raw.githubusercontent.com" >> /etc/hosts
```

如出现新错误`TCP connection reset by peer`，重复发起命令，多试几次

## 部署微服务集群

需求：将之前学习的cloud-demo微服务集群利用DockerCompose部署

- 实现思路
  1. 编写docker-compose文件
  2. 修改自己的cloud-demo项目，将其中的数据库、nacos地址，都重命名为docker-compose中的服务名
  3. 使用maven打包工具，将项目中的每个微服务都打包为app.jar（打包名与Dockerfile中一致即可）
  4. 将打包好的app.jar拷贝到cloud-demo中的每一个对应的子目录中，编写Dockerfile文件
  5. 将cloud-demo上传至虚拟机，利用`docker-compose up -d`来部署

#### compose文件

- 针对我们之前写的cloud-demo，来编写对应的docker-compose文件

```
yml

version: "3.2"

services:
  nacos:
    image: nacos/nacos-server
    environment:
      MDOE: standalone
    ports:
      - "8848:8848"
    mysql:
      image: mysql:5.7.25
      environment:
        MYSQL_ROOT_PASSWORD: root
      volumes:
        - "$PWD/mysql/data:/var/lib/mysql"  ## 这里的$PWD是执行linux命令，获取当前目录
        - "$PWD/mysql/conf:/etc/mysql/conf.d"
    userservice:
      build: ./user-service
    orderservice:
      build: ./order-service
    gateway:
      build: ./gateway
      poets:
        - "10010:10010"
```

- 其中包含了5个服务：
  1. nacos：作为注册中心和配置中心
     - image: nacos/nacos-server：基于nacos/nacos-server镜像构建
     - environment: 环境变量
       - MODE: standalone：单点模式启动
     - ports：端口映射，这里暴露了8848端口
  2. mysql：数据库
     - image: mysql5.7.25：基于5.7.25版本的MySQL镜像构建
     - environment：环境变量
       - MYSQL_ROOT_PASSWORD: root：设置数据库root账户密码为root
     - volumes：数据卷挂载，这里挂载了mysql的data和conf目录
  3. userservice：基于Dockerfile临时构建，userservice不需要暴露端口，网关才是微服务的入口，如果暴露了userservice的端口，那么网关的身份认证，权限校验就形同虚设了
  4. orderservice：基于Dockerfile临时构建，不需要暴露端口，理由同上
  5. gateway：基于Dockerfile临时构建，网关需要暴露端口，它是其他微服务的入口

#### 修改微服务配置

- 使用Docker Compose部署时，所有的服务之间都可以用服务名互相访问，那我们现在就需要修改我们cloud-demo中的yml配置文件，如下

- bootstrap.yml
- application.yml

```
yml
spring:
  cloud:
    nacos:
      ## server-addr: localhost:80 #Nacos地址
      server-addr: nacos:8848 ## 使用compose中的服务名来互相访问，用nacos替换localhost
      config:
        file-extension: yaml ## 文件后缀名
```



#### 打包

- 将我们修改好的代码打包，注意修改pom文件指定打包名为app

```
xml
<build>
    <!-- 服务打包的最终名称 -->
    <finalName>app</finalName>
    <plugins>
        <plugin>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-maven-plugin</artifactId>
        </plugin>
    </plugins>
</build>
```

- 之后使用maven工具打包

#### 拷贝jar包到部署目录，并编写Dockerfile文件

- gateway
- order-service
- user-service

```
yml
FROM openjdk:8
COPY ./app.jar /tmp/app.jar
ENTERPOINT java -jar /tmp/app.jar
```



- 最终的目录结构如下
  - cloud-demo
    - gateway
      - app.jar
      - Dockerfile.yml
    - order-service
      - app.jar
      - Dockerfile.yml
    - user-service
      - app.jar
      - Dockerfile.yml
    - mysql
      - data
      - conf
    - docker-compose.yml

#### 部署

- 将cloud-demo上传到虚拟机，进入目录，执行以下命令

```
bash
docker-compose up -d
```

- 启动之后查看日志，会发现日志中报错` com.alibaba.nacos.api.exception.NacosException: failed to req API:/nacos/v1/ns/instance/list after all servers([nacos:8848]) tried: java.net.ConnectException: Connection refused (Connection refused)`

```
bash
docker-compose logs -f
```

阿里巴巴nacos连接失败，其原因是userservice在nacos之前启动了，而nacos启动太慢了，userservice注册失败，而且也没有重试机制（等nacos启动完成后，重试注册，就可以避免这个问题）

- 所以建议nacos单独先启动，其他服务后启动，我这里的解决方案是重启另外三个服务
- 重启gateway userservice orderservice服务

```
bash
docker-compose restart gateway userservice orderserivce 
```

- 查看userservice启动日志，这次就不报错了

```
bash
docker-compose logs -f userservice
```

- 打开浏览器访问http://192.168.128.130:10010/user/1?authorization=admin， 也可以看到数据
  [![img](data:image/gif;base64,R0lGODlhAQABAIAAAAAAAP///yH5BAEAAAAALAAAAAABAAEAAAIBRAA7)](https://pic.imgdb.cn/item/63a31cebb1fccdcd36645016.jpg)

# Docker镜像仓库

- 搭建镜像仓库可以基于Docker官方提供的DockerRegistry来实现
- 官网地址：https://hub.docker.com/_/registry

### 搭建私有镜像仓库

- 我们自己编写的项目显然是不适合放到Docker的共有仓库的，所以需要我们搭建一个私服

#### 配置Docker信任地址

- 我们的私服采用的是http协议，默认不被Docker信任，所以需要做一个配置：

```
bash
## 打开要修改的文件
vi /etc/docker/daemon.json
## 添加内容：
"insecure-registries":["http://192.168.128.101:8080"]
## 重加载
systemctl daemon-reload
## 重启docker
systemctl restart docker
```

#### 带图形化界面版本

- 使用DockerCompose部署带有图象界面的DockerRegistry，命令如下：

```
yml

version: '3.0'
services:
  registry:
    image: registry
    volumes:
      - ./registry-data:/var/lib/registry
  ui:
    image: joxit/docker-registry-ui:static
    ports:
      - 8080:80
    environment:
      - REGISTRY_TITLE=Kyle's Blog私有仓库
      - REGISTRY_URL=http://registry:5000
    depends_on:
      - registry
```

- 随后打开浏览器访问http://192.168.128.130:8080/， 就能看到带图形化界面的镜像仓库了
  [![img](data:image/gif;base64,R0lGODlhAQABAIAAAAAAAP///yH5BAEAAAAALAAAAAABAAEAAAIBRAA7)](https://pic.imgdb.cn/item/63a3202cb1fccdcd366c4296.jpg)

### 推送、拉取镜像

- 推送镜像到私有镜像服务必须先tag，步骤如下

  1. 重新tag本地镜像，名称前缀为私有仓库的地址：192.168.128.130:8080/

  ```
  bash
  docker tag nginx:latest 192.168.128.130:8080/nginx:1.0
  ```

  1. 推送镜像

  ```
  bash
  docker push 192.168.128.130:8080/nginx:1.0
  ```

  ![img](data:image/gif;base64,R0lGODlhAQABAIAAAAAAAP///yH5BAEAAAAALAAAAAABAAEAAAIBRAA7)

  3. 拉取镜像

  ```
  bash
  docker pull 192.168.128.130:8080/nginx:1.0
  ```

## 总结

- 作为程序员我们应`怎样理解docker？`
  - 容器技术的`起源`
    - 假设公司正在秘密研发下一个“今日头条”APP，我们姑且称为明日头条，程序员自己从头到尾搭建了一套环境开始写代码，写完代码后程序员要把代码交给测试同学测试，这时测试同学开始从头到尾搭建这套环境，测试过程中出现问题程序员也不用担心，大可以一脸无辜的撒娇，“明明在人家的环境上可以运行的”。
    - 测试同学测完后终于可以上线了，这时运维同学又要重新从头到尾搭建这套环境，费了九牛二虎之力搭建好环境开始上线，糟糕，上线系统就崩溃了，这时心理素质好的程序员又可以施展演技了，“明明在人家的环境上可以运行的”。
    - 从整个过程可以看到，不但我们重复搭建了三套环境还要迫使程序员转行演员浪费表演才华，典型的浪费时间和效率，聪明的程序员是永远不会满足现状的，因此又到了程序员改变世界的时候了，容器技术应运而生。
    - 有的同学可能会说：“等等，先别改变世界，我们有虚拟机啊，VMware好用的飞起，先搭好一套虚拟机环境然后给测试和运维clone出来不就可以了吗？”
  - 在没有容器技术之前，这确实是一个好办法，只不过这个办法还`没有那么好。`
    - 先科普一下，现在云计算其底层的基石就是虚拟机技术，云计算厂商买回来一堆硬件搭建好数据中心后使用虚拟机技术就可以将硬件资源进行切分了，比如可以切分出100台虚拟机，这样就可以卖给很多用户了。
  - 那么这个办法`为什么不好`呢？
    - 因为操作系统`太笨重`了，操作系统运行起来是需要占用很多资源的，大家对此肯定深有体会，刚装好的系统还什么都没有部署，单纯的操作系统其磁盘占用至少几十G起步，内存要几个G起步。
    - 我们需要的只是一个简单的应用程序，不需要把内存浪费在对我们应用程序`无用`的操作系统上。
    - 此外，还有启动时间的问题，我们知道操作系统重启是非常慢的，因为操作系统要从头到尾把该检测的都检测了，该加载的都加载上，这个过程非常缓慢，动辄就得几分钟，所以操作系统终究还是太笨重了
- 那么有没有一种技术可以让我们获得虚拟机的好处又能克服这些缺点从而一举`实现鱼和熊掌的兼得`呢？
  - 答案是肯定的，这就是`容器技术。`
- `什么是容器`
  - 容器一词的英文是container，其实container还有集装箱的意思，集装箱绝对是商业史上了不起的一项发明，大大降低了海洋贸易运输成本。让我们来看看集装箱的好处：
    - 集装箱之间相互隔离
    - 长期反复使用
    - 快速装载和卸载
    - 规格标准，在港口和船上都可以摆放
  - 回到软件中的容器，其实容器和集装箱在概念上是很相似的。
  - 现代软件开发的一大目的就是隔离，应用程序在运行时相互独立互不干扰，这种隔离实现起来是很不容易的，其中一种解决方案就是上面提到的虚拟机技术，通过将应用程序部署在不同的虚拟机中从而实现隔离。
  - 但是虚拟机技术有上述提到的各种缺点，那么容器技术又怎么样呢？
  - 与虚拟机通过操作系统实现隔离不同，容器技术只隔离应用程序的运行时环境但容器之间可以共享同一个操作系统，这里的运行时环境指的是程序运行依赖的各种库以及配置。
  - 与操作系统动辄几G的内存占用相比，容器技术只需数M空间，因此我们可以在同样规格的硬件上大量部署容器，这是虚拟机所不能比拟的，而且不同于操作系统数分钟的启动时间容器几乎瞬时启动，容器技术为打包服务栈提供了一种更加高效的方式。
- `那么我们该怎么使用容器呢？`这就要讲到docker了。
  - 注意，容器是一种通用技术，docker只是其中的一种实现。
- `什么是docker`
  - docker是一个用Go语言实现的开源项目，可以让我们方便的创建和使用容器，docker将程序以及程序所有的依赖都打包到docker container，这样你的程序可以在任何环境都会有一致的表现，这里程序运行的依赖也就是容器就好比集装箱，容器所处的操作系统环境就好比货船或港口，程序的表现只和集装箱有关系(容器)，和集装箱放在哪个货船或者哪个港口(操作系统)没有关系。
  - 因此我们可以看到docker可以屏蔽环境差异，也就是说，只要你的程序打包到了docker中，那么无论运行在什么环境下程序的行为都是一致的，程序员再也无法施展表演才华了，不会再有“在我的环境上可以运行”，真正实现“build once, run everywhere”。
  - 此外docker的另一个好处就是快速部署，这是当前互联网公司最常见的一个应用场景，一个原因在于容器启动速度非常快，另一个原因在于只要确保一个容器中的程序正确运行，那么你就能确信无论在生产环境部署多少都能正确运行。
- `如何使用docker`
  - docker中有这样几个概念：
    - dockerfile
    - image
    - container
  - 实际上你可以简单的把image理解为可执行程序，container就是运行起来的进程。
  - 那么写程序需要源代码，那么“写”image就需要dockerfile，dockerfile就是image的源代码，docker就是"编译器"。
  - 因此我们只需要在dockerfile中指定需要哪些程序、依赖什么样的配置，之后把dockerfile交给“编译器”docker进行“编译”，也就是docker build命令，生成的可执行程序就是image，之后就可以运行这个image了，这就是docker run命令，image运行起来后就是docker container。
  - 具体的使用方法就不再这里赘述了，大家可以参考docker的官方文档，那里有更详细的讲解。
- `docker是如何工作的`
  - 实际上docker使用了常见的CS架构，也就是client-server模式，docker client负责处理用户输入的各种命令，比如docker build、docker run，真正工作的其实是server，也就是docker demon，值得注意的是，docker client和docker demon可以运行在同一台机器上。
- `接下来我们用几个命令来讲解一下docker的工作流程：`
  1. docker build
     - 当我们写完dockerfile交给docker“编译”时使用这个命令，那么client在接收到请求后转发给docker daemon，接着docker daemon根据dockerfile创建出“可执行程序”image。
  2. docker run
     - 有了“可执行程序”image后就可以运行程序了，接下来使用命令docker run，docker daemon接收到该命令后找到具体的image，然后加载到内存开始执行，image执行起来就是所谓的container。
  3. docker pull
     - 其实docker build和docker run是两个最核心的命令，会用这两个命令基本上docker就可以用起来了，剩下的就是一些补充。
     - 那么docker pull是什么意思呢？
     - 我们之前说过，docker中image的概念就类似于“可执行程序”，我们可以从哪里下载到别人写好的应用程序呢？很简单，那就是APP Store，即应用商店。与之类似，既然image也是一种“可执行程序”，那么有没有"Docker Image Store"呢？答案是肯定的，这就是Docker Hub，docker官方的“应用商店”，你可以在这里下载到别人编写好的image，这样你就不用自己编写dockerfile了。
     - docker registry 可以用来存放各种image，公共的可以供任何人下载image的仓库就是docker Hub。那么该怎么从Docker Hub中下载image呢，就是这里的docker pull命令了。
     - 因此，这个命令的实现也很简单，那就是用户通过docker client发送命令，docker daemon接收到命令后向docker registry发送image下载请求，下载后存放在本地，这样我们就可以使用image了。
- `最后，让我们来看一下docker的底层实现`。
  - docker基于Linux内核提供这样几项功能实现的：
    - NameSpace
      我们知道Linux中的PID、IPC、网络等资源是全局的，而NameSpace机制是一种资源隔离方案，在该机制下这些资源就不再是全局的了，而是属于某个特定的NameSpace，各个NameSpace下的资源互不干扰，这就使得每个NameSpace看上去就像一个独立的操作系统一样，但是只有NameSpace是不够。
    - Control groups
      虽然有了NameSpace技术可以实现资源隔离，但进程还是可以不受控的访问系统资源，比如CPU、内存、磁盘、网络等，为了控制容器中进程对资源的访问，Docker采用control groups技术(也就是cgroup)，有了cgroup就可以控制容器中进程对系统资源的消耗了，比如你可以限制某个容器使用内存的上限、可以在哪些CPU上运行等等。
    - 有了这两项技术，容器看起来就真的像是独立的操作系统了。
- `总结`
  - docker是目前非常流行的技术，很多公司都在生产环境中使用，但是docker依赖的底层技术实际上很早就已经出现了，现在以docker的形式重新焕发活力，并且能很好的解决面临的问题





# Docker 挂载

Docker 容器的默认存储是临时的，容器删除后数据会丢失。为了持久化数据，Docker 提供了 **挂载** 功能，将宿主机的目录或 Docker 管理的卷（Volume）与容器内的目录关联。

### Docker 挂载的三种类型

| 类型                       | 特点                                                         |
| -------------------------- | ------------------------------------------------------------ |
| **绑定挂载（Bind Mount）** | 直接将宿主机的目录或文件挂载到容器。适合开发调试，数据实时同步。 |
| **卷（Volume）**           | 由 Docker 管理的持久化存储，独立于容器生命周期，适合生产环境。 |
| **tmpfs**                  | 数据存储在内存中，容器停止后数据丢失，适合临时缓存。         |

| 挂载类型         | 适用场景             | 优点                    | 缺点                     |
| ---------------- | -------------------- | ----------------------- | ------------------------ |
| **绑定挂载**     | 开发调试、实时同步   | 直接操作宿主机文件      | 路径依赖宿主机，不可移植 |
| **卷（Volume）** | 生产环境、数据持久化 | Docker 管理，跨容器共享 | 需要额外管理卷           |
| **tmpfs**        | 临时缓存、敏感数据   | 数据不落盘，安全性高    | 容器停止后数据丢失       |

### 挂载的核心参数
- **`-v` 或 `--mount`**：用于指定挂载。
  
  - **绑定挂载**：
    ```bash
    docker run -v /host/path:/container/path ...
    ```
  - **卷挂载**：
    ```bash
    docker run -v my-volume:/container/path ...
    ```

---

### 挂载的典型场景
| 场景         | 挂载类型 | 说明                                     |
| ------------ | -------- | ---------------------------------------- |
| 数据持久化   | 卷       | MySQL 数据库、日志文件等。               |
| 配置文件管理 | 绑定挂载 | 实时更新 Nginx、MySQL 配置文件。         |
| 开发调试     | 绑定挂载 | 挂载代码目录，修改代码后容器内自动生效。 |
| 日志收集     | 绑定挂载 | 将日志目录挂载到宿主机，便于分析。       |

### **案例 1：Nginx 静态网页挂载**
#### **目标**：将宿主机的网页文件挂载到 Nginx 容器中，实现动态更新。

#### **步骤**：
1. **创建宿主机目录**：
   ```bash
   mkdir -p /home/user/nginx/html
   echo "Hello Docker!" > /home/user/nginx/html/index.html
   ```

2. **启动容器并挂载目录**：
   ```bash
   docker run -d \
     --name my-nginx \
     -p 80:80 \
     -v /home/user/nginx/html:/usr/share/nginx/html \
     nginx:latest
   ```
   - `-v`：将宿主机的 `/home/user/nginx/html` 挂载到容器的 `/usr/share/nginx/html`（Nginx 默认网页目录）。

3. **验证结果**：
   - 访问 `http://localhost`，应显示 "Hello Docker!"。
   - 修改宿主机的 `index.html` 内容，刷新网页即可看到更新。

---

### **案例 2：MySQL 数据持久化**
#### **目标**：将 MySQL 数据目录挂载到宿主机，确保容器删除后数据不丢失。

#### **步骤**：
1. **创建宿主机目录**：
   ```bash
   mkdir -p /home/user/mysql/data
   ```

2. **启动 MySQL 容器并挂载数据目录**：
   ```bash
   docker run -d \
     --name my-mysql \
     -e MYSQL_ROOT_PASSWORD=your_password \
     -p 3306:3306 \
     -v /home/user/mysql/data:/var/lib/mysql \
     mysql:8.0
   ```
   - `/var/lib/mysql` 是 MySQL 默认的数据存储目录。

3. **验证数据持久化**：
   - 进入容器执行 SQL 操作：
     ```bash
     docker exec -it my-mysql mysql -u root -p
     CREATE DATABASE test_db;
     ```
   - 删除容器后重新启动：
     ```bash
     docker run -d ...  # 使用相同挂载配置
     ```
   - 新容器中的 `test_db` 数据库仍存在。

---

### **案例 3：使用 Docker 卷管理数据**
#### **目标**：通过 Docker 管理的卷实现 MySQL 数据持久化。

#### **步骤**：
1. **创建 Docker 卷**：
   ```bash
   docker volume create mysql-volume
   ```

2. **启动 MySQL 容器并挂载卷**：
   ```bash
   docker run -d \
     --name my-mysql \
     -e MYSQL_ROOT_PASSWORD=your_password \
     -p 3306:3306 \
     -v mysql-volume:/var/lib/mysql \
     mysql:8.0
   ```

3. **查看卷信息**：
   ```bash
   docker volume inspect mysql-volume
   ```

---

**常见问题与注意事项**

**权限问题**

- **问题**：挂载后容器内无法写入数据。
- **解决**：
  - 确保宿主机目录的权限允许容器用户访问（如 `chmod 777 /host/path`）。
  - 使用 `--user` 参数指定容器内运行用户。

**挂载目录为空**

- **问题**：宿主机目录为空时，容器内的原有数据会被清空。
- **解决**：
  - 提前在宿主机目录中准备好需要的文件（如 MySQL 的配置文件）。
  - 或使用卷挂载（Docker 会自动复制容器内的数据到卷中）。

**挂载冲突**

- **问题**：挂载的目录与容器内已有文件冲突。
- **解决**：确保挂载的目录结构与容器内一致（如 Nginx 的配置文件路径）。
