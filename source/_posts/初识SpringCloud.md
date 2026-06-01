---
title: 初识SpringCloud
date: 2022-12-10 13:20:21
tags:
  - SpringCloud
  - Java
cover: https://pic.imgdb.cn/item/66ebeef6f21886ccc0d0a4ca.jpg
categories: 技术记录
description: 想想现在的分布式都变成实习的面试标准了，努力！
---

# Spring Cloud Alibaba 基础知识点笔记

> 适用目标：面向 Java 后端、JavaWeb、SpringBoot 基础之后的微服务入门与面试复习。  
> 整理原则：以国内企业更常见的 `Spring Cloud Alibaba` 技术栈为主线；过时或现在新项目较少使用的组件只做历史理解，不再作为重点展开。  

---

## 0. 学习路线与组件取舍

### 0.1 当前建议主线

学习 SpringCloud 基础时，不建议从一堆组件名称开始背，而应该围绕一个真实微服务系统要解决的问题来学：

| 问题 | 推荐重点学习组件 / 技术 | 说明 |
|---|---|---|
| 服务如何拆分 | 微服务设计原则、DDD 粗粒度思想 | 不要为了微服务而微服务 |
| 服务如何互相发现 | `Nacos Discovery` | 国内 `Spring Cloud Alibaba` 体系中最常见 |
| 服务如何远程调用 | `OpenFeign` + `Spring Cloud LoadBalancer` | 替代手写 `RestTemplate` 和旧版 `Ribbon` |
| 服务入口如何统一管理 | `Spring Cloud Gateway` | 替代旧版 `Zuul` |
| 服务雪崩如何治理 | `Sentinel` | 限流、熔断、降级、热点参数、系统保护 |
| 配置如何集中管理 | `Nacos Config` | 支持配置中心、动态刷新、环境隔离 |
| 跨服务事务如何处理 | `Seata`、最终一致性、消息事务 | 不是所有场景都应该上分布式事务 |
| 异步削峰如何做 | `RocketMQ` / `Kafka` / `RabbitMQ` | SpringCloud 基础中了解即可，项目中很常见 |
| 问题如何定位 | 日志、链路追踪、指标监控 | `traceId`、`SkyWalking`、`Micrometer`、`Prometheus` |
| 如何上线部署 | Docker、Kubernetes、Nginx、灰度发布 | 微服务落地必须理解部署形态 |

### 0.2 不建议作为主线深入的旧组件

| 旧组件 | 现在定位 | 推荐替代 |
|---|---|---|
| `Eureka` | 老项目可能还在用，新项目尤其国内 Alibaba 体系较少作为首选 | `Nacos` |
| `Ribbon` | 旧版客户端负载均衡组件，已经不是新版本主线 | `Spring Cloud LoadBalancer` |
| `Hystrix` | 老熔断组件，维护状态，不建议新项目使用 | `Sentinel` / `Resilience4j` |
| `Zuul` | 老网关组件，阻塞模型，课程中常见但新项目少作为首选 | `Spring Cloud Gateway` |
| `Spring Cloud Config` | 非 Alibaba 技术栈仍可能使用，但国内常被 `Nacos Config` 替代 | `Nacos Config` |
| `Spring Cloud Sleuth` | 老链路追踪方案，新版 Spring 体系逐步转向 `Micrometer Tracing` | `Micrometer Tracing` / `SkyWalking` |

### 0.3 需要知道但不必在基础阶段过度展开的组件

| 组件 | 基础阶段建议 |
|---|---|
| `Dubbo` | 如果公司用 RPC 或电商中台较多，需要学；普通 SpringCloud REST 微服务先不用深入 |
| `RocketMQ` | 建议掌握消息队列基本思想、削峰、异步、最终一致性，深入可以后续单独学 |
| `SchedulerX` | 偏阿里云生态，非通用开源基础，基础笔记不作为重点 |
| `Sidecar` | 跨语言接入场景才需要，Java 后端基础阶段了解即可 |
| `Consul` / `Zookeeper` | 了解注册中心历史和可选方案即可，国内 Java SpringCloud Alibaba 主线优先 `Nacos` |

---

## 1. 微服务架构基础

### 1.1 单体架构、分布式架构、微服务架构

#### 单体架构

`单体架构` 是把系统所有功能都放在一个项目中开发、打包、部署。

**优点：**

- 架构简单，开发初期效率高。
- 本地调试方便。
- 部署成本低，一个应用包即可启动。
- 适合小型系统、后台管理系统、课程练习项目。

**缺点：**

- 代码体积越来越大，模块之间耦合严重。
- 一个小功能修改也可能影响整个系统。
- 部署粒度粗，无法单独扩容某一个高流量模块。
- 多团队协作时容易互相影响。
- 启动慢、测试慢、发布风险大。

#### 分布式架构

`分布式架构` 是将系统拆分成多个独立服务，不同服务可以部署在不同机器上，通过网络进行调用。

**优点：**

- 降低模块耦合。
- 可以按业务模块独立开发、部署、扩容。
- 更适合大型系统、多团队协作。

**缺点：**

- 服务之间调用关系复杂。
- 网络调用会引入延迟、超时、失败、重试等问题。
- 数据一致性变复杂。
- 运维、监控、排查问题难度明显上升。

#### 微服务架构

`微服务架构` 可以理解为一种更规范、更细粒度、更强调自治能力的分布式架构。

微服务通常具备以下特征：

- **单一职责**：一个服务只负责一个清晰的业务能力。
- **独立部署**：每个服务可以单独构建、发布、回滚。
- **独立数据**：每个服务拥有自己的数据库或数据表边界，不直接访问其他服务数据库。
- **服务自治**：服务内部技术实现可以独立演进。
- **接口通信**：服务之间通过接口调用，例如 `HTTP REST`、`RPC`、`消息队列`。
- **容错治理**：必须考虑限流、熔断、降级、重试、超时、隔离。
- **可观测性**：必须有日志、指标、链路追踪，否则故障很难定位。

### 1.2 微服务不是越拆越好

很多初学者容易误解：服务拆得越多越微服务。实际不是这样。

拆分过细会带来：

- 接口数量暴增。
- 网络调用链路变长。
- 事务和一致性更难处理。
- 部署和监控复杂度提升。
- 本来一个本地方法调用变成一次远程调用，性能和可靠性都下降。

基础阶段可以遵循：

1. 先按业务边界拆，例如 `用户服务`、`订单服务`、`商品服务`、`支付服务`。
2. 不同服务不要直接访问彼此数据库。
3. 服务之间通过接口或消息通信。
4. 高频强一致的逻辑不要轻易拆开。
5. 服务拆分要考虑团队规模、业务复杂度和部署能力。

---

## 2. SpringCloud 与 Spring Cloud Alibaba

