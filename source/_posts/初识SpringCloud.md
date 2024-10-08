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



## 1.微服务是什么

​	概述：微服务，又叫微服务架构，是一种软件架构方式。它将应用构建成一系列按业务领域划分模块的、小的自治服务。
​	在微服务架构中，每个服务都是自我包含的，并且实现了单一的业务功能。
​	简单来说，就是将一个系统按业务划分成多个子系统，每个子系统都是完整的，可独立运行的，子系统间的交互可通过HTTP协议进行通信（也可以采用消息队列来通信，如RoocketMQ，Kafaka等）。
​	所以，不同子系统可以使用不同的编程语言实现，使用不同的存储技术。但是，因为子系统服务数量越多，管理起来越复杂，因此需要采用集中化管理，例如Eureka，Zookeeper等都是比较常见的服务集中化管理框架；同时，使用自动化部署（如Jenkins）减少人为控制，降低出错概率，提高效率。
​	

	例子：场景--购物车应用
		传统的一体化架构实现：所有的功能都被放到了一个代码库中，业务都开展在一个基础性的数据库之下，功能包含：品牌管理，商品管理，接收付款、客户服务等。当需要添加新品牌的详细信息时（新增了有别于旧品牌设置的结构），此时开发者不仅需要为这个服务添加新的标签而修改代码，而且还要重构整个系统并进行部署。
		与之相反的是，微服务架构可以帮助开发者克服使用旧架构时所面临的挑战，并且使得这个购物车应用可以很容易地被构建、部署和扩展。因为它为搜索、推荐、品牌管理、商品管理、客户服务等业务分别创建不同的微服务，当需求来临时，它只需修改并更新对应的微服务即可。

## 2.微服务的特点

​	● 解耦：同一系统内的服务大部分可以被解耦。因此应用，作为一个整体，可以轻易地被构建、修改和扩展。
​	● 组件化：微服务可以被看成相互独立的组件，这些组件可以被轻易地替换和升级。
​	● 业务能力：微服务很小，它们可以专注于某种单一的能力
​	● 自治：开发者和团队可以独立地工作，提高开发速度。
​	● 持续交付：允许持续发布软件新版本，通过系统化的自动手段来创建、测试和批准新版本。
​	● 职责明确：微服务不把应用看成一个又一个的项目。相反，它们把应用当成了自己需要负责的项目。
​	● 去中心化管理：关注于使用正确的工具来完成正确的工作。这也就是说，没有标准化的方式或者技术模式。开发者们有权选择最好的工具来解决问题。
​	● 敏捷性：微服务支持敏捷开发。任何新功能都可以被快速开发或丢弃。

## 3.微服务的优势

​	● 独立开发：基于各个微服务所独有的功能，它们可以被轻易开发出来。
​	● 独立部署：基于它们所提供的服务，它们可以被独立地部署到应用中。
​	● 错误隔离：即便其中某个服务发生了故障，整个系统还可以继续工作。
​	● 混合技术栈：可以使用不同的语言和技术来为同一个应用构建不同的服务。
​	● 按粒度扩展：可以根据需求扩展某一个组件，不需要将所有组件全部扩展。

## SpringCloud

背景：目前在国内用到的微服务构架有SpringCloud和Dubbo
	Dubbo，是阿里巴巴服务化治理的核心框架。Springcloud，是一系列关于服务治理框架的集合。
协议
	Dubbo缺省协议采用单一长连接和NIO异步通讯，适合于小数据量大并发的服务调用，以及服务消费者机器数远大于服务提供者机器数的情况	
	Spring Cloud 使用HTTP协议的REST API
运行流程

​	Dubbo：每个组件都是需要部署在单独的服务器上，gateway用来接受前端请求、聚合服务，并批量调用后台原子服务。每个service层和单独的DB交互。

​	SpringCloud：所有请求都统一通过 API 网关（Zuul）来访问内部服务。网关接收到请求后，从注册中心（Eureka）获取可用服务。由 Ribbon 进行均衡负载后，分发到后端的具体实例。微服务之间通过 Feign 进行通信处理业务。



#### 组件

​	Spring Cloud Config：配置管理工具包，让你可以把配置放到远程服务器，集中化管理集群配置，目前支持本地存储、Git以及Subversion。


Spring Cloud Bus：事件、消息总线，用于在集群（例如，配置变化事件）中传播状态变化，可与Spring Cloud Config联合实现热部署。

Eureka：云端服务发现，一个基于 REST 的服务，用于定位服务，以实现云端中间层服务发现和故障转移。

Hystriz：熔断器，容错管理工具，旨在通过熔断机制控制服务和第三方库的节点,从而对延迟和故障提供更强大的容错能力。

Zull：Zuul 是在云平台上提供动态路由,监控,弹性,安全等边缘服务的框架。Zuul 相当于是设备和 Netflix 流应用的 Web 网站后端所有请求的前门。

Archaius：配置管理API，包含一系列配置管理API，提供动态类型化属性、线程安全配置操作、轮询框架、回调机制等功能。

Consul：封装了Consul操作，consul是一个服务发现与配置工具，与Docker容器可以无缝集成。

Spring Cloud for Cloud Foundry：通过Oauth2协议绑定服务到CloudFoundry，CloudFoundry是VMware推出的开源PaaS云平台。

Spring Cloud Sleth：日志收集工具包，封装了Dapper和log-based追踪以及Zipkin和HTrace操作，为SpringCloud应用实现了一种分布式追踪解决方案。

Spring Cloud Data Flow：大数据操作工具，作为Spring XD的替代产品，它是一个混合计算模型，结合了流数据与批量数据的处理方式。

Spring Cloud Security：基于spring security的安全工具包，为你的应用程序添加安全控制。

Spring Cloud Zookeeper：操作Zookeeper的工具包，用于使用zookeeper方式的服务发现和配置管理。

Spring Cloud Stream：数据流操作开发包，封装了与Redis,Rabbit、Kafka等发送接收消息。

Spring Cloud CLI：基于 Spring Boot CLI，可以让你以命令行方式快速建立云组件。

Ribbon：提供云端负载均衡，有多种负载均衡策略可供选择，可配合服务发现和断路器使用。

Turbine：Turbine是聚合服务器发送事件流数据的一个工具，用来监控集群下hystrix的metrics情况。

Feign：Feign是一种声明式、模板化的HTTP客户端。

Spring Cloud Task：提供云端计划任务管理、任务调度。

Spring Cloud Connectors：便于云端应用程序在各种PaaS平台连接到后端，如：数据库和消息代理服务。

Spring Cloud Cluster：提供Leadership选举，如：Zookeeper, Redis, Hazelcast, Consul等常见状态模式的抽象和实现。

Spring Cloud Starters：Spring Boot式的启动项目，为Spring Cloud提供开箱即用的依赖管理。
