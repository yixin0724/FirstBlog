---
title: SSM三个框架的知识点
date: 2022-09-18 22:39:16
cover: https://pic.imgdb.cn/item/66ebf119f21886ccc0d25705.jpg
tags: 
  - SSM
  - Java
categories: 技术记录
description: 关于SSM的一些知识。
---

# SSM 三大框架知识点笔记（整理 + 补充增强版）

> 适用对象：已经学过 `JavaWeb`、`Maven`、`SpringBoot` 基础，准备系统复习传统 `SSM` 架构、面试知识点、项目整合流程的学习者。  
> 本文档在原始 `SSM整合.md` 基础上重构整理，保留原笔记中的 `SSM整合`、`统一结果封装`、`统一异常处理`、`前后台联调`、`拦截器` 等核心内容，并补充 `Spring`、`SpringMVC`、`MyBatis` 三个框架的常见但原笔记缺失的知识点。

---

## 目录

- [第 0 章：SSM 学习总览](#第-0-章ssm-学习总览)
- [第 1 章：SSM 整体架构与请求链路](#第-1-章ssm-整体架构与请求链路)
- [第 2 章：Spring 核心框架](#第-2-章spring-核心框架)
- [第 3 章：SpringMVC 框架](#第-3-章springmvc-框架)
- [第 4 章：MyBatis 框架](#第-4-章mybatis-框架)
- [第 5 章：SSM 整合配置](#第-5-章ssm-整合配置)
- [第 6 章：图书管理 CRUD 案例重构](#第-6-章图书管理-crud-案例重构)
- [第 7 章：统一响应结果封装](#第-7-章统一响应结果封装)
- [第 8 章：统一异常处理](#第-8-章统一异常处理)
- [第 9 章：前后台协议联调](#第-9-章前后台协议联调)
- [第 10 章：SpringMVC 拦截器](#第-10-章springmvc-拦截器)
- [第 11 章：SSM 项目常见问题与排错](#第-11-章ssm-项目常见问题与排错)
- [第 12 章：SSM 高频面试题](#第-12-章ssm-高频面试题)
- [附录：常见注解速查表](#附录常见注解速查表)

---

# 第 0 章：SSM 学习总览

## 0.1 什么是 SSM

`SSM` 是传统 Java Web 开发中非常经典的三大框架组合：

| 框架 | 全称 | 所属层次 | 核心职责 |
|---|---|---|---|
| `Spring` | Spring Framework | 业务层 / 基础容器 | `IoC`、`DI`、`AOP`、事务管理、对象生命周期管理 |
| `SpringMVC` | Spring Web MVC | 表现层 / 控制层 | 接收请求、参数绑定、调用业务层、响应数据 |
| `MyBatis` | MyBatis Persistence Framework | 持久层 / 数据访问层 | SQL 映射、参数映射、结果映射、动态 SQL、Mapper 代理 |

一句话理解：

> `Spring` 管对象，`SpringMVC` 管请求，`MyBatis` 管数据库。

---

## 0.2 SSM 与 SpringBoot 的关系

`SSM` 不是过时知识。即使现在项目更多使用 `SpringBoot`，底层仍然绕不开这些核心思想。

| 对比项 | 传统 SSM | SpringBoot |
|---|---|---|
| 配置方式 | 手动配置较多，例如 `SpringConfig`、`SpringMvcConfig`、`MyBatisConfig` | 自动配置为主 |
| 部署方式 | 通常打成 `war`，部署到外部 `Tomcat` | 通常打成 `jar`，内置 `Tomcat` |
| 学习价值 | 更适合理解底层原理 | 更适合快速开发项目 |
| 面试价值 | 体现框架原理能力 | 体现工程开发能力 |

学习建议：

1. 先理解传统 `SSM` 的分层与请求链路。
2. 再理解 `SpringBoot` 是如何通过自动配置简化 `SSM` 的。
3. 面试时不要只会用 `@SpringBootApplication`，要能说清背后的 `Spring`、`SpringMVC`、`MyBatis`。

---

## 0.3 SSM 学习路线

```text
JavaWeb 基础
    ↓
Servlet / Filter / Listener / Tomcat
    ↓
Spring IoC / DI / Bean / AOP / 事务
    ↓
SpringMVC 请求处理流程 / 参数绑定 / 响应 / 拦截器 / 异常处理
    ↓
MyBatis Mapper / XML / 动态 SQL / 结果映射 / 缓存
    ↓
SSM 整合
    ↓
统一响应、统一异常、前后端联调、权限校验
```

---

# 第 1 章：SSM 整体架构与请求链路

## 1.1 三层架构

典型 Java Web 项目通常分为三层：

| 层次 | 常见包名 | 作用 |
|---|---|---|
| 表现层 | `controller` | 接收 HTTP 请求，校验参数，调用业务层，返回响应 |
| 业务层 | `service` / `service.impl` | 编写业务逻辑，控制事务边界 |
| 持久层 | `dao` / `mapper` | 访问数据库，执行 SQL |
| 实体层 | `domain` / `entity` / `pojo` | 封装业务数据 |
| 配置层 | `config` | 存放 Spring、SpringMVC、MyBatis 配置 |

原始笔记中已经有 `controller → service → dao` 的链路，这里进一步规范为：

```text
浏览器 / 前端页面
    ↓ HTTP 请求
Tomcat
    ↓
Filter
    ↓
DispatcherServlet
    ↓
HandlerMapping
    ↓
Controller
    ↓
Service
    ↓
Mapper / Dao
    ↓
MyBatis
    ↓
数据库
```

---

## 1.2 SSM 各组件在请求中的职责

一次查询所有图书的请求 `GET /books`，在 SSM 中大致流程如下：

1. 浏览器或前端通过 `Axios` 发送 `GET /books` 请求。
2. 请求先到达 `Tomcat`。
3. `CharacterEncodingFilter` 等过滤器先执行。
4. 请求进入 `SpringMVC` 的前端控制器 `DispatcherServlet`。
5. `DispatcherServlet` 根据请求路径找到对应的 `Controller` 方法。
6. `Controller` 调用 `BookService.getAll()`。
7. `Service` 调用 `BookDao.getAll()`。
8. `MyBatis` 根据 Mapper 方法找到对应 SQL 并执行。
9. 查询结果映射为 `Book` 对象集合。
10. `Controller` 将结果封装为 `Result`。
11. `SpringMVC` 借助 `HttpMessageConverter` 把对象转换为 JSON。
12. 前端接收 JSON 并渲染页面。

---

## 1.3 传统 SSM 常见项目结构

```text
ssm-demo
├── pom.xml
├── src
│   ├── main
│   │   ├── java
│   │   │   └── com.blog
│   │   │       ├── config
│   │   │       │   ├── SpringConfig.java
│   │   │       │   ├── SpringMvcConfig.java
│   │   │       │   ├── MyBatisConfig.java
│   │   │       │   ├── JdbcConfig.java
│   │   │       │   └── ServletContainersInitConfig.java
│   │   │       ├── controller
│   │   │       │   └── BookController.java
│   │   │       ├── service
│   │   │       │   ├── BookService.java
│   │   │       │   └── impl
│   │   │       │       └── BookServiceImpl.java
│   │   │       ├── dao
│   │   │       │   └── BookDao.java
│   │   │       ├── domain
│   │   │       │   └── Book.java
│   │   │       └── exception
│   │   │           ├── BusinessException.java
│   │   │           └── SystemException.java
│   │   ├── resources
│   │   │   └── jdbc.properties
│   │   └── webapp
│   │       ├── pages
│   │       ├── css
│   │       ├── js
│   │       └── plugins
│   └── test
│       └── java
│           └── com.blog
│               └── BookServiceTest.java
```

---

## 1.4 SSM 中常见对象命名

| 类型 | 推荐命名 | 示例 |
|---|---|---|
| 实体类 | 名词 | `Book`、`User` |
| Mapper / Dao 接口 | 实体名 + `Dao` 或 `Mapper` | `BookDao`、`UserMapper` |
| Service 接口 | 实体名 + `Service` | `BookService` |
| Service 实现类 | 实体名 + `ServiceImpl` | `BookServiceImpl` |
| Controller | 实体名 + `Controller` | `BookController` |
| 统一返回类 | `Result`、`ApiResponse` | `Result` |
| 状态码类 | `Code`、`ResultCode` | `Code` |
| 异常处理器 | `ProjectExceptionAdvice`、`GlobalExceptionHandler` | `ProjectExceptionAdvice` |

---

# 第 2 章：Spring 核心框架

## 2.1 Spring 的核心能力

`Spring Framework` 是 SSM 中最核心的基础框架。它提供的能力包括：

1. `IoC` 容器：统一创建和管理对象。
2. `DI` 依赖注入：自动装配对象之间的依赖。
3. `AOP` 面向切面编程：对方法进行增强。
4. 事务管理：声明式事务、编程式事务。
5. JDBC 抽象：简化数据库访问。
6. 与 Web、ORM、测试等技术集成。

在 SSM 中，`Spring` 主要管理：

- `Service` 对象。
- `Dao / Mapper` 代理对象。
- 数据源 `DataSource`。
- 事务管理器 `DataSourceTransactionManager`。
- 业务类中的事务边界。

---

## 2.2 IoC：控制反转

`IoC` 全称是 `Inversion of Control`，即控制反转。

传统写法：

```java
public class BookController {
    private BookService bookService = new BookServiceImpl();
}
```

这种方式的问题：

1. `Controller` 直接依赖具体实现类。
2. 如果 `BookServiceImpl` 变更，`Controller` 也可能要修改。
3. 不利于测试和扩展。

使用 `Spring IoC` 后：

```java
@RestController
public class BookController {

    @Autowired
    private BookService bookService;
}
```

此时对象创建权从程序员手动 `new`，转移给 `Spring` 容器。

总结：

> `IoC` 的核心不是消灭对象创建，而是把对象创建、对象依赖关系维护交给容器统一管理。

---

## 2.3 DI：依赖注入

`DI` 全称是 `Dependency Injection`，即依赖注入。

常见注入方式：

| 注入方式 | 示例 | 推荐程度 |
|---|---|---|
| 属性注入 | `@Autowired private BookService bookService;` | 初学常见，但不推荐大型项目大量使用 |
| Setter 注入 | `setBookService(BookService bookService)` | 可选 |
| 构造器注入 | `public BookController(BookService bookService)` | 推荐 |

### 2.3.1 属性注入

```java
@RestController
public class BookController {

    @Autowired
    private BookService bookService;
}
```

优点：代码短。  
缺点：不利于单元测试，不容易发现循环依赖。

### 2.3.2 构造器注入

```java
@RestController
public class BookController {

    private final BookService bookService;

    public BookController(BookService bookService) {
        this.bookService = bookService;
    }
}
```

优点：

1. 依赖关系更明确。
2. 可以配合 `final` 使用。
3. 更利于单元测试。
4. 更容易暴露循环依赖问题。

面试建议：

> 简历项目或正式项目中，优先使用构造器注入；学习阶段看到 `@Autowired` 属性注入也要能理解。

---

## 2.4 Bean 与组件扫描

被 `Spring` 容器管理的对象称为 `Bean`。

常见声明 Bean 的注解：

| 注解 | 位置 | 作用 |
|---|---|---|
| `@Component` | 普通类 | 通用组件 |
| `@Controller` | Controller 类 | 表现层 Bean |
| `@Service` | Service 实现类 | 业务层 Bean |
| `@Repository` | Dao 类 | 持久层 Bean |
| `@Bean` | 配置类方法上 | 将方法返回值注册为 Bean |
| `@Configuration` | 配置类上 | 声明当前类是 Spring 配置类 |

示例：

```java
@Service
public class BookServiceImpl implements BookService {
}
```

手动配置 Bean：

```java
@Configuration
public class JdbcConfig {

    @Bean
    public DataSource dataSource() {
        DruidDataSource dataSource = new DruidDataSource();
        return dataSource;
    }
}
```

组件扫描：

```java
@Configuration
@ComponentScan("com.blog.service")
public class SpringConfig {
}
```

`@ComponentScan` 会扫描指定包及其子包下被 `@Component`、`@Service` 等注解标记的类。

---

## 2.5 Bean 的作用域

| 作用域 | 含义 | 典型场景 |
|---|---|---|
| `singleton` | 单例，容器中只有一个 Bean | 默认，大多数 Service 使用 |
| `prototype` | 每次获取都会创建新对象 | 有状态对象 |
| `request` | 每次 HTTP 请求创建一个 | Web 场景 |
| `session` | 每个 HTTP Session 创建一个 | 用户会话 |
| `application` | ServletContext 范围 | Web 应用级对象 |

示例：

```java
@Component
@Scope("prototype")
public class TaskContext {
}
```

默认情况下，`Spring` 的 Bean 是 `singleton`。

注意：

> 单例 Bean 中不要保存会被多个请求同时修改的成员变量，否则会产生线程安全问题。

---

## 2.6 Bean 生命周期

一个 Bean 大致经历：

```text
实例化
    ↓
属性赋值
    ↓
Aware 接口回调
    ↓
BeanPostProcessor 前置处理
    ↓
初始化方法
    ↓
BeanPostProcessor 后置处理
    ↓
Bean 可以使用
    ↓
销毁方法
```

常见生命周期注解：

```java
@Component
public class CacheLoader {

    @PostConstruct
    public void init() {
        System.out.println("初始化缓存");
    }

    @PreDestroy
    public void destroy() {
        System.out.println("释放资源");
    }
}
```

---

## 2.7 `@Configuration` 与 `@Bean`

`@Configuration` 用于声明配置类，`@Bean` 用于把方法返回值交给 Spring 容器。

```java
@Configuration
public class MyConfig {

    @Bean
    public UserService userService() {
        return new UserServiceImpl();
    }
}
```

等价理解：

```text
Spring 调用 userService() 方法
    ↓
拿到返回对象
    ↓
把对象放入 IoC 容器
```

常见用途：

1. 注册第三方类库对象，例如 `DruidDataSource`。
2. 注册框架对象，例如 `SqlSessionFactoryBean`。
3. 注册自定义工具类对象。

---

## 2.8 AOP：面向切面编程

`AOP` 全称是 `Aspect Oriented Programming`，即面向切面编程。

核心思想：

> 在不修改原有业务代码的情况下，对方法进行统一增强。

典型应用场景：

1. 日志记录。
2. 权限校验。
3. 事务管理。
4. 接口耗时统计。
5. 参数校验。
6. 异常监控。

---

## 2.9 AOP 核心概念

| 概念 | 说明 |
|---|---|
| 连接点 `JoinPoint` | 可以被增强的方法 |
| 切入点 `Pointcut` | 真正要增强的方法范围 |
| 通知 `Advice` | 具体增强逻辑 |
| 切面 `Aspect` | 切入点 + 通知 |
| 目标对象 `Target` | 被增强的对象 |
| 代理对象 `Proxy` | 增强后的对象 |
| 织入 `Weaving` | 把增强逻辑加入目标方法的过程 |

---

## 2.10 AOP 通知类型

| 通知类型 | 注解 | 执行时机 |
|---|---|---|
| 前置通知 | `@Before` | 目标方法执行前 |
| 后置通知 | `@After` | 目标方法执行后，无论是否异常 |
| 返回后通知 | `@AfterReturning` | 目标方法成功返回后 |
| 异常后通知 | `@AfterThrowing` | 目标方法抛异常后 |
| 环绕通知 | `@Around` | 目标方法执行前后都可增强，功能最强 |

示例：

```java
@Aspect
@Component
public class LogAspect {

    @Around("execution(* com.blog.service.*.*(..))")
    public Object recordTime(ProceedingJoinPoint joinPoint) throws Throwable {
        long begin = System.currentTimeMillis();

        Object result = joinPoint.proceed();

        long end = System.currentTimeMillis();
        System.out.println(joinPoint.getSignature() + "耗时：" + (end - begin) + "ms");

        return result;
    }
}
```

---

## 2.11 切入点表达式

常见写法：

```java
execution(* com.blog.service.*.*(..))
```

含义：

| 位置 | 含义 |
|---|---|
| `execution` | 按方法执行匹配 |
| 第一个 `*` | 任意返回值 |
| `com.blog.service` | 包名 |
| 第一个 `*` | 任意类 |
| 第二个 `*` | 任意方法 |
| `(..)` | 任意参数 |

更多示例：

```java
// 匹配 service 包下所有类的所有方法
execution(* com.blog.service.*.*(..))

// 匹配 service 包及其子包下所有方法
execution(* com.blog.service..*.*(..))

// 匹配 BookService 的所有方法
execution(* com.blog.service.BookService.*(..))

// 匹配所有 save 开头的方法
execution(* com.blog.service.*.save*(..))
```

---

## 2.12 Spring 事务管理

事务用于保证一组数据库操作要么全部成功，要么全部失败。

事务四大特性 `ACID`：

| 特性 | 说明 |
|---|---|
| 原子性 `Atomicity` | 一个事务中的操作要么全成功，要么全失败 |
| 一致性 `Consistency` | 事务前后数据状态保持一致 |
| 隔离性 `Isolation` | 多个事务之间互不干扰 |
| 持久性 `Durability` | 事务提交后数据永久保存 |

---

## 2.13 声明式事务

在 SSM 中，最常见的是声明式事务：

```java
@Service
public class BookServiceImpl implements BookService {

    @Transactional
    public boolean save(Book book) {
        return bookDao.save(book) > 0;
    }
}
```

配置类需要开启事务：

```java
@Configuration
@EnableTransactionManagement
public class SpringConfig {
}
```

并注册事务管理器：

```java
@Bean
public PlatformTransactionManager transactionManager(DataSource dataSource) {
    DataSourceTransactionManager transactionManager = new DataSourceTransactionManager();
    transactionManager.setDataSource(dataSource);
    return transactionManager;
}
```

---

## 2.14 `@Transactional` 常用属性

| 属性 | 作用 |
|---|---|
| `propagation` | 事务传播行为 |
| `isolation` | 事务隔离级别 |
| `timeout` | 超时时间 |
| `readOnly` | 是否只读 |
| `rollbackFor` | 指定哪些异常回滚 |
| `noRollbackFor` | 指定哪些异常不回滚 |

示例：

```java
@Transactional(
    rollbackFor = Exception.class,
    timeout = 5,
    readOnly = false
)
public void saveOrder(Order order) {
}
```

---

## 2.15 事务传播行为

| 传播行为 | 含义 | 常见程度 |
|---|---|---|
| `REQUIRED` | 有事务就加入，没有就新建 | 最常用，默认 |
| `REQUIRES_NEW` | 总是新建事务，挂起外部事务 | 常用于日志、流水 |
| `SUPPORTS` | 有事务就加入，没有就非事务执行 | 查询场景 |
| `NOT_SUPPORTED` | 非事务执行，有事务则挂起 | 少用 |
| `MANDATORY` | 必须在事务中执行，否则报错 | 少用 |
| `NEVER` | 必须非事务执行，否则报错 | 少用 |
| `NESTED` | 嵌套事务，依赖保存点 | 特定场景 |

示例：

```java
@Transactional(propagation = Propagation.REQUIRES_NEW)
public void saveLog(Log log) {
    logDao.save(log);
}
```

---

## 2.16 事务失效的常见场景

这是面试高频点。

### 场景一：方法不是 `public`

```java
@Transactional
private void save() {
}
```

多数代理模式下，非 `public` 方法不适合作为事务入口。

### 场景二：同类内部方法调用

```java
@Service
public class UserService {

    public void methodA() {
        methodB(); // 这里不会经过 Spring 代理对象
    }

    @Transactional
    public void methodB() {
    }
}
```

原因：

> `@Transactional` 基于代理增强，同类内部 `this.methodB()` 调用不会经过代理对象。

### 场景三：异常被捕获但没有继续抛出

```java
@Transactional
public void save() {
    try {
        int i = 1 / 0;
    } catch (Exception e) {
        // 吞掉异常，事务可能不会回滚
    }
}
```

解决：

```java
@Transactional(rollbackFor = Exception.class)
public void save() {
    try {
        int i = 1 / 0;
    } catch (Exception e) {
        throw new RuntimeException(e);
    }
}
```

### 场景四：默认只回滚运行时异常

默认情况下，`Spring` 对 `RuntimeException` 和 `Error` 回滚，对受检异常不一定回滚。

推荐写法：

```java
@Transactional(rollbackFor = Exception.class)
public void save() throws Exception {
}
```

### 场景五：对象没有交给 Spring 管理

```java
BookService service = new BookServiceImpl();
service.save(book);
```

如果对象是自己 `new` 出来的，不是 Spring 容器中的 Bean，事务自然不会生效。

---

# 第 3 章：SpringMVC 框架

## 3.1 SpringMVC 是什么

`SpringMVC` 是基于 `Servlet` 的 Web MVC 框架，主要负责表现层开发。

核心职责：

1. 接收 HTTP 请求。
2. 路由请求到 Controller 方法。
3. 完成参数绑定。
4. 完成类型转换。
5. 调用业务层。
6. 封装响应结果。
7. JSON 序列化。
8. 统一异常处理。
9. 拦截器增强。

---

## 3.2 SpringMVC 核心组件

| 组件 | 作用 |
|---|---|
| `DispatcherServlet` | 前端控制器，统一接收请求 |
| `HandlerMapping` | 根据请求路径找到处理器 |
| `HandlerAdapter` | 调用 Controller 方法 |
| `Controller` | 编写请求处理逻辑 |
| `ViewResolver` | 解析视图，前后端不分离常用 |
| `HttpMessageConverter` | 对请求体和响应体进行转换，例如 JSON |
| `HandlerInterceptor` | 拦截 Controller 方法 |

---

## 3.3 SpringMVC 请求处理流程

```text
客户端请求
    ↓
DispatcherServlet
    ↓
HandlerMapping 查找 Controller 方法
    ↓
HandlerAdapter 调用 Controller
    ↓
Controller 调用 Service
    ↓
返回对象 / Result
    ↓
HttpMessageConverter 转 JSON
    ↓
响应客户端
```

---

## 3.4 `DispatcherServlet`

`DispatcherServlet` 是 SpringMVC 的前端控制器。

传统 SSM 中需要通过配置类注册：

```java
public class ServletContainersInitConfig
        extends AbstractAnnotationConfigDispatcherServletInitializer {

    @Override
    protected Class<?>[] getRootConfigClasses() {
        return new Class[]{SpringConfig.class};
    }

    @Override
    protected Class<?>[] getServletConfigClasses() {
        return new Class[]{SpringMvcConfig.class};
    }

    @Override
    protected String[] getServletMappings() {
        return new String[]{"/"};
    }
}
```

解释：

| 方法 | 作用 |
|---|---|
| `getRootConfigClasses()` | 返回 Spring 根容器配置类，通常管理 `Service`、`Dao`、事务 |
| `getServletConfigClasses()` | 返回 SpringMVC 子容器配置类，通常管理 `Controller`、拦截器 |
| `getServletMappings()` | 配置 `DispatcherServlet` 拦截路径 |
| `getServletFilters()` | 配置过滤器，例如编码过滤器 |

---

## 3.5 `@Controller` 与 `@RestController`

### 3.5.1 `@Controller`

```java
@Controller
public class PageController {

    @RequestMapping("/index")
    public String index() {
        return "index";
    }
}
```

通常用于返回页面视图。

### 3.5.2 `@RestController`

```java
@RestController
public class BookController {

    @GetMapping("/books")
    public List<Book> getAll() {
        return bookService.getAll();
    }
}
```

`@RestController` 等价于：

```java
@Controller
@ResponseBody
```

表示方法返回值直接写入响应体，常用于返回 JSON 数据。

---

## 3.6 请求映射注解

| 注解 | 作用 |
|---|---|
| `@RequestMapping` | 通用请求映射 |
| `@GetMapping` | 处理 GET 请求 |
| `@PostMapping` | 处理 POST 请求 |
| `@PutMapping` | 处理 PUT 请求 |
| `@DeleteMapping` | 处理 DELETE 请求 |
| `@PatchMapping` | 处理 PATCH 请求 |

示例：

```java
@RestController
@RequestMapping("/books")
public class BookController {

    @GetMapping("/{id}")
    public Book getById(@PathVariable Integer id) {
        return bookService.getById(id);
    }

    @PostMapping
    public boolean save(@RequestBody Book book) {
        return bookService.save(book);
    }
}
```

---

## 3.7 参数接收

### 3.7.1 简单参数

请求：

```text
GET /simpleParam?name=Tom&age=18
```

Controller：

```java
@GetMapping("/simpleParam")
public String simpleParam(String name, Integer age) {
    return name + ":" + age;
}
```

如果请求参数名与形参名不一致：

```java
@GetMapping("/simpleParam")
public String simpleParam(@RequestParam("name") String username, Integer age) {
    return username + ":" + age;
}
```

---

### 3.7.2 POJO 参数

请求：

```text
GET /users?name=Tom&age=18
```

实体类：

```java
public class User {
    private String name;
    private Integer age;

    // getter / setter
}
```

Controller：

```java
@GetMapping("/users")
public String pojoParam(User user) {
    return user.toString();
}
```

---

### 3.7.3 嵌套 POJO 参数

```java
public class User {
    private String name;
    private Address address;
}

public class Address {
    private String province;
    private String city;
}
```

请求：

```text
GET /users?name=Tom&address.province=湖南&address.city=长沙
```

---

### 3.7.4 数组参数

请求：

```text
GET /arrayParam?hobby=java&hobby=game&hobby=music
```

Controller：

```java
@GetMapping("/arrayParam")
public String arrayParam(String[] hobby) {
    return Arrays.toString(hobby);
}
```

---

### 3.7.5 集合参数

```java
@GetMapping("/listParam")
public String listParam(@RequestParam List<String> hobby) {
    return hobby.toString();
}
```

集合类型参数一般需要加 `@RequestParam`。

---

### 3.7.6 日期参数

```java
@GetMapping("/dateParam")
public String dateParam(
        @DateTimeFormat(pattern = "yyyy-MM-dd HH:mm:ss")
        LocalDateTime updateTime) {
    return updateTime.toString();
}
```

---

### 3.7.7 JSON 参数

请求体：

```json
{
  "name": "Tom",
  "age": 18
}
```

Controller：

```java
@PostMapping("/users")
public String jsonParam(@RequestBody User user) {
    return user.toString();
}
```

注意：

> 使用 `@RequestBody` 接收 JSON 时，请求头通常需要设置 `Content-Type: application/json`。

---

### 3.7.8 路径参数

请求：

```text
GET /books/1
```

Controller：

```java
@GetMapping("/books/{id}")
public Book getById(@PathVariable Integer id) {
    return bookService.getById(id);
}
```

多个路径参数：

```java
@GetMapping("/users/{userId}/orders/{orderId}")
public Order getOrder(
        @PathVariable Long userId,
        @PathVariable Long orderId) {
    return orderService.getOrder(userId, orderId);
}
```

---

## 3.8 响应数据

### 3.8.1 返回字符串

```java
@GetMapping("/hello")
public String hello() {
    return "hello";
}
```

如果类上是 `@RestController`，返回的是响应体字符串。  
如果是 `@Controller`，可能表示视图名。

---

### 3.8.2 返回对象

```java
@GetMapping("/books/{id}")
public Book getById(@PathVariable Integer id) {
    return bookService.getById(id);
}
```

对象会通过 `Jackson` 转为 JSON。

---

### 3.8.3 返回统一结果

```java
@GetMapping("/books/{id}")
public Result getById(@PathVariable Integer id) {
    Book book = bookService.getById(id);
    return Result.success(book);
}
```

---

## 3.9 JSON 转换原理

在 SpringMVC 中，对象与 JSON 的转换通常由 `HttpMessageConverter` 完成。

常见转换器：

- `MappingJackson2HttpMessageConverter`

需要依赖：

```xml
<dependency>
    <groupId>com.fasterxml.jackson.core</groupId>
    <artifactId>jackson-databind</artifactId>
    <version>2.17.2</version>
</dependency>
```

如果缺少 `jackson-databind`，可能会出现：

```text
No converter found for return value of type
```

---

## 3.10 参数校验 Validation

原始笔记中对参数校验记录较少，这里补充。

引入依赖：

```xml
<dependency>
    <groupId>jakarta.validation</groupId>
    <artifactId>jakarta.validation-api</artifactId>
    <version>3.0.2</version>
</dependency>
```

如果是 Spring 5 + Java EE 旧体系，可能使用：

```xml
<dependency>
    <groupId>javax.validation</groupId>
    <artifactId>validation-api</artifactId>
    <version>2.0.1.Final</version>
</dependency>
```

实体类：

```java
public class Book {

    @NotBlank(message = "图书名称不能为空")
    private String name;

    @NotBlank(message = "图书类型不能为空")
    private String type;

    @Size(max = 255, message = "描述不能超过255个字符")
    private String description;
}
```

Controller：

```java
@PostMapping("/books")
public Result save(@Validated @RequestBody Book book) {
    boolean flag = bookService.save(book);
    return flag ? Result.success() : Result.error("保存失败");
}
```

---

## 3.11 全局异常处理

全局异常处理使用：

```java
@RestControllerAdvice
public class ProjectExceptionAdvice {

    @ExceptionHandler(Exception.class)
    public Result doException(Exception ex) {
        return Result.error("系统繁忙，请稍后再试");
    }
}
```

`@RestControllerAdvice` 等价于：

```java
@ControllerAdvice
@ResponseBody
```

---

## 3.12 静态资源放行

传统 SSM 中，如果 `DispatcherServlet` 拦截了 `/`，静态资源可能被 SpringMVC 拦截，需要配置放行：

```java
@Configuration
public class SpringMvcSupport implements WebMvcConfigurer {

    @Override
    public void addResourceHandlers(ResourceHandlerRegistry registry) {
        registry.addResourceHandler("/pages/**").addResourceLocations("/pages/");
        registry.addResourceHandler("/css/**").addResourceLocations("/css/");
        registry.addResourceHandler("/js/**").addResourceLocations("/js/");
        registry.addResourceHandler("/plugins/**").addResourceLocations("/plugins/");
    }
}
```

---

## 3.13 Filter、Interceptor、AOP 的区别

| 对比项 | Filter | Interceptor | AOP |
|---|---|---|---|
| 所属技术 | Servlet | SpringMVC | Spring |
| 作用位置 | 请求进入 Servlet 前后 | Controller 方法前后 | 任意 Spring Bean 方法前后 |
| 拦截范围 | 几乎所有 Web 请求 | SpringMVC 管理的请求 | Spring 容器中的 Bean 方法 |
| 常见用途 | 编码、登录过滤、跨域 | 权限校验、登录校验、日志 | 事务、日志、权限、耗时统计 |
| 是否依赖 Spring | 不一定 | 是 | 是 |

记忆：

> `Filter` 管 Web 入口，`Interceptor` 管 Controller，`AOP` 管 Bean 方法。

---

# 第 4 章：MyBatis 框架

## 4.1 MyBatis 是什么

`MyBatis` 是一个持久层框架，核心作用是把 Java 方法与 SQL 语句映射起来。

它解决的问题：

1. 简化 `JDBC` 代码。
2. 自动完成参数设置。
3. 自动完成结果集封装。
4. 支持动态 SQL。
5. 支持一级缓存、二级缓存。
6. 支持注解和 XML 两种 SQL 写法。

---

## 4.2 MyBatis 核心组件

| 组件 | 作用 |
|---|---|
| `SqlSessionFactoryBuilder` | 构建 `SqlSessionFactory` |
| `SqlSessionFactory` | 创建 `SqlSession` |
| `SqlSession` | 执行 SQL、获取 Mapper |
| `Mapper` 接口 | 定义数据库操作方法 |
| `Mapper.xml` | 编写 SQL 映射 |
| `Executor` | 执行器 |
| `MappedStatement` | 封装一条 SQL 的配置信息 |

---

## 4.3 MyBatis 执行流程

```text
调用 Mapper 接口方法
    ↓
Mapper 代理对象拦截方法调用
    ↓
根据 namespace + methodName 找到 SQL
    ↓
解析参数
    ↓
执行 JDBC
    ↓
映射 ResultSet
    ↓
返回 Java 对象
```

---

## 4.4 Mapper 接口开发

原始笔记使用的是注解方式：

```java
public interface BookDao {

    @Insert("insert into tbl_book values (null, #{type}, #{name}, #{description})")
    int save(Book book);

    @Update("update tbl_book set type=#{type}, name=#{name}, description=#{description} where id=#{id}")
    int update(Book book);

    @Delete("delete from tbl_book where id=#{id}")
    int delete(Integer id);

    @Select("select * from tbl_book where id=#{id}")
    Book getById(Integer id);

    @Select("select * from tbl_book")
    List<Book> getAll();
}
```

注解方式适合简单 SQL。  
复杂 SQL 推荐使用 XML。

---

## 4.5 XML 映射文件

Mapper 接口：

```java
public interface BookMapper {
    Book getById(Integer id);
    List<Book> getAll();
}
```

`BookMapper.xml`：

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "https://mybatis.org/dtd/mybatis-3-mapper.dtd">

<mapper namespace="com.blog.dao.BookMapper">

    <select id="getById" resultType="com.blog.domain.Book">
        select id, type, name, description
        from tbl_book
        where id = #{id}
    </select>

    <select id="getAll" resultType="com.blog.domain.Book">
        select id, type, name, description
        from tbl_book
    </select>

</mapper>
```

注意：

1. `namespace` 必须对应 Mapper 接口全限定名。
2. `id` 必须对应 Mapper 接口方法名。
3. 参数和返回值要能匹配。
4. XML 文件建议放在 `resources` 对应包路径下。

---

## 4.6 `#{}` 与 `${}` 的区别

这是 MyBatis 高频面试题。

| 写法 | 本质 | 是否预编译 | 是否防 SQL 注入 | 使用场景 |
|---|---|---|---|---|
| `#{}` | 占位符 | 是 | 是 | 普通参数 |
| `${}` | 字符串拼接 | 否 | 否 | 表名、字段名、排序字段等不能用占位符的位置 |

示例：

```sql
select * from tbl_book where id = #{id}
```

最终类似：

```sql
select * from tbl_book where id = ?
```

不安全示例：

```sql
select * from tbl_book order by ${sortField}
```

如果使用 `${}`，必须严格白名单校验：

```java
private static final Set<String> ALLOW_SORT_FIELDS = Set.of("id", "name", "type");
```

结论：

> 能用 `#{}` 就不要用 `${}`。

---

## 4.7 参数传递

### 4.7.1 单个简单参数

```java
Book getById(Integer id);
```

XML：

```xml
<select id="getById" resultType="Book">
    select * from tbl_book where id = #{id}
</select>
```

---

### 4.7.2 多个参数

推荐加 `@Param`：

```java
List<Book> listByTypeAndName(@Param("type") String type,
                             @Param("name") String name);
```

XML：

```xml
<select id="listByTypeAndName" resultType="Book">
    select * from tbl_book
    where type = #{type}
      and name like concat('%', #{name}, '%')
</select>
```

---

### 4.7.3 POJO 参数

```java
int save(Book book);
```

XML：

```xml
<insert id="save">
    insert into tbl_book(type, name, description)
    values(#{type}, #{name}, #{description})
</insert>
```

---

### 4.7.4 Map 参数

```java
List<Book> listByMap(Map<String, Object> params);
```

XML：

```xml
<select id="listByMap" resultType="Book">
    select * from tbl_book
    where type = #{type}
</select>
```

---

## 4.8 返回结果映射

### 4.8.1 `resultType`

当数据库字段名和 Java 属性名一致时，可以用 `resultType`：

```xml
<select id="getById" resultType="Book">
    select id, type, name, description
    from tbl_book
    where id = #{id}
</select>
```

---

### 4.8.2 字段名与属性名不一致

例如表字段为 `user_name`，Java 属性为 `userName`。

方式一：SQL 起别名。

```xml
<select id="getById" resultType="User">
    select id, user_name as userName
    from tb_user
    where id = #{id}
</select>
```

方式二：开启驼峰映射。

```xml
<settings>
    <setting name="mapUnderscoreToCamelCase" value="true"/>
</settings>
```

方式三：使用 `resultMap`。

```xml
<resultMap id="userResultMap" type="User">
    <id property="id" column="id"/>
    <result property="userName" column="user_name"/>
</resultMap>

<select id="getById" resultMap="userResultMap">
    select id, user_name
    from tb_user
    where id = #{id}
</select>
```

---

## 4.9 `resultMap` 高级映射

### 4.9.1 一对一映射

```java
public class Order {
    private Integer id;
    private User user;
}
```

XML：

```xml
<resultMap id="orderMap" type="Order">
    <id property="id" column="order_id"/>
    <association property="user" javaType="User">
        <id property="id" column="user_id"/>
        <result property="name" column="user_name"/>
    </association>
</resultMap>
```

---

### 4.9.2 一对多映射

```java
public class User {
    private Integer id;
    private String name;
    private List<Order> orders;
}
```

XML：

```xml
<resultMap id="userOrderMap" type="User">
    <id property="id" column="user_id"/>
    <result property="name" column="user_name"/>

    <collection property="orders" ofType="Order">
        <id property="id" column="order_id"/>
        <result property="name" column="order_name"/>
    </collection>
</resultMap>
```

---

## 4.10 动态 SQL

复杂查询中，经常需要根据参数是否为空动态拼接 SQL。

### 4.10.1 `if`

```xml
<select id="list" resultType="Book">
    select * from tbl_book
    where 1 = 1
    <if test="type != null and type != ''">
        and type = #{type}
    </if>
    <if test="name != null and name != ''">
        and name like concat('%', #{name}, '%')
    </if>
</select>
```

---

### 4.10.2 `where`

`where` 可以自动处理多余的 `and`。

```xml
<select id="list" resultType="Book">
    select * from tbl_book
    <where>
        <if test="type != null and type != ''">
            and type = #{type}
        </if>
        <if test="name != null and name != ''">
            and name like concat('%', #{name}, '%')
        </if>
    </where>
</select>
```

---

### 4.10.3 `set`

用于动态更新：

```xml
<update id="update">
    update tbl_book
    <set>
        <if test="type != null">type = #{type},</if>
        <if test="name != null">name = #{name},</if>
        <if test="description != null">description = #{description},</if>
    </set>
    where id = #{id}
</update>
```

---

### 4.10.4 `foreach`

批量删除：

```xml
<delete id="deleteByIds">
    delete from tbl_book
    where id in
    <foreach collection="ids" item="id" open="(" separator="," close=")">
        #{id}
    </foreach>
</delete>
```

Mapper：

```java
int deleteByIds(@Param("ids") List<Integer> ids);
```

---

### 4.10.5 `sql` 与 `include`

复用 SQL 片段：

```xml
<sql id="bookColumns">
    id, type, name, description
</sql>

<select id="getAll" resultType="Book">
    select <include refid="bookColumns"/>
    from tbl_book
</select>
```

---

## 4.11 主键回填

插入后获取自增主键：

```java
@Insert("insert into tbl_book(type, name, description) values(#{type}, #{name}, #{description})")
@Options(useGeneratedKeys = true, keyProperty = "id")
int save(Book book);
```

XML 写法：

```xml
<insert id="save" useGeneratedKeys="true" keyProperty="id">
    insert into tbl_book(type, name, description)
    values(#{type}, #{name}, #{description})
</insert>
```

---

## 4.12 MyBatis 缓存

### 4.12.1 一级缓存

一级缓存默认开启，作用域是同一个 `SqlSession`。

在 SSM 中，由于通常每次请求都会使用不同的 `SqlSession`，所以不要过度依赖一级缓存。

### 4.12.2 二级缓存

二级缓存作用域是 `namespace`，需要手动开启。

```xml
<cache/>
```

注意：

1. 二级缓存容易带来数据一致性问题。
2. 增删改会清空相关缓存。
3. 实际项目中更常使用 `Redis` 做缓存。

---

## 4.13 分页

传统方式：

```sql
select * from tbl_book limit #{offset}, #{pageSize}
```

实际项目中常用分页插件，如 `PageHelper`。

依赖示例：

```xml
<dependency>
    <groupId>com.github.pagehelper</groupId>
    <artifactId>pagehelper</artifactId>
    <version>5.3.3</version>
</dependency>
```

使用示例：

```java
PageHelper.startPage(pageNum, pageSize);
List<Book> list = bookMapper.getAll();
PageInfo<Book> pageInfo = new PageInfo<>(list);
```

---

## 4.14 MyBatis 常见问题

### 4.14.1 `Invalid bound statement`

常见原因：

1. Mapper 接口方法名和 XML 中 `id` 不一致。
2. XML 的 `namespace` 不等于 Mapper 接口全限定名。
3. XML 没有被加载。
4. Mapper 扫描包配置错误。
5. 注解和 XML 混用时映射冲突。

### 4.14.2 查询结果字段为 `null`

常见原因：

1. 数据库字段名和实体属性名不一致。
2. 没有配置驼峰映射。
3. 没有使用 `resultMap`。
4. SQL 查询字段缺失。

### 4.14.3 Mapper 注入失败

常见原因：

1. `MapperScannerConfigurer` 扫描路径错误。
2. Mapper 接口不在扫描包下。
3. 配置类没有被 `@Import`。
4. Spring 容器和 SpringMVC 容器扫描范围混乱。

---

# 第 5 章：SSM 整合配置

## 5.1 整合目标

把 `Spring`、`SpringMVC`、`MyBatis` 整合在一个 Maven Web 项目中，实现：

1. `Spring` 管理 `Service`、事务、数据源、MyBatis。
2. `SpringMVC` 管理 `Controller`、请求映射、JSON 响应、拦截器。
3. `MyBatis` 管理 Mapper 接口代理和 SQL 执行。
4. `Tomcat` 负责运行 Web 应用。

---

## 5.2 版本说明

原始笔记使用的是：

```text
Spring 5.2.10.RELEASE
MyBatis 3.5.6
mybatis-spring 1.3.0
MySQL Connector/J 5.1.46
javax.servlet-api 3.1.0
```

这套配置适合学习传统 SSM 和老项目维护。

注意：

> 如果使用 `Spring 6+` 或 `SpringBoot 3+`，Servlet 包名从 `javax.servlet.*` 迁移到 `jakarta.servlet.*`，很多依赖和代码需要对应调整。传统 SSM 入门建议先使用 `Spring 5 + javax.servlet` 体系，避免迁移问题干扰学习。

---

## 5.3 Maven 依赖

基础依赖：

```xml
<dependencies>
    <!-- SpringMVC -->
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-webmvc</artifactId>
        <version>5.2.10.RELEASE</version>
    </dependency>

    <!-- Spring JDBC 与事务 -->
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-jdbc</artifactId>
        <version>5.2.10.RELEASE</version>
    </dependency>

    <!-- Spring Test -->
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-test</artifactId>
        <version>5.2.10.RELEASE</version>
        <scope>test</scope>
    </dependency>

    <!-- MyBatis -->
    <dependency>
        <groupId>org.mybatis</groupId>
        <artifactId>mybatis</artifactId>
        <version>3.5.6</version>
    </dependency>

    <!-- MyBatis 与 Spring 整合 -->
    <dependency>
        <groupId>org.mybatis</groupId>
        <artifactId>mybatis-spring</artifactId>
        <version>1.3.0</version>
    </dependency>

    <!-- MySQL 驱动 -->
    <dependency>
        <groupId>mysql</groupId>
        <artifactId>mysql-connector-java</artifactId>
        <version>5.1.46</version>
    </dependency>

    <!-- Druid 数据源 -->
    <dependency>
        <groupId>com.alibaba</groupId>
        <artifactId>druid</artifactId>
        <version>1.1.16</version>
    </dependency>

    <!-- Servlet API，运行时由 Tomcat 提供 -->
    <dependency>
        <groupId>javax.servlet</groupId>
        <artifactId>javax.servlet-api</artifactId>
        <version>3.1.0</version>
        <scope>provided</scope>
    </dependency>

    <!-- JSON 转换 -->
    <dependency>
        <groupId>com.fasterxml.jackson.core</groupId>
        <artifactId>jackson-databind</artifactId>
        <version>2.9.0</version>
    </dependency>

    <!-- JUnit -->
    <dependency>
        <groupId>junit</groupId>
        <artifactId>junit</artifactId>
        <version>4.12</version>
        <scope>test</scope>
    </dependency>
</dependencies>
```

---

## 5.4 `jdbc.properties`

```properties
jdbc.driver=com.mysql.jdbc.Driver
jdbc.url=jdbc:mysql://localhost:3306/ssm_db?useSSL=false&useUnicode=true&characterEncoding=utf8
jdbc.username=root
jdbc.password=123456
```

如果使用新版 MySQL 驱动，驱动类通常为：

```properties
jdbc.driver=com.mysql.cj.jdbc.Driver
```

---

## 5.5 `JdbcConfig`

```java
public class JdbcConfig {

    @Value("${jdbc.driver}")
    private String driver;

    @Value("${jdbc.url}")
    private String url;

    @Value("${jdbc.username}")
    private String username;

    @Value("${jdbc.password}")
    private String password;

    @Bean
    public DataSource dataSource() {
        DruidDataSource dataSource = new DruidDataSource();

        dataSource.setDriverClassName(driver);
        dataSource.setUrl(url);
        dataSource.setUsername(username);
        dataSource.setPassword(password);

        return dataSource;
    }

    @Bean
    public PlatformTransactionManager transactionManager(DataSource dataSource) {
        DataSourceTransactionManager transactionManager = new DataSourceTransactionManager();
        transactionManager.setDataSource(dataSource);
        return transactionManager;
    }
}
```

说明：

- `DataSource` 用于获取数据库连接。
- `PlatformTransactionManager` 是 Spring 事务管理的核心接口。
- `DataSourceTransactionManager` 适合 JDBC / MyBatis 单数据源事务。

---

## 5.6 `MyBatisConfig`

```java
public class MyBatisConfig {

    @Bean
    public SqlSessionFactoryBean sqlSessionFactory(DataSource dataSource) {
        SqlSessionFactoryBean factoryBean = new SqlSessionFactoryBean();

        factoryBean.setDataSource(dataSource);
        factoryBean.setTypeAliasesPackage("com.blog.domain");

        return factoryBean;
    }

    @Bean
    public MapperScannerConfigurer mapperScannerConfigurer() {
        MapperScannerConfigurer configurer = new MapperScannerConfigurer();
        configurer.setBasePackage("com.blog.dao");
        return configurer;
    }
}
```

说明：

- `SqlSessionFactoryBean` 用于创建 `SqlSessionFactory`。
- `setTypeAliasesPackage("com.blog.domain")` 可以让 MyBatis 在 XML 中直接使用 `Book`，而不必写全限定类名。
- `MapperScannerConfigurer` 会扫描 Mapper 接口，并为接口创建代理对象交给 Spring 容器。

---

## 5.7 `SpringConfig`

```java
@Configuration
@ComponentScan("com.blog.service")
@PropertySource("classpath:jdbc.properties")
@EnableTransactionManagement
@Import({JdbcConfig.class, MyBatisConfig.class})
public class SpringConfig {
}
```

说明：

| 注解 | 作用 |
|---|---|
| `@Configuration` | 当前类是配置类 |
| `@ComponentScan` | 扫描 Service 组件 |
| `@PropertySource` | 加载外部属性文件 |
| `@EnableTransactionManagement` | 开启声明式事务 |
| `@Import` | 导入其他配置类 |

注意：

> `SpringConfig` 主要管理非 Web 层对象，例如 `Service`、`DataSource`、`Mapper`、事务等。

---

## 5.8 `SpringMvcConfig`

```java
@Configuration
@ComponentScan({"com.blog.controller", "com.blog.config"})
@EnableWebMvc
public class SpringMvcConfig implements WebMvcConfigurer {

    @Override
    public void addResourceHandlers(ResourceHandlerRegistry registry) {
        registry.addResourceHandler("/pages/**").addResourceLocations("/pages/");
        registry.addResourceHandler("/css/**").addResourceLocations("/css/");
        registry.addResourceHandler("/js/**").addResourceLocations("/js/");
        registry.addResourceHandler("/plugins/**").addResourceLocations("/plugins/");
    }
}
```

说明：

| 注解 / 接口 | 作用 |
|---|---|
| `@Configuration` | 当前类是配置类 |
| `@ComponentScan` | 扫描 Controller 与 Web 配置类 |
| `@EnableWebMvc` | 开启 SpringMVC 注解驱动 |
| `WebMvcConfigurer` | 扩展 SpringMVC 配置 |

注意：

> `SpringMvcConfig` 主要管理 Web 层对象，例如 `Controller`、`Interceptor`、静态资源映射等。

---

## 5.9 `ServletContainersInitConfig`

```java
public class ServletContainersInitConfig
        extends AbstractAnnotationConfigDispatcherServletInitializer {

    @Override
    protected Class<?>[] getRootConfigClasses() {
        return new Class[]{SpringConfig.class};
    }

    @Override
    protected Class<?>[] getServletConfigClasses() {
        return new Class[]{SpringMvcConfig.class};
    }

    @Override
    protected String[] getServletMappings() {
        return new String[]{"/"};
    }

    @Override
    protected Filter[] getServletFilters() {
        CharacterEncodingFilter filter = new CharacterEncodingFilter();
        filter.setEncoding("UTF-8");
        filter.setForceEncoding(true);
        return new Filter[]{filter};
    }
}
```

说明：

- `getRootConfigClasses()`：加载 Spring 根容器。
- `getServletConfigClasses()`：加载 SpringMVC 子容器。
- `getServletMappings()`：配置前端控制器拦截路径。
- `getServletFilters()`：配置过滤器，这里用于解决中文乱码。

---

## 5.10 Spring 根容器与 SpringMVC 子容器

传统 SSM 中，通常有两个容器：

| 容器 | 配置类 | 管理对象 |
|---|---|---|
| Spring 根容器 | `SpringConfig` | `Service`、`Dao`、`DataSource`、事务 |
| SpringMVC 子容器 | `SpringMvcConfig` | `Controller`、拦截器、Web 配置 |

注意：

1. `Controller` 应该放在 SpringMVC 子容器中。
2. `Service` 应该放在 Spring 根容器中。
3. 不要让两个容器重复扫描所有包，否则可能导致 Bean 重复或层次混乱。

推荐扫描方式：

```java
@ComponentScan("com.blog.service")
```

```java
@ComponentScan({"com.blog.controller", "com.blog.config"})
```

---

# 第 6 章：图书管理 CRUD 案例重构

## 6.1 建表 SQL

```sql
create database ssm_db character set utf8mb4;

use ssm_db;

create table tbl_book
(
    id          int primary key auto_increment,
    type        varchar(20),
    name        varchar(50),
    description varchar(255)
);

insert into tbl_book(id, type, name, description)
values
    (1, '计算机理论', 'Spring实战 第五版', 'Spring入门经典教程，深入理解Spring原理技术内幕'),
    (2, '计算机理论', 'Spring 5核心原理与30个类手写实践', '十年沉淀之作，手写Spring精华思想'),
    (3, '计算机理论', 'Spring 5设计模式', '深入Spring源码剖析Spring源码中蕴含的设计模式');
```

---

## 6.2 实体类 `Book`

```java
public class Book {

    private Integer id;
    private String type;
    private String name;
    private String description;

    public Integer getId() {
        return id;
    }

    public void setId(Integer id) {
        this.id = id;
    }

    public String getType() {
        return type;
    }

    public void setType(String type) {
        this.type = type;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

	public String getDescription() {
        return description;
    }

    public void setDescription(String description) {
        this.description = description;
    }
}
```

如果使用 `Lombok`，可以简化为：

```java
@Data
@NoArgsConstructor
@AllArgsConstructor
public class Book {
    private Integer id;
    private String type;
    private String name;
    private String description;
}
```

---

## 6.3 Dao / Mapper 接口

```java
public interface BookDao {

    @Insert("insert into tbl_book(type, name, description) values(#{type}, #{name}, #{description})")
    int save(Book book);

    @Update("update tbl_book set type=#{type}, name=#{name}, description=#{description} where id=#{id}")
    int update(Book book);

    @Delete("delete from tbl_book where id=#{id}")
    int delete(Integer id);

    @Select("select id, type, name, description from tbl_book where id=#{id}")
    Book getById(Integer id);

    @Select("select id, type, name, description from tbl_book")
    List<Book> getAll();
}
```

原始笔记中 `getById()` 和 `getAll()` 返回值写成了 `void`，这是不合理的，应该改为：

```java
Book getById(Integer id);
List<Book> getAll();
```

---

## 6.4 Service 接口

```java
public interface BookService {

    boolean save(Book book);

    boolean update(Book book);

    boolean delete(Integer id);

    Book getById(Integer id);

    List<Book> getAll();
}
```

---

## 6.5 Service 实现类

```java
@Service
@Transactional(rollbackFor = Exception.class)
public class BookServiceImpl implements BookService {

    private final BookDao bookDao;

    public BookServiceImpl(BookDao bookDao) {
        this.bookDao = bookDao;
    }

    @Override
    public boolean save(Book book) {
        return bookDao.save(book) > 0;
    }

    @Override
    public boolean update(Book book) {
        return bookDao.update(book) > 0;
    }

    @Override
    public boolean delete(Integer id) {
        return bookDao.delete(id) > 0;
    }

    @Override
    @Transactional(readOnly = true)
    public Book getById(Integer id) {
        return bookDao.getById(id);
    }

    @Override
    @Transactional(readOnly = true)
    public List<Book> getAll() {
        return bookDao.getAll();
    }
}
```

说明：

1. 增删改方法需要事务。
2. 查询方法可以标记 `readOnly = true`。
3. 构造器注入比属性注入更规范。

---

## 6.6 Controller

```java
@RestController
@RequestMapping("/books")
public class BookController {

    private final BookService bookService;

    public BookController(BookService bookService) {
        this.bookService = bookService;
    }

    @PostMapping
    public Result save(@RequestBody Book book) {
        boolean flag = bookService.save(book);
        return flag ? Result.success(true) : Result.error("新增失败");
    }

    @PutMapping
    public Result update(@RequestBody Book book) {
        boolean flag = bookService.update(book);
        return flag ? Result.success(true) : Result.error("修改失败");
    }

    @DeleteMapping("/{id}")
    public Result delete(@PathVariable Integer id) {
        boolean flag = bookService.delete(id);
        return flag ? Result.success(true) : Result.error("删除失败");
    }

    @GetMapping("/{id}")
    public Result getById(@PathVariable Integer id) {
        Book book = bookService.getById(id);
        return book != null ? Result.success(book) : Result.error("数据不存在");
    }

    @GetMapping
    public Result getAll() {
        List<Book> list = bookService.getAll();
        return Result.success(list);
    }
}
```

---

## 6.7 单元测试

```java
@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration(classes = SpringConfig.class)
public class BookServiceTest {

    @Autowired
    private BookService bookService;

    @Test
    public void testGetById() {
        Book book = bookService.getById(1);
        System.out.println(book);
    }

    @Test
    public void testGetAll() {
        List<Book> list = bookService.getAll();
        list.forEach(System.out::println);
    }

    @Test
    public void testSave() {
        Book book = new Book();
        book.setType("测试分类");
        book.setName("测试书名");
        book.setDescription("测试描述");

        boolean result = bookService.save(book);
        System.out.println(result);
    }
}
```

---

## 6.8 Postman 测试

### 新增图书

请求：

```text
POST http://localhost:8080/books
```

请求体：

```json
{
  "type": "类别测试数据",
  "name": "书名测试数据",
  "description": "描述测试数据"
}
```

---

### 修改图书

请求：

```text
PUT http://localhost:8080/books
```

请求体：

```json
{
  "id": 1,
  "type": "类别测试数据",
  "name": "书名测试数据9527",
  "description": "描述测试数据"
}
```

---

### 删除图书

请求：

```text
DELETE http://localhost:8080/books/1
```

---

### 查询单个图书

请求：

```text
GET http://localhost:8080/books/1
```

---

### 查询所有图书

请求：

```text
GET http://localhost:8080/books
```

---

# 第 7 章：统一响应结果封装

## 7.1 为什么需要统一响应

原始笔记中已经指出问题：

- 新增、修改、删除返回 `boolean`。
- 查询单个返回对象。
- 查询所有返回集合。
- 异常时可能返回 HTML 错误页面。

前端解析时会非常混乱。

所以应该统一返回结构：

```json
{
  "code": 1,
  "msg": "success",
  "data": {}
}
```

---

## 7.2 推荐的 `Result` 类

相比原始笔记中的 `20011`、`20021` 这种编码，学习阶段可以保留；实际项目中建议更简单清晰。

```java
public class Result {

    private Integer code;
    private String msg;
    private Object data;

    public Result() {
    }

    public Result(Integer code, String msg, Object data) {
        this.code = code;
        this.msg = msg;
        this.data = data;
    }

    public static Result success() {
        return new Result(1, "success", null);
    }

    public static Result success(Object data) {
        return new Result(1, "success", data);
    }

    public static Result error(String msg) {
        return new Result(0, msg, null);
    }

    public Integer getCode() {
        return code;
    }

    public void setCode(Integer code) {
        this.code = code;
    }

    public String getMsg() {
        return msg;
    }

    public void setMsg(String msg) {
        this.msg = msg;
    }

    public Object getData() {
        return data;
    }

    public void setData(Object data) {
        this.data = data;
    }
}
```

如果希望区分更多业务状态，可以定义枚举：

```java
public enum ResultCode {

    SUCCESS(1, "success"),
    ERROR(0, "error"),
    PARAM_ERROR(400, "参数错误"),
    NOT_FOUND(404, "资源不存在"),
    SYSTEM_ERROR(500, "系统繁忙");

    private final Integer code;
    private final String message;

    ResultCode(Integer code, String message) {
        this.code = code;
        this.message = message;
    }
}
```

---

## 7.3 原笔记中的 Code 编码方式

原笔记中的编码规则：

```java
public class Code {
    public static final Integer SAVE_OK = 20011;
    public static final Integer UPDATE_OK = 20021;
    public static final Integer DELETE_OK = 20031;
    public static final Integer GET_OK = 20041;

    public static final Integer SAVE_ERR = 20010;
    public static final Integer UPDATE_ERR = 20020;
    public static final Integer DELETE_ERR = 20030;
    public static final Integer GET_ERR = 20040;

    public static final Integer SYSTEM_ERR = 50001;
    public static final Integer SYSTEM_TIMEOUT_ERR = 50002;
    public static final Integer SYSTEM_UNKNOWN_ERR = 59999;

    public static final Integer BUSINESS_ERR = 60001;
}
```

这种方式适合教学演示，能体现“不同操作不同状态码”。  
但是实际项目中建议：

1. 不要设计过于复杂的编码规则。
2. 结合 HTTP 状态码。
3. 业务码要有文档说明。
4. 前后端必须约定统一协议。

---

# 第 8 章：统一异常处理

## 8.1 为什么需要统一异常处理

如果没有统一异常处理，后端出现异常时，前端可能收到：

1. Tomcat 默认错误页面。
2. SpringMVC 默认异常信息。
3. 不符合 `Result` 协议的响应。
4. 甚至暴露堆栈信息。

统一异常处理的目标：

1. 所有异常都返回统一结构。
2. 用户看到友好提示。
3. 后端记录详细日志。
4. 避免敏感信息泄露。
5. 区分业务异常和系统异常。

---

## 8.2 异常分类

| 异常类型 | 说明 | 示例 |
|---|---|---|
| 业务异常 | 用户行为或业务规则导致 | 用户名已存在、库存不足 |
| 系统异常 | 系统可预期但无法避免的问题 | 数据库超时、第三方服务不可用 |
| 未知异常 | 程序未考虑到的问题 | 空指针、数组越界 |

---

## 8.3 自定义业务异常

```java
public class BusinessException extends RuntimeException {

    private Integer code;

    public BusinessException(Integer code, String message) {
        super(message);
        this.code = code;
    }

    public Integer getCode() {
        return code;
    }
}
```

---

## 8.4 自定义系统异常

```java
public class SystemException extends RuntimeException {

    private Integer code;

    public SystemException(Integer code, String message) {
        super(message);
        this.code = code;
    }

    public SystemException(Integer code, String message, Throwable cause) {
        super(message, cause);
        this.code = code;
    }

    public Integer getCode() {
        return code;
    }
}
```

---

## 8.5 全局异常处理器

```java
@RestControllerAdvice
public class ProjectExceptionAdvice {

    @ExceptionHandler(BusinessException.class)
    public Result handleBusinessException(BusinessException ex) {
        return new Result(ex.getCode(), ex.getMessage(), null);
    }

    @ExceptionHandler(SystemException.class)
    public Result handleSystemException(SystemException ex) {
        // 实际项目中这里需要记录日志
        ex.printStackTrace();
        return new Result(ex.getCode(), "系统繁忙，请稍后再试", null);
    }

    @ExceptionHandler(Exception.class)
    public Result handleException(Exception ex) {
        // 实际项目中这里需要记录日志
        ex.printStackTrace();
        return new Result(500, "系统繁忙，请稍后再试", null);
    }
}
```

---

## 8.6 Service 中抛出业务异常

```java
@Override
public Book getById(Integer id) {
    if (id == null || id <= 0) {
        throw new BusinessException(400, "图书ID不合法");
    }

    Book book = bookDao.getById(id);

    if (book == null) {
        throw new BusinessException(404, "图书不存在");
    }

    return book;
}
```

---

## 8.7 异常处理最佳实践

1. `Controller` 层不建议到处写 `try-catch`。
2. 业务规则异常应主动抛出 `BusinessException`。
3. 系统异常可以包装成 `SystemException`。
4. 不要把详细堆栈直接返回给前端。
5. 后端日志要记录异常堆栈。
6. 前端只展示用户可理解的提示。
7. `@ExceptionHandler(Exception.class)` 应作为兜底处理。

---

# 第 9 章：前后台协议联调

## 9.1 联调目标

前端通过 `Vue + ElementUI + Axios` 调用后端 SSM 接口，实现：

1. 查询图书列表。
2. 新增图书。
3. 修改图书。
4. 删除图书。
5. 根据后端响应码提示成功或失败。

---

## 9.2 静态资源目录

```text
src/main/webapp
├── pages
│   └── books.html
├── css
│   └── style.css
├── js
│   ├── vue.js
│   └── axios-0.18.0.js
└── plugins
    └── elementui
```

如果 SpringMVC 拦截 `/`，需要配置静态资源放行。

---

## 9.3 页面加载查询列表

Vue 生命周期钩子：

```js
created() {
    this.getAll();
}
```

查询方法：

```js
getAll() {
    axios.get("/books").then((res) => {
        this.dataList = res.data.data;
    });
}
```

说明：

- `res.data` 是后端返回的 `Result` 对象。
- `res.data.data` 才是真正的图书列表。

---

## 9.4 新增功能

打开新增窗口：

```js
openSave() {
    this.dialogFormVisible = true;
    this.resetForm();
}
```

重置表单：

```js
resetForm() {
    this.formData = {};
}
```

提交新增：

```js
saveBook() {
    axios.post("/books", this.formData).then((res) => {
        if (res.data.code === 1) {
            this.dialogFormVisible = false;
            this.$message.success("添加成功");
        } else {
            this.$message.error(res.data.msg || "添加失败");
        }
    }).finally(() => {
        this.getAll();
    });
}
```

---

## 9.5 修改功能

打开编辑窗口并回显数据：

```js
openEdit(row) {
    axios.get("/books/" + row.id).then((res) => {
        if (res.data.code === 1) {
            this.formData = res.data.data;
            this.dialogFormVisible4Edit = true;
        } else {
            this.$message.error(res.data.msg);
        }
    });
}
```

提交修改：

```js
handleEdit() {
    axios.put("/books", this.formData).then((res) => {
        if (res.data.code === 1) {
            this.dialogFormVisible4Edit = false;
            this.$message.success("修改成功");
        } else {
            this.$message.error(res.data.msg || "修改失败");
        }
    }).finally(() => {
        this.getAll();
    });
}
```

---

## 9.6 删除功能

```js
deleteBook(row) {
    this.$confirm("此操作将永久删除当前数据，是否继续？", "提示", {
        type: "warning"
    }).then(() => {
        axios.delete("/books/" + row.id).then((res) => {
            if (res.data.code === 1) {
                this.$message.success("删除成功");
            } else {
                this.$message.error(res.data.msg || "删除失败");
            }
        }).finally(() => {
            this.getAll();
        });
    }).catch(() => {
        this.$message.info("取消删除操作");
    });
}
```

---

## 9.7 联调常见问题

### 问题一：前端请求 404

检查：

1. 请求路径是否正确。
2. `Controller` 上的 `@RequestMapping` 是否正确。
3. Tomcat 项目上下文路径是否包含项目名。
4. `DispatcherServlet` 是否拦截了对应路径。

### 问题二：后端接收不到 JSON

检查：

1. 前端是否使用 `axios.post()` 发送对象。
2. 请求头是否是 `application/json`。
3. Controller 参数是否加了 `@RequestBody`。
4. 是否引入了 `jackson-databind`。

### 问题三：中文乱码

检查：

1. 是否配置 `CharacterEncodingFilter`。
2. 请求和响应是否统一 `UTF-8`。
3. 数据库连接 URL 是否配置 `characterEncoding=utf8`。
4. 数据库表字符集是否为 `utf8mb4`。

---

# 第 10 章：SpringMVC 拦截器

## 10.1 拦截器是什么

拦截器 `Interceptor` 是 SpringMVC 提供的、用于动态拦截 Controller 方法调用的机制。

作用：

1. 在 Controller 方法执行前增强。
2. 在 Controller 方法执行后增强。
3. 决定是否放行请求。
4. 常用于登录校验、权限校验、日志记录、接口耗时统计。

---

## 10.2 拦截器与过滤器区别

| 对比项 | Filter | Interceptor |
|---|---|---|
| 所属规范 | Servlet 规范 | SpringMVC |
| 执行时机 | 请求进入 Servlet 前后 | Controller 方法前后 |
| 拦截范围 | 所有请求，包括静态资源 | SpringMVC 处理的请求 |
| 配置方式 | Servlet 配置 | SpringMVC 配置 |
| 是否能拿到 Controller 方法信息 | 不方便 | 可以通过 `HandlerMethod` 获取 |

---

## 10.3 自定义拦截器

```java
@Component
public class ProjectInterceptor implements HandlerInterceptor {

    @Override
    public boolean preHandle(HttpServletRequest request,
                             HttpServletResponse response,
                             Object handler) throws Exception {
        System.out.println("preHandle");
        return true;
    }

    @Override
    public void postHandle(HttpServletRequest request,
                           HttpServletResponse response,
                           Object handler,
                           ModelAndView modelAndView) throws Exception {
        System.out.println("postHandle");
    }

    @Override
    public void afterCompletion(HttpServletRequest request,
                                HttpServletResponse response,
                                Object handler,
                                Exception ex) throws Exception {
        System.out.println("afterCompletion");
    }
}
```

---

## 10.4 三个方法执行时机

| 方法 | 执行时机 | 是否常用 |
|---|---|---|
| `preHandle()` | Controller 方法执行前 | 最常用 |
| `postHandle()` | Controller 方法执行后，视图渲染前 | 前后端分离项目中较少 |
| `afterCompletion()` | 请求完成后 | 可用于资源清理、日志 |

---

## 10.5 配置拦截器

```java
@Configuration
public class SpringMvcConfig implements WebMvcConfigurer {

    private final ProjectInterceptor projectInterceptor;

    public SpringMvcConfig(ProjectInterceptor projectInterceptor) {
        this.projectInterceptor = projectInterceptor;
    }

    @Override
    public void addInterceptors(InterceptorRegistry registry) {
        registry.addInterceptor(projectInterceptor)
                .addPathPatterns("/books", "/books/*")
                .excludePathPatterns("/pages/**", "/css/**", "/js/**");
    }
}
```

---

## 10.6 `preHandle()` 返回值

```java
@Override
public boolean preHandle(HttpServletRequest request,
                         HttpServletResponse response,
                         Object handler) throws Exception {
    return true;
}
```

返回值含义：

| 返回值 | 含义 |
|---|---|
| `true` | 放行，继续执行 Controller |
| `false` | 拦截，请求不再进入 Controller |

---

## 10.7 获取 Controller 方法信息

```java
@Override
public boolean preHandle(HttpServletRequest request,
                         HttpServletResponse response,
                         Object handler) throws Exception {

    if (handler instanceof HandlerMethod) {
        HandlerMethod handlerMethod = (HandlerMethod) handler;
        String methodName = handlerMethod.getMethod().getName();
        Class<?> beanType = handlerMethod.getBeanType();

        System.out.println("Controller类：" + beanType.getName());
        System.out.println("方法名：" + methodName);
    }

    return true;
}
```

---

## 10.8 登录校验拦截器示例

```java
@Component
public class LoginInterceptor implements HandlerInterceptor {

    @Override
    public boolean preHandle(HttpServletRequest request,
                             HttpServletResponse response,
                             Object handler) throws Exception {

        HttpSession session = request.getSession(false);

        if (session != null && session.getAttribute("loginUser") != null) {
            return true;
        }

        response.setContentType("application/json;charset=UTF-8");
        response.getWriter().write("{\"code\":0,\"msg\":\"请先登录\"}");
        return false;
    }
}
```

配置：

```java
@Override
public void addInterceptors(InterceptorRegistry registry) {
    registry.addInterceptor(loginInterceptor)
            .addPathPatterns("/**")
            .excludePathPatterns("/login", "/pages/login.html", "/css/**", "/js/**");
}
```

---

## 10.9 多个拦截器执行顺序

如果配置两个拦截器：

```java
registry.addInterceptor(interceptor1).addPathPatterns("/books/**");
registry.addInterceptor(interceptor2).addPathPatterns("/books/**");
```

执行顺序：

```text
interceptor1.preHandle()
interceptor2.preHandle()
Controller 方法
interceptor2.postHandle()
interceptor1.postHandle()
interceptor2.afterCompletion()
interceptor1.afterCompletion()
```

规律：

1. `preHandle()` 按配置顺序执行。
2. `postHandle()` 按配置相反顺序执行。
3. `afterCompletion()` 按配置相反顺序执行。
4. 如果某个 `preHandle()` 返回 `false`，后续拦截器和 Controller 不再执行。

---

# 第 11 章：SSM 项目常见问题与排错

## 11.1 Controller 无法访问

检查：

1. `SpringMvcConfig` 是否扫描了 `controller` 包。
2. `ServletContainersInitConfig` 是否配置了 `SpringMvcConfig`。
3. `getServletMappings()` 是否返回了 `"/"`。
4. 请求路径是否和 `@RequestMapping` 一致。
5. Tomcat 部署路径是否正确。

---

## 11.2 Service 注入失败

检查：

1. `SpringConfig` 是否扫描了 `service` 包。
2. 实现类是否加了 `@Service`。
3. Controller 中注入的是接口还是实现类。
4. 是否存在多个同类型 Bean 造成歧义。
5. Spring 根容器是否正确加载。

---

## 11.3 Mapper 注入失败

检查：

1. `MapperScannerConfigurer` 的 `basePackage` 是否正确。
2. Mapper 接口是否在扫描包下。
3. `MyBatisConfig` 是否被 `@Import`。
4. `SqlSessionFactoryBean` 是否配置了 `DataSource`。
5. Mapper 接口是否写成了类。

---

## 11.4 数据库连接失败

检查：

1. `jdbc.properties` 是否在 `resources` 目录下。
2. `@PropertySource` 路径是否正确。
3. 数据库地址、端口、用户名、密码是否正确。
4. MySQL 服务是否启动。
5. 驱动版本和驱动类名是否匹配。
6. 数据库是否存在。
7. 用户是否有权限。

---

## 11.5 JSON 转换失败

检查：

1. 是否引入 `jackson-databind`。
2. Controller 是否使用 `@RestController` 或 `@ResponseBody`。
3. 返回对象是否有 getter 方法。
4. 是否出现循环引用。
5. 日期格式是否无法序列化。

---

## 11.6 事务不生效

检查：

1. 是否配置了 `@EnableTransactionManagement`。
2. 是否注册了 `PlatformTransactionManager`。
3. 方法是否由 Spring Bean 调用。
4. 是否同类内部调用。
5. 异常是否被吞掉。
6. 是否抛出的是受检异常但没有配置 `rollbackFor`。
7. 方法是否是 `public`。
8. 数据库表引擎是否支持事务，例如 MySQL 需要 `InnoDB`。

---

## 11.7 静态资源 404

检查：

1. 资源是否放在 `webapp` 目录下。
2. 是否配置了 `addResourceHandlers()`。
3. 路径是否写错。
4. `DispatcherServlet` 是否拦截了静态资源。
5. 浏览器访问路径是否带了项目上下文路径。

---

## 11.8 中文乱码

检查：

1. `CharacterEncodingFilter` 是否配置。
2. `filter.setForceEncoding(true)` 是否设置。
3. 页面 `<meta charset="utf-8">` 是否配置。
4. 数据库连接 URL 是否配置编码。
5. 数据库、表、字段字符集是否为 `utf8mb4`。

---

# 第 12 章：SSM 高频面试题

## 12.1 SSM 三个框架分别负责什么

答：

- `Spring`：负责对象创建、依赖注入、AOP、事务管理。
- `SpringMVC`：负责接收请求、参数绑定、调用业务层、响应 JSON。
- `MyBatis`：负责 SQL 映射、执行 SQL、结果映射。

---

## 12.2 Spring IoC 和 DI 的区别

答：

- `IoC` 是思想：对象控制权交给容器。
- `DI` 是实现方式：容器把依赖对象注入到需要的 Bean 中。

---

## 12.3 `@Autowired` 和 `@Resource` 的区别

| 注解 | 来源 | 默认装配方式 |
|---|---|---|
| `@Autowired` | Spring | 按类型 |
| `@Resource` | JDK / Java EE | 按名称 |

`@Autowired` 可以配合 `@Qualifier` 指定名称：

```java
@Autowired
@Qualifier("bookServiceImpl")
private BookService bookService;
```

`@Resource` 可以直接指定名称：

```java
@Resource(name = "bookServiceImpl")
private BookService bookService;
```

---

## 12.4 Spring AOP 的底层原理

答：

Spring AOP 底层主要基于动态代理：

1. 如果目标类实现了接口，默认可以使用 `JDK` 动态代理。
2. 如果目标类没有实现接口，可以使用 `CGLIB` 代理。
3. 代理对象在调用目标方法前后织入增强逻辑。

---

## 12.5 Spring 事务为什么会失效

答：

常见原因：

1. 方法不是 `public`。
2. 同类内部方法调用。
3. 异常被捕获没有抛出。
4. 抛出受检异常但没有设置 `rollbackFor`。
5. 对象不是 Spring 容器管理的 Bean。
6. 没有配置事务管理器。
7. 数据库表不支持事务。

---

## 12.6 SpringMVC 的执行流程

答：

1. 请求进入 `DispatcherServlet`。
2. `HandlerMapping` 根据路径找到处理器。
3. `HandlerAdapter` 调用 Controller 方法。
4. Controller 调用 Service 完成业务。
5. 返回对象或视图。
6. 如果是对象，使用 `HttpMessageConverter` 转成 JSON。
7. 响应客户端。

---

## 12.7 `@RequestParam`、`@PathVariable`、`@RequestBody` 区别

| 注解 | 接收位置 | 示例 |
|---|---|---|
| `@RequestParam` | 查询参数 / 表单参数 | `/books?id=1` |
| `@PathVariable` | 路径参数 | `/books/1` |
| `@RequestBody` | 请求体 JSON | `{"id":1}` |

---

## 12.8 Filter 和 Interceptor 区别

答：

- `Filter` 属于 Servlet 规范，拦截范围更大。
- `Interceptor` 属于 SpringMVC，只拦截进入 SpringMVC 的请求。
- `Filter` 常用于编码、跨域、基础登录过滤。
- `Interceptor` 常用于权限校验、Controller 日志、接口耗时统计。

---

## 12.9 MyBatis 中 `#{}` 和 `${}` 的区别

答：

- `#{}` 使用预编译，占位符形式，能防止 SQL 注入。
- `${}` 是字符串拼接，不能防止 SQL 注入。
- 普通参数必须优先使用 `#{}`。
- 表名、字段名、排序字段等特殊场景才可能使用 `${}`，并且要做白名单校验。

---

## 12.10 MyBatis 一级缓存和二级缓存

答：

- 一级缓存默认开启，作用域是同一个 `SqlSession`。
- 二级缓存作用域是同一个 `namespace`，需要配置开启。
- 实际项目中由于数据一致性问题，二级缓存使用要谨慎，通常更推荐使用 `Redis`。

---

## 12.11 SSM 中如何处理异常

答：

使用 `@RestControllerAdvice` + `@ExceptionHandler` 统一处理异常。

```java
@RestControllerAdvice
public class GlobalExceptionHandler {

    @ExceptionHandler(Exception.class)
    public Result handleException(Exception ex) {
        return Result.error("系统繁忙，请稍后再试");
    }
}
```

---

## 12.12 SSM 中如何实现统一返回

答：

定义统一响应类 `Result`，所有 Controller 方法都返回该类型。

```java
public class Result {
    private Integer code;
    private String msg;
    private Object data;
}
```

---

# 附录：常见注解速查表

## A.1 Spring 常见注解

| 注解 | 作用 |
|---|---|
| `@Component` | 通用 Bean |
| `@Controller` | Controller Bean |
| `@Service` | Service Bean |
| `@Repository` | Dao Bean |
| `@Autowired` | 自动注入 |
| `@Qualifier` | 指定 Bean 名称 |
| `@Resource` | 按名称或类型注入 |
| `@Configuration` | 配置类 |
| `@Bean` | 注册 Bean |
| `@ComponentScan` | 组件扫描 |
| `@PropertySource` | 加载配置文件 |
| `@Import` | 导入配置类 |
| `@Value` | 注入配置值 |
| `@Scope` | 设置 Bean 作用域 |
| `@Lazy` | 延迟初始化 |

---

## A.2 Spring AOP 与事务注解

| 注解 | 作用 |
|---|---|
| `@Aspect` | 声明切面类 |
| `@Before` | 前置通知 |
| `@After` | 后置通知 |
| `@AfterReturning` | 返回后通知 |
| `@AfterThrowing` | 异常后通知 |
| `@Around` | 环绕通知 |
| `@Pointcut` | 定义切入点 |
| `@EnableTransactionManagement` | 开启事务管理 |
| `@Transactional` | 声明事务 |

---

## A.3 SpringMVC 常见注解

| 注解 | 作用 |
|---|---|
| `@RequestMapping` | 请求映射 |
| `@GetMapping` | GET 请求 |
| `@PostMapping` | POST 请求 |
| `@PutMapping` | PUT 请求 |
| `@DeleteMapping` | DELETE 请求 |
| `@RequestParam` | 请求参数 |
| `@PathVariable` | 路径参数 |
| `@RequestBody` | 请求体 JSON |
| `@ResponseBody` | 响应体 |
| `@RestController` | REST 控制器 |
| `@ControllerAdvice` | Controller 增强 |
| `@RestControllerAdvice` | REST Controller 增强 |
| `@ExceptionHandler` | 异常处理 |
| `@DateTimeFormat` | 日期格式转换 |
| `@Validated` | 参数校验 |

---

## A.4 MyBatis 常见注解

| 注解 | 作用 |
|---|---|
| `@Select` | 查询 SQL |
| `@Insert` | 新增 SQL |
| `@Update` | 修改 SQL |
| `@Delete` | 删除 SQL |
| `@Param` | 指定参数名 |
| `@Options` | 配置选项，例如主键回填 |
| `@Results` | 结果映射 |
| `@Result` | 字段映射 |
| `@One` | 一对一查询 |
| `@Many` | 一对多查询 |

---

## A.5 SSM 重点记忆口诀

```text
Spring 管对象：
    IoC、DI、Bean、AOP、事务

SpringMVC 管请求：
    DispatcherServlet、Controller、参数绑定、JSON、异常处理、拦截器

MyBatis 管 SQL：
    Mapper、SqlSessionFactory、动态 SQL、ResultMap、缓存

SSM 整合核心：
    SpringConfig 管 Service / Dao / 事务
    SpringMvcConfig 管 Controller / Web
    MyBatisConfig 管 SqlSessionFactory / Mapper 扫描
```

---

## A.6 学习建议

1. 不要只背注解，要能说出注解背后的对象和流程。
2. `SSM` 整合重点不是代码量，而是容器边界和对象归属。
3. `Spring` 重点掌握 `IoC`、`DI`、`AOP`、事务。
4. `SpringMVC` 重点掌握 `DispatcherServlet`、参数绑定、异常处理、拦截器。
5. `MyBatis` 重点掌握 Mapper 代理、动态 SQL、`#{}` 和 `${}`、`resultMap`。
6. 面试时尽量用请求链路串起来回答，而不是零散背概念。