### 2.1 SpringCloud 是什么

`Spring Cloud` 是 Spring 体系中用于构建分布式系统和微服务系统的一组工具集合。

它不是一个单独框架，而是将微服务中常见的问题抽象出来，并提供统一的编程模型：

- 配置管理
- 服务注册与发现
- 服务调用
- 负载均衡
- 网关路由
- 熔断限流
- 消息总线
- 链路追踪
- 分布式事务集成

### 2.2 Spring Cloud Alibaba 是什么

`Spring Cloud Alibaba` 是 Alibaba 围绕 SpringCloud 生态提供的一套微服务解决方案，常见组合是：

- `Nacos`：注册中心 + 配置中心。
- `Sentinel`：流量控制、熔断降级、系统保护。
- `Seata`：分布式事务。
- `RocketMQ`：消息队列相关集成。
- `Spring Cloud Gateway`：网关，一般仍使用 Spring 官方 Gateway，而不是 Alibaba 自己单独实现一个网关。

### 2.3 推荐技术栈组合

#### 学习 / 中小项目版本

```text
Spring Boot
Spring Cloud
Spring Cloud Alibaba
Nacos
OpenFeign
Spring Cloud LoadBalancer
Spring Cloud Gateway
Sentinel
MyBatis / MyBatis-Plus
Redis
RocketMQ / RabbitMQ / Kafka
Docker
```

#### 国内企业常见组合

```text
Spring Boot + Spring Cloud Alibaba
Nacos 注册中心 / 配置中心
OpenFeign 服务调用
Gateway 统一网关
Sentinel 限流熔断
Seata 或 MQ 最终一致性
Redis 缓存
RocketMQ 消息队列
SkyWalking 链路追踪
Prometheus + Grafana 指标监控
ELK / Loki 日志系统
Docker / Kubernetes 部署
```

### 2.4 版本兼容原则

SpringCloud 生态最容易踩坑的地方是 **版本不兼容**。

基本原则：

- `Spring Boot`、`Spring Cloud`、`Spring Cloud Alibaba` 必须使用兼容版本。
- 不要随便复制网上不同年代的 `pom.xml`。
- 老课程常见 `Hoxton.SR10 + Spring Boot 2.3.x + Spring Cloud Alibaba 2.2.x`，现在更适合当作旧项目理解。
- 新项目要优先查官方兼容表，特别是 `Boot 3.x`、`Java 17`、`Spring Cloud 2023/2024/2025` 之后的写法变化。
- `bootstrap.yml` 在老版本常见；新版更推荐用 `spring.config.import` 导入外部配置。

一个父工程依赖管理模板如下，版本号根据公司项目或官方兼容表填写：

```xml
<dependencyManagement>
    <dependencies>
        <!-- Spring Boot 依赖版本一般由 spring-boot-starter-parent 管理 -->

        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-dependencies</artifactId>
            <version>${spring-cloud.version}</version>
            <type>pom</type>
            <scope>import</scope>
        </dependency>

        <dependency>
            <groupId>com.alibaba.cloud</groupId>
            <artifactId>spring-cloud-alibaba-dependencies</artifactId>
            <version>${spring-cloud-alibaba.version}</version>
            <type>pom</type>
            <scope>import</scope>
        </dependency>
    </dependencies>
</dependencyManagement>
```

---

## 3. 微服务工程结构

### 3.1 聚合工程结构

一个典型的微服务学习项目可以这样组织：

```text
cloud-demo
├── pom.xml                         # 父工程，统一版本管理
├── common-core                     # 通用工具、统一返回、异常、常量
├── common-web                      # Web 通用配置、拦截器、过滤器
├── common-feign                    # Feign 通用配置
├── user-api                        # 用户服务对外暴露的 DTO / Feign Client
├── user-service                    # 用户服务实现
├── order-api                       # 订单服务对外暴露的 DTO / Feign Client
├── order-service                   # 订单服务实现
├── product-service                 # 商品服务
├── gateway-service                 # 网关服务
└── auth-service                    # 认证授权服务
```

### 3.2 API 模块和 Service 模块

很多项目会把 `api` 和 `service` 分开：

- `xxx-api`：只放对外暴露的 `DTO`、`FeignClient`、常量。
- `xxx-service`：服务真正实现，包括 `Controller`、`Service`、`Mapper`、数据库操作。

这样做的好处：

- 其他服务只依赖 `xxx-api`，不需要依赖完整服务实现。
- 降低模块耦合。
- 方便统一管理服务调用接口。

示例：

```text
user-api
├── dto
│   └── UserDTO.java
└── client
    └── UserClient.java

user-service
├── controller
├── service
├── mapper
└── domain
```

### 3.3 DTO、VO、Entity 的边界

| 类型 | 所属层 | 作用 |
|---|---|---|
| `Entity` / `DO` | 数据层 | 对应数据库表结构 |
| `DTO` | 服务调用层 | 服务之间传输数据 |
| `VO` | 表现层 | 返回给前端展示的数据 |
| `Query` / `Param` | 请求参数层 | 接收复杂查询条件 |

不要在微服务接口之间直接传 `Entity`，因为数据库字段变化会影响外部服务。

---

## 4. 服务拆分与远程调用

### 4.1 服务拆分原则

服务拆分可以遵循：

1. **业务边界优先**：按业务能力拆，而不是按技术层拆。
2. **数据独立**：一个服务不要直接访问其他服务数据库。
3. **接口隔离**：服务对外暴露明确接口，而不是共享内部实现。
4. **避免循环依赖**：`order-service` 调 `user-service`，`user-service` 又调 `order-service` 会带来复杂链路。
5. **先粗后细**：初期可以拆得粗一些，等业务边界稳定后再细化。
6. **强一致逻辑谨慎拆分**：必须一个事务完成的场景不要轻易拆成多个服务。

### 4.2 服务调用方式

常见服务调用方式：

| 调用方式 | 典型技术 | 特点 |
|---|---|---|
| 同步 HTTP 调用 | `OpenFeign`、`RestTemplate`、`WebClient` | 简单直观，但调用方会等待结果 |
| RPC 调用 | `Dubbo`、`gRPC` | 性能较好，接口约束强 |
| 异步消息 | `RocketMQ`、`Kafka`、`RabbitMQ` | 解耦、削峰、最终一致性，但链路更复杂 |
| 事件驱动 | 领域事件、消息总线 | 适合状态变更通知 |

### 4.3 RestTemplate 的定位

老笔记里用 `RestTemplate` 演示服务远程调用，这对理解原理很有帮助：

```java
String url = "http://localhost:8081/user/" + order.getUserId();
User user = restTemplate.getForObject(url, User.class);
```

但实际项目中不建议大量手写这种调用方式，因为：

- URL 写死，不利于服务发现。
- 代码重复。
- 请求头、超时、重试、日志不好统一处理。
- 接口变更不容易维护。

现在更推荐：

```java
@FeignClient(name = "user-service", path = "/users")
public interface UserClient {

    @GetMapping("/{id}")
    UserDTO getById(@PathVariable("id") Long id);
}
```

---

## 5. Nacos 注册中心

### 5.1 Nacos 的作用

`Nacos` 全称可以理解为 `Dynamic Naming and Configuration Service`，核心能力包括：

- 服务注册
- 服务发现
- 服务健康检查
- 服务元数据管理
- 配置中心
- 配置动态刷新
- 命名空间隔离

在国内 Spring Cloud Alibaba 技术栈中，`Nacos` 往往同时承担：

```text
注册中心 + 配置中心
```

### 5.2 为什么需要注册中心

如果没有注册中心，服务调用可能写死地址：

```text
http://127.0.0.1:8081/users/1
```

问题：

- 服务扩容后有多个实例，不知道调用哪个。
- 某个实例宕机后，调用方仍可能请求失败实例。
- IP 和端口变化需要改配置甚至重新发版。
- 多环境、多机房管理困难。

注册中心解决：

1. 服务启动时把自己的地址注册到 `Nacos`。
2. 调用方根据服务名从 `Nacos` 拉取实例列表。
3. 负载均衡器选择一个实例。
4. 服务下线或异常时，注册中心更新实例状态。

### 5.3 启动 Nacos

开发环境通常可以单机启动：

```bash
startup.cmd -m standalone
```

Linux / macOS：

```bash
sh startup.sh -m standalone
```

默认控制台：

```text
http://localhost:8848/nacos
```

默认账号密码通常是：

```text
nacos / nacos
```

生产环境注意：

- 不要使用默认密码。
- 不要把 Nacos 控制台直接暴露到公网。
- 建议集群部署。
- 配置数据建议持久化到 MySQL。
- 重要配置要有权限控制和审计。

### 5.4 服务注册依赖

在服务中加入：

```xml
<dependency>
    <groupId>com.alibaba.cloud</groupId>
    <artifactId>spring-cloud-starter-alibaba-nacos-discovery</artifactId>
</dependency>
```

配置：

```yaml
spring:
  application:
    name: user-service
  cloud:
    nacos:
      discovery:
        server-addr: 127.0.0.1:8848
```

启动后，服务会注册到 Nacos。

### 5.5 服务发现与调用

通过服务名调用：

```text
http://user-service/users/1
```

如果使用 `RestTemplate`，需要开启负载均衡：

```java
@Bean
@LoadBalanced
public RestTemplate restTemplate() {
    return new RestTemplate();
}
```

但实际项目更推荐 `OpenFeign`，后面单独讲。

### 5.6 Nacos 服务分级模型

Nacos 的服务模型可以理解为：

```text
Namespace
└── Group
    └── Service
        └── Cluster
            └── Instance
```

常用概念：

| 概念 | 说明 | 常见用法 |
|---|---|---|
| `Namespace` | 命名空间 | 隔离开发、测试、生产环境 |
| `Group` | 分组 | 区分业务线或配置组 |
| `Service` | 服务名 | 例如 `user-service` |
| `Cluster` | 集群 | 例如 `HZ`、`SH`，表示杭州机房、上海机房 |
| `Instance` | 服务实例 | 某个具体 IP + 端口 |

配置集群：

```yaml
spring:
  cloud:
    nacos:
      discovery:
        server-addr: 127.0.0.1:8848
        cluster-name: HZ
```

配置命名空间：

```yaml
spring:
  cloud:
    nacos:
      discovery:
        namespace: dev-namespace-id
```

### 5.7 临时实例与非临时实例

Nacos 实例分为：

| 类型 | 配置 | 特点 |
|---|---|---|
| 临时实例 | `ephemeral: true` | 默认方式，心跳异常会被剔除 |
| 非临时实例 | `ephemeral: false` | 实例异常不会直接删除，适合少数固定服务 |

```yaml
spring:
  cloud:
    nacos:
      discovery:
        ephemeral: true
```

一般业务微服务保持默认临时实例即可。

### 5.8 Nacos 与 Eureka 的区别

| 对比项 | Nacos | Eureka |
|---|---|---|
| 注册发现 | 支持 | 支持 |
| 配置中心 | 内置支持 | 不支持，需要搭配其他组件 |
| 服务推送 | 支持服务变更推送 | 主要依赖客户端定时拉取 |
| 实例类型 | 临时实例、非临时实例 | 主要是临时心跳模型 |
| 集群模型 | 支持 `namespace/group/cluster` | 模型相对简单 |
| 国内使用 | Spring Cloud Alibaba 场景常见 | 老项目更常见 |

---

## 6. Spring Cloud LoadBalancer

### 6.1 为什么替代 Ribbon

`Ribbon` 是老版 Spring Cloud Netflix 体系中的客户端负载均衡组件。新版本 Spring Cloud 更推荐使用 `Spring Cloud LoadBalancer`。

客户端负载均衡的核心思想：

1. 根据服务名获取服务实例列表。
2. 从实例列表中选择一个实例。
3. 把服务名替换成真实 IP + 端口。
4. 发起请求。

例如：

```text
http://user-service/users/1
```

经过负载均衡后可能变成：

```text
http://192.168.1.10:8081/users/1
```

### 6.2 常见负载均衡策略

| 策略 | 说明 |
|---|---|
| 轮询 | 按顺序选择实例 |
| 随机 | 随机选择一个实例 |
| 权重 | 权重高的实例承接更多流量 |
| 同集群优先 | 优先调用同机房或同区域实例 |

### 6.3 实际项目注意点

- 默认策略通常够用，初学阶段不要急着自定义。
- 机房/地域部署时，需要考虑同集群优先。
- 权重调整可以用于灰度发布。
- 某实例权重设为 `0` 可以临时摘流。
- 负载均衡不能替代熔断降级，实例慢或异常还需要 `Sentinel` 等治理组件。

---

## 7. OpenFeign 远程调用

### 7.1 OpenFeign 的作用

`OpenFeign` 是声明式 HTTP 客户端，可以让远程调用像调用本地接口一样写。

不使用 Feign：

```java
String url = "http://user-service/users/" + userId;
UserDTO user = restTemplate.getForObject(url, UserDTO.class);
```

使用 Feign：

```java
UserDTO user = userClient.getById(userId);
```

### 7.2 引入依赖

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-openfeign</artifactId>
</dependency>
```

启动类开启：

```java
@SpringBootApplication
@EnableFeignClients(basePackages = "com.example")
public class OrderApplication {
    public static void main(String[] args) {
        SpringApplication.run(OrderApplication.class, args);
    }
}
```

### 7.3 编写 FeignClient

```java
@FeignClient(name = "user-service", path = "/users")
public interface UserClient {

    @GetMapping("/{id}")
    UserDTO getById(@PathVariable("id") Long id);

    @PostMapping
    Long create(@RequestBody UserCreateRequest request);
}
```

调用：

```java
@Service
public class OrderService {

    private final UserClient userClient;

    public OrderService(UserClient userClient) {
        this.userClient = userClient;
    }

    public OrderDTO getOrderDetail(Long orderId) {
        Order order = orderMapper.selectById(orderId);
        UserDTO user = userClient.getById(order.getUserId());
        return OrderDTO.of(order, user);
    }
}
```

### 7.4 Feign 的常用配置

```yaml
spring:
  cloud:
    openfeign:
      client:
        config:
          user-service:
            connectTimeout: 1000
            readTimeout: 3000
            loggerLevel: basic
```

日志级别：

| 级别 | 说明 |
|---|---|
| `NONE` | 不记录日志 |
| `BASIC` | 记录请求方法、URL、状态码、耗时 |
| `HEADERS` | 在 `BASIC` 基础上记录请求头、响应头 |
| `FULL` | 记录完整请求和响应，生产谨慎使用 |

### 7.5 Feign 最佳实践

#### 推荐做法：抽取 API 模块

```text
user-api
├── dto
│   └── UserDTO.java
└── client
    └── UserClient.java
```

消费者服务依赖：

```xml
<dependency>
    <groupId>com.example</groupId>
    <artifactId>user-api</artifactId>
    <version>${project.version}</version>
</dependency>
```

优点：

- DTO 和接口定义统一。
- 避免手写重复接口。
- 服务实现和服务调用解耦。

#### 不推荐：直接继承 Controller 接口

有些教程会把 `Controller` 实现某个接口，然后 Feign 也继承这个接口。这样虽然减少代码，但会让 Controller 层和客户端强耦合，实际项目中不够清晰。

### 7.6 Feign 常见坑

| 问题 | 原因 | 解决 |
|---|---|---|
| `No Feign Client for loadBalancing defined` | 缺少负载均衡依赖或版本不匹配 | 检查 `Spring Cloud LoadBalancer` 相关依赖 |
| `PathVariable annotation was empty` | `@PathVariable` 没写名字 | 写成 `@PathVariable("id")` |
| 请求体丢失 | GET 请求中错误使用 `@RequestBody` | GET 用 query 参数，POST/PUT 用 body |
| 超时 | 下游慢或超时配置太短 | 设置合理超时，配合 Sentinel 降级 |
| 循环调用 | 服务边界设计不合理 | 调整业务依赖方向或引入事件 |

---

## 8. Spring Cloud Gateway 服务网关

### 8.1 为什么需要网关

如果没有网关，前端可能直接访问多个服务：

```text
/user-service/users/1
/order-service/orders/1
/product-service/products/1
```

问题：

- 前端需要知道所有后端服务地址。
- 认证鉴权逻辑分散在各个服务。
- 跨域处理重复。
- 日志、限流、灰度、路由规则难统一。
- 服务地址暴露给外部，不安全。

`Gateway` 作为统一入口：

```text
客户端 -> Gateway -> 各个微服务
```

网关常见职责：

- 路由转发
- 统一认证鉴权
- 跨域处理
- 统一限流
- 请求/响应日志
- 灰度发布
- 黑白名单
- 请求头处理
- 协议适配

### 8.2 引入依赖

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-gateway</artifactId>
</dependency>
```

注意：`Spring Cloud Gateway` 基于响应式模型，传统 servlet 的 `spring-boot-starter-web` 不应和 Gateway 主应用混用，否则容易冲突。Gateway 服务通常单独建一个模块。

### 8.3 基本路由配置

```yaml
server:
  port: 10010

spring:
  application:
    name: gateway-service
  cloud:
    nacos:
      discovery:
        server-addr: 127.0.0.1:8848
    gateway:
      routes:
        - id: user-route
          uri: lb://user-service
          predicates:
            - Path=/api/users/**
          filters:
            - StripPrefix=1

        - id: order-route
          uri: lb://order-service
          predicates:
            - Path=/api/orders/**
          filters:
            - StripPrefix=1
```

说明：

- `id`：路由唯一标识。
- `uri`：目标服务地址，`lb://user-service` 表示根据服务名负载均衡。
- `predicates`：断言，判断请求是否匹配该路由。
- `filters`：过滤器，对请求或响应进行加工。
- `StripPrefix=1`：去掉路径前缀，例如 `/api/users/1` 转发为 `/users/1`。

### 8.4 常见断言工厂

| 断言 | 作用 |
|---|---|
| `Path` | 按请求路径匹配 |
| `Method` | 按请求方法匹配 |
| `Host` | 按域名匹配 |
| `Header` | 按请求头匹配 |
| `Query` | 按请求参数匹配 |
| `After` / `Before` / `Between` | 按时间匹配 |
| `Cookie` | 按 Cookie 匹配 |

示例：

```yaml
predicates:
  - Path=/api/orders/**
  - Method=GET,POST
```

### 8.5 常见过滤器

| 过滤器 | 作用 |
|---|---|
| `StripPrefix` | 去掉路径前缀 |
| `AddRequestHeader` | 添加请求头 |
| `RemoveRequestHeader` | 删除请求头 |
| `AddResponseHeader` | 添加响应头 |
| `RewritePath` | 重写路径 |
| `RequestRateLimiter` | 请求限流 |
| 自定义 `GlobalFilter` | 全局认证、日志、灰度等 |

### 8.6 自定义全局过滤器：JWT 鉴权示例

```java
@Component
public class AuthGlobalFilter implements GlobalFilter, Ordered {

    private static final List<String> WHITE_LIST = List.of(
            "/api/auth/login",
            "/api/auth/register"
    );

    @Override
    public Mono<Void> filter(ServerWebExchange exchange, GatewayFilterChain chain) {
        String path = exchange.getRequest().getURI().getPath();

        if (WHITE_LIST.contains(path)) {
            return chain.filter(exchange);
        }

        String token = exchange.getRequest()
                .getHeaders()
                .getFirst("Authorization");

        if (token == null || !token.startsWith("Bearer ")) {
            exchange.getResponse().setStatusCode(HttpStatus.UNAUTHORIZED);
            return exchange.getResponse().setComplete();
        }

        // 这里应该解析 JWT，校验签名、过期时间、用户权限等
        return chain.filter(exchange);
    }

    @Override
    public int getOrder() {
        return -1;
    }
}
```

### 8.7 Gateway 跨域配置

```yaml
spring:
  cloud:
    gateway:
      globalcors:
        add-to-simple-url-handler-mapping: true
        corsConfigurations:
          '[/**]':
            allowedOrigins:
              - "http://localhost:5173"
            allowedMethods:
              - GET
              - POST
              - PUT
              - DELETE
              - OPTIONS
            allowedHeaders: "*"
            allowCredentials: true
            maxAge: 3600
```

生产建议：

- `allowedOrigins` 不要随便写 `*`。
- 如果允许携带 Cookie，`allowCredentials=true` 时不能简单配 `*`。
- 后端服务内部一般不再重复配置 CORS，由网关统一处理。

### 8.8 Gateway 与 Nginx 的区别

| 对比项 | Nginx | Gateway |
|---|---|---|
| 层级 | 通用反向代理 / Web 服务器 | 应用层 API 网关 |
| 性能 | 高 | 相对低于 Nginx，但更灵活 |
| 业务逻辑 | 不适合写复杂 Java 逻辑 | 可写 Java 过滤器 |
| 服务发现 | 需要额外集成 | 可直接对接注册中心 |
| 鉴权 | 简单鉴权可以，复杂不方便 | 适合统一认证鉴权 |
| 使用方式 | 外层入口、静态资源、负载均衡 | 微服务内部 API 网关 |

实际项目中常见结构：

```text
用户 -> Nginx / SLB -> Gateway -> 微服务
```

---

## 9. Sentinel 限流、熔断、降级

### 9.1 为什么需要 Sentinel

微服务系统中，一个服务可能依赖多个下游服务。

如果下游服务变慢或宕机，上游服务线程会被大量阻塞，最终可能导致整个系统雪崩。

`Sentinel` 主要解决：

- 流量突增导致服务被打爆。
- 下游异常导致调用链雪崩。
- 热点参数被恶意或集中访问。
- 系统整体负载过高。
- 不同调用来源需要授权控制。

### 9.2 Sentinel 的核心概念

| 概念 | 说明 |
|---|---|
| 资源 | 被保护的对象，可以是接口、方法、服务调用 |
| 规则 | 对资源设置限流、降级、授权等规则 |
| 流控 | QPS 或并发线程数超过阈值时拒绝 |
| 熔断降级 | 下游异常比例、慢调用比例过高时快速失败 |
| 热点参数 | 对某些高频参数单独限流 |
| 系统保护 | 根据系统负载、CPU、线程数等整体保护 |
| 授权规则 | 按来源黑白名单控制访问 |

### 9.3 引入依赖

```xml
<dependency>
    <groupId>com.alibaba.cloud</groupId>
    <artifactId>spring-cloud-starter-alibaba-sentinel</artifactId>
</dependency>
```

配置 Dashboard：

```yaml
spring:
  cloud:
    sentinel:
      transport:
        dashboard: 127.0.0.1:8080
```

启动 Sentinel Dashboard 后，访问控制台即可看到实时资源。

### 9.4 Web 接口限流

假设接口：

```java
@RestController
@RequestMapping("/orders")
public class OrderController {

    @GetMapping("/{id}")
    public OrderDTO getById(@PathVariable Long id) {
        return orderService.getById(id);
    }
}
```

Sentinel 控制台中会出现资源：

```text
/orders/{id}
```

可以配置：

- QPS 阈值，例如 `10`。
- 超出后直接拒绝。
- 可配合统一异常处理返回友好提示。

### 9.5 `@SentinelResource` 方法保护

```java
@Service
public class UserQueryService {

    @SentinelResource(
            value = "queryUserById",
            blockHandler = "handleBlock",
            fallback = "handleFallback"
    )
    public UserDTO queryUserById(Long id) {
        if (id <= 0) {
            throw new IllegalArgumentException("用户ID不合法");
        }
        return remoteQuery(id);
    }

    public UserDTO handleBlock(Long id, BlockException ex) {
        return UserDTO.empty("访问过于频繁，请稍后重试");
    }

    public UserDTO handleFallback(Long id, Throwable throwable) {
        return UserDTO.empty("服务暂时不可用，请稍后重试");
    }
}
```

区别：

| 方法 | 触发原因 |
|---|---|
| `blockHandler` | 被 Sentinel 规则拦截，例如限流、熔断 |
| `fallback` | 方法本身抛出业务异常或运行异常 |

### 9.6 Feign 整合 Sentinel

配置：

```yaml
feign:
  sentinel:
    enabled: true
```

FeignClient：

```java
@FeignClient(
        name = "user-service",
        path = "/users",
        fallbackFactory = UserClientFallbackFactory.class
)
public interface UserClient {

    @GetMapping("/{id}")
    UserDTO getById(@PathVariable("id") Long id);
}
```

FallbackFactory：

```java
@Component
public class UserClientFallbackFactory implements FallbackFactory<UserClient> {

    @Override
    public UserClient create(Throwable cause) {
        return id -> UserDTO.empty("用户服务不可用：" + cause.getMessage());
    }
}
```

推荐使用 `fallbackFactory`，因为可以拿到异常原因。

### 9.7 常用规则

| 规则 | 作用 | 例子 |
|---|---|---|
| 流控规则 | 控制访问流量 | 每秒最多 100 个请求 |
| 熔断规则 | 下游不稳定时快速失败 | 慢调用比例超过 50% 熔断 |
| 热点参数规则 | 针对热点参数限流 | 商品 ID 为 1 的请求特别多 |
| 系统规则 | 按系统整体负载保护 | CPU 高于阈值限流 |
| 授权规则 | 黑白名单访问控制 | 只允许 gateway 调用 |

### 9.8 Sentinel 与 Hystrix 的区别

| 对比项 | Sentinel | Hystrix |
|---|---|---|
| 维护状态 | Alibaba 生态仍常见 | 老组件，已不建议新项目使用 |
| 限流能力 | 强 | 较弱 |
| 熔断降级 | 支持 | 支持 |
| 热点参数 | 支持 | 不突出 |
| 控制台 | Sentinel Dashboard | Hystrix Dashboard 旧 |
| 规则动态配置 | 支持对接 Nacos | 不如 Sentinel 方便 |
| 国内使用 | 常见 | 老项目可能存在 |

---

## 10. Nacos 配置中心

### 10.1 为什么需要配置中心

如果有几十个服务，每个服务又有多套环境：

```text
dev
test
pre
prod
```

配置散落在各个项目中会带来：

- 修改配置需要重新打包发布。
- 多实例配置不一致。
- 敏感配置难管理。
- 环境隔离混乱。
- 配置变更不可追踪。

配置中心解决：

- 集中管理配置。
- 支持动态刷新。
- 支持环境隔离。
- 支持配置历史版本。
- 支持灰度配置和权限控制。

### 10.2 什么配置适合放到 Nacos

适合放：

- 业务开关。
- 限流阈值。
- 第三方接口地址。
- 功能灰度配置。
- 日志级别。
- 可动态调整的业务参数。

不建议直接放或需要加密/权限控制：

- 数据库密码。
- 支付密钥。
- 私钥。
- 云厂商密钥。
- 生产环境核心敏感配置。

### 10.3 Data ID、Group、Namespace

Nacos 配置由以下信息定位：

```text
Namespace + Group + Data ID
```

常见命名：

```text
user-service-dev.yaml
user-service-test.yaml
user-service-prod.yaml
common.yaml
```

示例：

| 配置 | 说明 |
|---|---|
| `user-service-dev.yaml` | 用户服务开发环境配置 |
| `user-service-prod.yaml` | 用户服务生产环境配置 |
| `common.yaml` | 多服务共享配置 |
| `DEFAULT_GROUP` | 默认分组 |
| `dev namespace` | 开发环境命名空间 |

### 10.4 老版本写法：`bootstrap.yml`

老课程常见写法：

```yaml
spring:
  application:
    name: user-service
  profiles:
    active: dev
  cloud:
    nacos:
      server-addr: 127.0.0.1:8848
      config:
        file-extension: yaml
```

Data ID 默认推导：

```text
${spring.application.name}-${spring.profiles.active}.${file-extension}
```

例如：

```text
user-service-dev.yaml
```

这种写法在 `Spring Cloud 2020` 之后需要额外启用 `bootstrap` 机制，在更新版本中不再推荐作为主线。

### 10.5 新版推荐：`spring.config.import`

新版建议在 `application.yml` 中使用 `spring.config.import`。

基础示例：

```yaml
spring:
  application:
    name: user-service
  profiles:
    active: dev

  cloud:
    nacos:
      config:
        server-addr: 127.0.0.1:8848
        file-extension: yaml
        group: DEFAULT_GROUP

  config:
    import:
      - optional:nacos:${spring.application.name}-${spring.profiles.active}.yaml
```

共享配置示例：

```yaml
spring:
  config:
    import:
      - optional:nacos:common.yaml
      - optional:nacos:${spring.application.name}.yaml
      - optional:nacos:${spring.application.name}-${spring.profiles.active}.yaml
```

说明：

- `optional:` 表示配置不存在时不直接启动失败。
- 越靠后的配置通常可以覆盖前面的同名属性，具体以当前版本规则为准。
- 不同 Spring Cloud Alibaba 版本配置细节可能不同，一定要对照项目版本文档。

### 10.6 读取配置

使用 `@ConfigurationProperties` 更推荐：

```java
@Data
@Component
@ConfigurationProperties(prefix = "pattern")
public class PatternProperties {
    private String dateformat;
    private String env;
}
```

使用：

```java
@RestController
@RequestMapping("/test")
public class TestController {

    private final PatternProperties properties;

    public TestController(PatternProperties properties) {
        this.properties = properties;
    }

    @GetMapping("/config")
    public PatternProperties config() {
        return properties;
    }
}
```

如果是 `@Value`，动态刷新时通常需要配合：

```java
@RefreshScope
@RestController
public class DemoController {

    @Value("${pattern.dateformat}")
    private String dateformat;
}
```

### 10.7 配置优先级建议

建议设计：

```text
common.yaml                 # 所有服务共享
common-${profile}.yaml      # 某环境共享
user-service.yaml           # 用户服务基础配置
user-service-${profile}.yaml# 用户服务环境配置
本地 application.yml         # 服务启动必要配置
```

一般原则：

- 环境配置优先级高于通用配置。
- 服务私有配置优先级高于共享配置。
- 生产环境不要让开发环境配置混进来。
- 配置命名要规范，不要全靠默认值。

---

## 11. Seata 分布式事务

### 11.1 为什么分布式事务复杂

单体项目中，一个业务可能只操作一个数据库：

```java
@Transactional
public void createOrder() {
    orderMapper.insert(order);
    stockMapper.deduct(productId);
}
```

拆成微服务后：

```text
order-service -> 创建订单库记录
stock-service -> 扣减库存库记录
account-service -> 扣减账户余额
```

每个服务都有自己的数据库，本地事务只能保证单个服务内部一致，不能直接保证跨服务一致。

### 11.2 解决分布式一致性的常见方式

| 方式 | 说明 | 适用场景 |
|---|---|---|
| 本地事务 | 单服务内使用 `@Transactional` | 单体或单服务内部 |
| Seata AT | 对业务侵入较低，自动补偿 | 中后台系统、强一致要求较高 |
| TCC | Try、Confirm、Cancel 三阶段 | 金融、库存、账户等核心场景 |
| Saga | 长事务、状态机补偿 | 流程长、跨多个系统 |
| XA | 数据库 XA 协议 | 强一致但性能压力较大 |
| MQ 最终一致性 | 通过消息驱动异步一致 | 电商、支付后通知、积分、优惠券 |

### 11.3 Seata 三个角色

| 角色 | 全称 | 说明 |
|---|---|---|
| `TC` | Transaction Coordinator | 事务协调者，Seata Server |
| `TM` | Transaction Manager | 事务管理器，开启、提交、回滚全局事务 |
| `RM` | Resource Manager | 资源管理器，管理分支事务资源 |

调用链：

```text
TM 开启全局事务
  -> RM1 执行本地事务并注册分支事务
  -> RM2 执行本地事务并注册分支事务
TM 请求 TC 提交或回滚
TC 通知各 RM 提交或回滚
```

### 11.4 AT 模式基本原理

`AT` 模式是很多 Spring Cloud Alibaba 教程中的入门重点。

核心思路：

1. 执行业务 SQL 前，记录修改前数据镜像。
2. 执行业务 SQL 后，记录修改后数据镜像。
3. 本地事务提交。
4. 如果全局事务成功，删除 `undo_log`。
5. 如果全局事务失败，根据 `undo_log` 反向补偿回滚。

每个业务库需要有 `undo_log` 表。

### 11.5 使用 `@GlobalTransactional`

```java
@Service
public class OrderService {

    private final AccountClient accountClient;
    private final StockClient stockClient;

    @GlobalTransactional
    public void createOrder(CreateOrderRequest request) {
        // 1. 创建订单
        orderMapper.insert(buildOrder(request));

        // 2. 扣库存
        stockClient.deduct(request.getProductId(), request.getCount());

        // 3. 扣余额
        accountClient.deduct(request.getUserId(), request.getAmount());
    }
}
```

### 11.6 什么时候不要强行使用 Seata

不是所有跨服务调用都应该用 Seata。

不建议使用 Seata 的情况：

- 业务可以接受最终一致性。
- 调用链路特别长。
- 高并发核心链路，不能接受事务协调开销。
- 参与方不是数据库操作。
- 无法提供可靠补偿逻辑。
- 服务边界本身拆得不合理。

更推荐 MQ 最终一致性的例子：

```text
订单支付成功 -> 发送消息
库存服务消费消息 -> 扣库存
积分服务消费消息 -> 加积分
优惠券服务消费消息 -> 标记使用
```

---

## 12. RocketMQ / 消息队列基础定位

### 12.1 为什么微服务需要 MQ

消息队列用于解决：

- 异步处理。
- 流量削峰。
- 系统解耦。
- 最终一致性。
- 事件通知。
- 失败重试。

示例：

```text
用户下单成功
  -> 订单服务保存订单
  -> 发送 OrderCreatedEvent
  -> 库存服务扣库存
  -> 积分服务加积分
  -> 通知服务发短信
```

### 12.2 MQ 与 Feign 的区别

| 对比项 | Feign 同步调用 | MQ 异步消息 |
|---|---|---|
| 调用方式 | 请求-响应 | 发布-订阅 / 点对点 |
| 调用方是否等待 | 等待 | 不等待或少等待 |
| 耦合度 | 较高 | 较低 |
| 一致性 | 更接近实时一致 | 最终一致 |
| 故障影响 | 下游异常直接影响上游 | 可削弱直接影响 |
| 场景 | 查询、实时校验 | 通知、异步处理、削峰 |

### 12.3 基础阶段掌握到什么程度

SpringCloud 基础阶段不必深入 RocketMQ 源码，但应该知道：

- 什么是 `Topic`。
- 什么是 `Producer`、`Consumer`。
- 什么是消费组。
- 什么是消息重试。
- 什么是死信队列。
- 什么是消息幂等。
- 什么是顺序消息。
- 什么是事务消息。
- MQ 如何实现最终一致性。

---

## 13. 微服务认证授权

### 13.1 常见认证模式

微服务中常见认证结构：

```text
用户 -> Gateway -> Auth Service -> 颁发 JWT
用户后续请求携带 JWT
Gateway 校验 JWT
Gateway 将用户信息透传给下游服务
```

### 13.2 JWT 基础

`JWT` 一般包含：

```text
Header.Payload.Signature
```

Payload 中可以放：

- 用户 ID。
- 用户名。
- 角色。
- 权限。
- 过期时间。

不要放：

- 密码。
- 手机验证码。
- 身份证号。
- 银行卡。
- 私钥。
- 敏感业务数据。

### 13.3 Gateway 鉴权和下游服务鉴权

推荐：

1. `Gateway` 做统一登录态校验。
2. `Auth Service` 负责登录、刷新令牌、权限查询。
3. 下游服务不信任前端请求头，只信任网关转发的内部头。
4. 内部服务之间也要考虑鉴权或网络隔离。
5. 重要操作仍需要服务内部二次校验权限。

### 13.4 常见权限模型

| 模型 | 说明 |
|---|---|
| `RBAC` | 用户-角色-权限，企业系统最常用 |
| `ABAC` | 基于属性的权限控制，更灵活但复杂 |
| 数据权限 | 按部门、组织、租户隔离数据 |
| 多租户 | SaaS 系统常见，租户之间数据隔离 |

---

## 14. 微服务可观测性

### 14.1 为什么要可观测

微服务故障常常不是一个服务的问题，而是一条调用链的问题：

```text
gateway -> order-service -> user-service -> account-service -> mysql
```

如果没有可观测性，很难回答：

- 请求经过了哪些服务？
- 哪个服务慢？
- 哪个接口报错？
- 错误比例多少？
- 是单个实例问题还是整体问题？
- 是数据库慢还是远程调用慢？

### 14.2 三大可观测支柱

| 类型 | 说明 | 常见技术 |
|---|---|---|
| Logs | 日志 | `Logback`、`ELK`、`Loki` |
| Metrics | 指标 | `Micrometer`、`Prometheus`、`Grafana` |
| Traces | 链路追踪 | `SkyWalking`、`Zipkin`、`Jaeger`、`Micrometer Tracing` |

### 14.3 traceId

一次请求进入系统后，应生成一个 `traceId`，并在所有服务调用中传递。

日志中带上：

```text
traceId
spanId
userId
requestUri
method
cost
status
error
```

示例日志：

```text
2026-06-01 10:00:00 INFO [traceId=abc123] order-service createOrder cost=135ms
```

### 14.4 Actuator 健康检查

引入：

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-actuator</artifactId>
</dependency>
```

配置：

```yaml
management:
  endpoints:
    web:
      exposure:
        include: health,info,metrics,prometheus
  endpoint:
    health:
      show-details: always
```

常见端点：

| 端点 | 作用 |
|---|---|
| `/actuator/health` | 健康检查 |
| `/actuator/info` | 应用信息 |
| `/actuator/metrics` | 指标 |
| `/actuator/prometheus` | Prometheus 抓取指标 |

---

## 15. 微服务部署与生产注意点

### 15.1 多实例部署

微服务通常不会只部署一个实例，而是多个实例：

```text
user-service: 8081
user-service: 8082
user-service: 8083
```

目的：

- 提高可用性。
- 分摊流量。
- 支持滚动发布。
- 某个实例故障时不影响整体服务。

### 15.2 优雅停机

服务下线时要避免正在处理的请求直接中断。

配置示例：

```yaml
server:
  shutdown: graceful

spring:
  lifecycle:
    timeout-per-shutdown-phase: 30s
```

下线流程：

1. 从注册中心摘除实例。
2. 不再接收新请求。
3. 等待已有请求处理完成。
4. 关闭应用。

### 15.3 灰度发布

灰度发布常见方式：

- 按用户 ID 灰度。
- 按请求头灰度。
- 按 IP 灰度。
- 按 Nacos 实例权重灰度。
- 通过 Gateway 路由到新版本服务。

### 15.4 配置安全

生产注意：

- Nacos 控制台必须改默认密码。
- 不要暴露 Nacos 到公网。
- 配置变更要有审批。
- 敏感配置要加密或放到专门的密钥管理系统。
- 数据库账号按服务最小权限分配。
- 日志中不要打印密码、token、密钥。

---

## 16. 历史组件补充：Eureka、Ribbon、Hystrix、Zuul

### 16.1 Eureka

`Eureka` 是 Netflix 体系中的注册中心。老项目或老课程中常见。

学习建议：

- 了解服务注册、服务发现、心跳、剔除机制即可。
- 新的 Spring Cloud Alibaba 项目优先用 `Nacos`。
- 如果公司老项目使用 Eureka，需要能看懂配置和排查注册问题。

### 16.2 Ribbon

`Ribbon` 是旧版客户端负载均衡组件。

学习建议：

- 理解它的作用：根据服务名从实例列表中选择一个实例。
- 新项目优先理解 `Spring Cloud LoadBalancer`。
- 老笔记里 `@LoadBalanced + RestTemplate + Ribbon` 的案例可以当作理解负载均衡原理。

### 16.3 Hystrix

`Hystrix` 是老熔断降级组件。

学习建议：

- 了解熔断、降级、隔离、fallback 的概念。
- 新项目优先学习 `Sentinel` 或 `Resilience4j`。
- 不建议把 Hystrix 作为重点编码练习。

### 16.4 Zuul

`Zuul` 是老网关组件。

学习建议：

- 知道它曾经承担网关职责。
- 新项目优先学习 `Spring Cloud Gateway`。
- Gateway 更适合当前 SpringCloud 体系，支持响应式模型和更现代的路由过滤机制。

---

## 17. 高频面试题

### 17.1 微服务基础

#### 1. 什么是微服务？

微服务是一种分布式架构风格，它把系统拆分成多个围绕业务能力构建的小服务。每个服务可以独立开发、部署、扩容，并通过接口或消息进行通信。

#### 2. 微服务一定比单体好吗？

不一定。微服务提升了可扩展性和团队协作能力，但也带来了网络调用、数据一致性、部署、监控、故障排查等复杂度。小项目或团队能力不足时，单体反而更合适。

#### 3. 服务拆分原则是什么？

按业务边界拆分，保证单一职责、数据独立、接口清晰，避免循环依赖，不要拆得过细。高频强一致模块要谨慎拆分。

### 17.2 Nacos

#### 4. Nacos 有什么作用？

Nacos 可以做服务注册发现和配置中心。服务启动后注册到 Nacos，消费者通过服务名发现服务实例；配置中心可以集中管理配置并支持动态刷新。

#### 5. Nacos 的 Namespace、Group、Service 有什么区别？

`Namespace` 常用于环境隔离，`Group` 用于配置或服务分组，`Service` 是具体服务名，例如 `user-service`。

#### 6. Nacos 临时实例和非临时实例有什么区别？

临时实例通过心跳维持，心跳异常会被剔除；非临时实例不会因为心跳异常直接删除，更多依赖服务端主动检测。

### 17.3 OpenFeign

#### 7. OpenFeign 解决什么问题？

OpenFeign 把 HTTP 远程调用封装成接口调用，减少手写 URL、请求解析、响应转换等重复代码，并能和注册中心、负载均衡、熔断组件集成。

#### 8. Feign 和 RestTemplate 有什么区别？

`RestTemplate` 是手动发 HTTP 请求；`Feign` 是声明式客户端，更适合微服务之间接口调用。现在项目更推荐 Feign 或 WebClient。

#### 9. Feign 的 fallback 和 fallbackFactory 有什么区别？

`fallback` 只能返回降级实现；`fallbackFactory` 可以拿到异常原因，便于日志记录和问题排查。

### 17.4 Gateway

#### 10. 为什么要使用网关？

网关提供统一入口，集中处理路由、鉴权、跨域、限流、日志、灰度等横切逻辑，避免这些逻辑散落在每个微服务中。

#### 11. Gateway 的 Predicate 和 Filter 是什么？

`Predicate` 用于判断请求是否匹配某条路由；`Filter` 用于在请求转发前后修改请求或响应，例如加请求头、去路径前缀、鉴权。

#### 12. Gateway 和 Nginx 区别？

Nginx 更偏通用反向代理和静态资源服务，性能强；Gateway 是应用层 API 网关，更适合写 Java 业务过滤逻辑，并能和注册中心、鉴权体系结合。

### 17.5 Sentinel

#### 13. Sentinel 能解决什么问题？

Sentinel 用于流量控制、熔断降级、热点参数限流、系统保护、授权控制，防止服务过载和雪崩。

#### 14. 限流和熔断有什么区别？

限流是控制进入系统的流量；熔断是当下游服务不稳定时，调用方暂时停止调用下游，快速失败或降级返回。

#### 15. blockHandler 和 fallback 区别？

`blockHandler` 处理被 Sentinel 规则拦截的情况；`fallback` 处理业务方法自身抛出的异常。

### 17.6 Seata 与一致性

#### 16. Seata 三个角色是什么？

`TC` 是事务协调者，`TM` 是事务管理器，`RM` 是资源管理器。

#### 17. AT 模式原理是什么？

AT 模式通过记录数据修改前后镜像生成 `undo_log`，全局事务失败时根据 `undo_log` 反向补偿回滚。

#### 18. 分布式事务一定要用 Seata 吗？

不一定。很多业务可以使用 MQ 实现最终一致性。Seata 更适合需要较强一致性且参与方主要是数据库操作的场景。

### 17.7 配置中心与部署

#### 19. 哪些配置适合放 Nacos？

业务开关、限流阈值、灰度配置、日志级别、第三方接口地址等动态配置适合放 Nacos。敏感配置要谨慎处理。

#### 20. bootstrap.yml 和 spring.config.import 有什么区别？

`bootstrap.yml` 是老版本外部配置加载方式；新版本 Spring Boot / Spring Cloud 更推荐通过 `spring.config.import` 导入外部配置源，例如 Nacos。

---

## 18. 常见错误排查表

| 错误现象 | 常见原因 | 排查方向 |
|---|---|---|
| 服务没有注册到 Nacos | 依赖缺失、服务名没配、Nacos 地址错误 | 查 `spring.application.name`、`server-addr`、Nacos 控制台 |
| Feign 调用找不到服务 | 服务名写错、命名空间不同、服务未注册 | 查 `@FeignClient(name=...)` 和 Nacos 服务列表 |
| 配置中心读取不到配置 | Data ID 不匹配、Group/Namespace 不一致 | 查配置文件名、环境、命名空间 |
| 动态刷新不生效 | 没有 `@RefreshScope` 或版本写法不兼容 | 查 Nacos Config 依赖和刷新配置 |
| Gateway 路由 404 | `Path` 不匹配、`StripPrefix` 配错 | 打印转发路径，查路由配置 |
| Gateway 跨域失败 | CORS 配置不完整或前后端重复配置冲突 | 统一在 Gateway 配置 CORS |
| Sentinel 控制台无数据 | 没有访问资源、Dashboard 地址错误 | 访问接口后再看控制台 |
| Seata 回滚失败 | 没有 `undo_log`、代理数据源失败、事务分组错误 | 查 Seata Server、TC/TM/RM 日志 |
| 服务偶发超时 | 下游慢、连接池不足、超时配置不合理 | 查接口耗时、线程池、连接池 |
| 链路日志断裂 | traceId 没有跨线程/跨服务传递 | 查网关、Feign 拦截器、日志 MDC |
