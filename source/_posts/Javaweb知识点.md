---
title: javaweb知识点
date: 2022-10-19 22:27:04
cover: https://pic.imgdb.cn/item/66ebeffff21886ccc0d17888.jpg
tags: 
  - JavaWeb
  - Java
categories: 技术记录
description: 当时学的真是头疼呀，各种杂七杂八的。
---
# JavaWeb 知识点笔记

## 使用建议

| 学习阶段 | 建议重点 |
|---|---|
| 入门阶段 | `HTML`、`CSS`、`JavaScript`、`HTTP`、`Servlet`、`JSP` |
| 后端开发阶段 | `Spring Boot`、`Spring MVC`、三层架构、统一响应、异常处理、文件上传 |
| 数据访问阶段 | `JDBC`、`MyBatis`、连接池、动态 SQL、分页、事务 |
| 项目实战阶段 | 登录认证、`JWT`、过滤器、拦截器、`AOP`、部署、`Nginx` |
| 面试提升阶段 | `IOC`、`DI`、`AOP`、事务失效、自动配置、`Filter` 与 `Interceptor` 区别 |

# JavaWeb 常见知识点查漏补缺

## 1. JavaWeb 整体知识地图

JavaWeb 可以按照“请求进入系统后的完整链路”理解：

```text
浏览器 / 前端页面
    ↓ HTTP / HTTPS
Nginx / 网关 / 反向代理
    ↓
Tomcat / Servlet 容器
    ↓
Filter 过滤器
    ↓
DispatcherServlet 前端控制器
    ↓
Interceptor 拦截器
    ↓
Controller 控制层
    ↓
Service 业务层
    ↓
Mapper / DAO 持久层
    ↓
MySQL / Redis / MQ / OSS 等基础设施
```

常见后端项目关注点：

| 方向 | 核心内容 | 常见技术 |
|---|---|---|
| 接口开发 | 参数接收、响应封装、状态码、RESTful | `Spring MVC`、`Jackson`、`Validation` |
| 业务组织 | 三层架构、DTO/VO/PO、事务边界 | `Controller`、`Service`、`Mapper` |
| 数据访问 | SQL、动态 SQL、分页、连接池 | `MyBatis`、`MyBatis-Plus`、`Druid`、`HikariCP` |
| 登录认证 | Cookie、Session、JWT、权限校验 | `Interceptor`、`Filter`、`Spring Security` |
| 系统稳定性 | 异常处理、日志、限流、幂等、重试 | `@RestControllerAdvice`、`Slf4j`、`Redis` |
| 部署运维 | 打包、环境配置、反向代理、容器化 | `Maven`、`Nginx`、`Docker`、`Linux` |

---

## 2. HTTP/HTTPS 补充

### 2.1 HTTP 请求组成

一个标准 HTTP 请求通常由以下部分组成：

```text
请求行：GET /users/1 HTTP/1.1
请求头：Host、User-Agent、Content-Type、Authorization ...
空行
请求体：JSON、表单数据、文件数据等
```

常见请求头：

| 请求头 | 作用 |
|---|---|
| `Content-Type` | 告诉服务器请求体的数据格式，如 `application/json`、`multipart/form-data` |
| `Authorization` | 携带认证信息，如 `Bearer token` |
| `Cookie` | 浏览器自动携带的 Cookie 数据 |
| `User-Agent` | 客户端浏览器或程序信息 |
| `Referer` | 请求来源页面 |

### 2.2 HTTP 响应组成

```text
状态行：HTTP/1.1 200 OK
响应头：Content-Type、Set-Cookie、Cache-Control ...
空行
响应体：HTML / JSON / 文件流等
```

常见响应头：

| 响应头 | 作用 |
|---|---|
| `Content-Type` | 告诉浏览器响应体类型 |
| `Set-Cookie` | 让浏览器保存 Cookie |
| `Cache-Control` | 控制缓存策略 |
| `Location` | 重定向目标地址 |

### 2.3 常见状态码补充

| 状态码 | 含义 | 常见场景 |
|---|---|---|
| `200` | 请求成功 | 查询、新增、修改成功 |
| `201` | 创建成功 | 创建资源成功 |
| `204` | 成功但无响应体 | 删除成功但不返回数据 |
| `301` | 永久重定向 | 域名永久迁移 |
| `302` | 临时重定向 | 登录后跳转 |
| `400` | 请求参数错误 | 参数类型不匹配、缺失必填参数 |
| `401` | 未认证 | 未登录、Token 无效 |
| `403` | 无权限 | 已登录但权限不足 |
| `404` | 资源不存在 | URL 错误、接口不存在 |
| `405` | 请求方法不支持 | 用 `GET` 请求了只支持 `POST` 的接口 |
| `409` | 资源冲突 | 重复提交、唯一键冲突 |
| `500` | 服务端异常 | 空指针、SQL 异常等 |

### 2.4 GET 与 POST 的区别

| 对比项 | `GET` | `POST` |
|---|---|---|
| 语义 | 获取资源 | 提交数据、创建资源 |
| 参数位置 | 通常在 URL Query 中 | 通常在请求体中 |
| 幂等性 | 通常幂等 | 不一定幂等 |
| 缓存 | 更容易被缓存 | 默认不缓存 |
| 使用场景 | 查询列表、详情 | 新增、登录、上传、复杂查询 |

注意：`GET` 并不是绝对不能带请求体，只是浏览器和常见服务端框架通常不推荐这样做。

### 2.5 HTTPS 简要理解

`HTTPS = HTTP + TLS/SSL`，主要解决三类问题：

- 加密传输：防止请求和响应内容被窃听。
- 身份认证：通过证书确认访问的服务器是真实可信的。
- 完整性校验：防止传输过程中数据被篡改。

---

## 3. RESTful API 设计补充

### 3.1 RESTful 资源命名

推荐用“资源名 + HTTP 方法”表达动作，而不是在 URL 中写动词。

| 操作 | 推荐写法 | 不推荐写法 |
|---|---|---|
| 查询用户列表 | `GET /users` | `GET /getUsers` |
| 查询用户详情 | `GET /users/{id}` | `GET /getUserById?id=1` |
| 新增用户 | `POST /users` | `POST /addUser` |
| 修改用户 | `PUT /users/{id}` | `POST /updateUser` |
| 删除用户 | `DELETE /users/{id}` | `GET /deleteUser?id=1` |

### 3.2 常见响应格式

```java
@Data
@AllArgsConstructor
@NoArgsConstructor
public class Result<T> {
    private Integer code;
    private String message;
    private T data;

    public static <T> Result<T> success(T data) {
        return new Result<>(1, "success", data);
    }

    public static <T> Result<T> success() {
        return new Result<>(1, "success", null);
    }

    public static <T> Result<T> error(String message) {
        return new Result<>(0, message, null);
    }
}
```

泛型版 `Result<T>` 比 `Object data` 更规范，因为它能保留返回数据类型信息。

### 3.3 分页响应格式

```java
@Data
@AllArgsConstructor
@NoArgsConstructor
public class PageResult<T> {
    private Long total;
    private List<T> rows;
}
```

常见分页参数：

```text
GET /users?page=1&pageSize=10&keyword=tom
```

---

## 4. Servlet、Tomcat 与 Spring MVC 的关系

### 4.1 Tomcat 是什么

`Tomcat` 是一个 `Servlet` 容器，负责接收 HTTP 请求、封装 `HttpServletRequest` 和 `HttpServletResponse`，并调用对应的 `Servlet`。

### 4.2 DispatcherServlet 是什么

`DispatcherServlet` 是 `Spring MVC` 的核心前端控制器。浏览器请求进入 Spring Boot Web 项目后，通常会先进入 `DispatcherServlet`，再由它完成：

1. 根据 URL 找到对应 `Controller` 方法。
2. 完成参数绑定和类型转换。
3. 调用目标方法。
4. 对返回值进行处理。
5. 将结果转换成 JSON 或页面响应给前端。

简化流程：

```text
浏览器请求
  ↓
Tomcat
  ↓
Filter
  ↓
DispatcherServlet
  ↓
HandlerMapping 查找 Controller 方法
  ↓
HandlerAdapter 调用方法
  ↓
Controller
  ↓
ReturnValueHandler 处理返回值
  ↓
JSON / View 响应
```

### 4.3 Filter 与 Interceptor 的区别

| 对比项 | `Filter` | `Interceptor` |
|---|---|---|
| 所属体系 | `Servlet` 规范 | `Spring MVC` 体系 |
| 执行时机 | 进入 `DispatcherServlet` 之前 | 进入 `Controller` 之前/之后 |
| 拦截范围 | 几乎所有 Web 请求 | 通常只拦截 Spring MVC 请求 |
| 能否拿到 Handler 方法 | 不方便 | 可以拿到目标处理器信息 |
| 常见用途 | 编码、跨域、日志、登录粗校验 | 权限、登录、接口耗时、业务校验 |

执行顺序通常是：

```text
Filter 前置逻辑
  ↓
Interceptor preHandle
  ↓
Controller
  ↓
Interceptor postHandle
  ↓
Interceptor afterCompletion
  ↓
Filter 后置逻辑
```

---

## 5. Spring Boot 项目结构补充

推荐结构：

```text
com.example.project
├── ProjectApplication.java
├── controller        # 控制层
├── service           # 业务接口
│   └── impl          # 业务实现类
├── mapper            # MyBatis Mapper 接口
├── pojo / entity     # 数据库实体类
├── dto               # 接收前端请求的数据对象
├── vo                # 返回给前端的视图对象
├── config            # 配置类
├── exception         # 自定义异常、全局异常处理
├── filter            # 过滤器
├── interceptor       # 拦截器
├── utils             # 工具类
└── common            # 通用结果、常量、枚举
```

### 5.1 Entity、DTO、VO 的区别

| 类型 | 含义 | 使用位置 |
|---|---|---|
| `Entity` / `PO` | 与数据库表结构对应 | Mapper 层、数据库映射 |
| `DTO` | Data Transfer Object，接收请求参数 | Controller 入参、Service 入参 |
| `VO` | View Object，返回给前端展示 | Controller 返回值 |
| `BO` | Business Object，业务对象 | 复杂业务内部流转 |

不要把数据库实体类直接暴露给前端，原因是：

- 容易泄露敏感字段，如 `password`、`salt`。
- 前端需要的字段不一定等于数据库字段。
- 后续数据库结构变化会影响接口稳定性。

### 5.2 Controller 编写规范

```java
@RestController
@RequestMapping("/users")
@RequiredArgsConstructor
public class UserController {

    private final UserService userService;

    @GetMapping("/{id}")
    public Result<UserVO> getById(@PathVariable Long id) {
        return Result.success(userService.getById(id));
    }

    @PostMapping
    public Result<Void> create(@RequestBody @Valid UserCreateDTO dto) {
        userService.create(dto);
        return Result.success();
    }
}
```

推荐使用构造器注入，而不是字段注入：

```java
@RequiredArgsConstructor
@Service
public class UserServiceImpl implements UserService {
    private final UserMapper userMapper;
}
```

构造器注入的优点：依赖不可变、便于测试、避免反射注入带来的隐式依赖。

---

## 6. Spring MVC 参数绑定与校验补充

### 6.1 常见参数接收方式

| 参数来源 | 示例 | 后端接收方式 |
|---|---|---|
| Query 参数 | `/users?id=1` | `@RequestParam Long id` |
| Path 参数 | `/users/1` | `@PathVariable Long id` |
| JSON 请求体 | `{"name":"Tom"}` | `@RequestBody UserDTO dto` |
| 表单参数 | `username=tom&age=18` | 普通形参或对象 |
| 文件上传 | `multipart/form-data` | `MultipartFile file` |
| 请求头 | `Authorization: Bearer xxx` | `@RequestHeader String authorization` |
| Cookie | `token=xxx` | `@CookieValue String token` |

### 6.2 参数校验 Validation

添加依赖：

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-validation</artifactId>
</dependency>
```

DTO 示例：

```java
@Data
public class UserCreateDTO {
    @NotBlank(message = "用户名不能为空")
    private String username;

    @NotBlank(message = "密码不能为空")
    private String password;

    @Email(message = "邮箱格式不正确")
    private String email;

    @Min(value = 0, message = "年龄不能小于0")
    @Max(value = 150, message = "年龄不能大于150")
    private Integer age;
}
```

Controller 中使用：

```java
@PostMapping("/users")
public Result<Void> create(@RequestBody @Valid UserCreateDTO dto) {
    userService.create(dto);
    return Result.success();
}
```

### 6.3 常见校验注解

| 注解 | 作用 |
|---|---|
| `@NotNull` | 不能为 `null` |
| `@NotBlank` | 字符串不能为 `null`，且去除空格后不能为空 |
| `@NotEmpty` | 集合、数组、字符串不能为空 |
| `@Min` / `@Max` | 数值范围校验 |
| `@Email` | 邮箱格式校验 |
| `@Pattern` | 正则表达式校验 |
| `@Size` | 长度或集合大小校验 |

---

## 7. 全局异常处理补充

推荐不要在每个 Controller 方法中大量写 `try-catch`，而是使用全局异常处理器统一兜底。

```java
@Slf4j
@RestControllerAdvice
public class GlobalExceptionHandler {

    @ExceptionHandler(BusinessException.class)
    public Result<Void> handleBusinessException(BusinessException e) {
        log.warn("业务异常：{}", e.getMessage());
        return Result.error(e.getMessage());
    }

    @ExceptionHandler(MethodArgumentNotValidException.class)
    public Result<Void> handleValidException(MethodArgumentNotValidException e) {
        String message = e.getBindingResult()
                .getFieldErrors()
                .stream()
                .findFirst()
                .map(FieldError::getDefaultMessage)
                .orElse("参数校验失败");
        return Result.error(message);
    }

    @ExceptionHandler(Exception.class)
    public Result<Void> handleException(Exception e) {
        log.error("系统异常", e);
        return Result.error("系统繁忙，请稍后再试");
    }
}
```

自定义业务异常：

```java
public class BusinessException extends RuntimeException {
    public BusinessException(String message) {
        super(message);
    }
}
```

常见原则：

- 参数错误：返回明确提示。
- 业务错误：抛出自定义业务异常。
- 系统错误：记录完整日志，但不要把堆栈直接返回给前端。

---

## 8. Spring 事务补充

### 8.1 `@Transactional` 常见属性

| 属性 | 作用 |
|---|---|
| `rollbackFor` | 指定哪些异常触发回滚 |
| `propagation` | 事务传播行为 |
| `isolation` | 事务隔离级别 |
| `readOnly` | 是否只读事务 |
| `timeout` | 超时时间 |

推荐写法：

```java
@Transactional(rollbackFor = Exception.class)
public void createOrder(CreateOrderDTO dto) {
    // 1. 新增订单
    // 2. 扣减库存
    // 3. 写入日志
}
```

### 8.2 默认回滚规则

`Spring` 默认只对 `RuntimeException` 和 `Error` 回滚，对受检异常不一定回滚。因此实际项目中常用：

```java
@Transactional(rollbackFor = Exception.class)
```

### 8.3 事务失效的常见场景

| 场景 | 原因 |
|---|---|
| 同类方法内部调用 | 没有经过 Spring 代理对象 |
| 方法不是 `public` | 代理增强可能无法生效 |
| 异常被捕获但没有重新抛出 | Spring 感知不到异常 |
| 数据库引擎不支持事务 | 如 MySQL 的 MyISAM |
| 没有被 Spring 管理 | 类没有成为 Bean |

### 8.4 事务传播行为

| 传播行为 | 含义 | 常见使用 |
|---|---|---|
| `REQUIRED` | 有事务就加入，没有就新建 | 默认，最常用 |
| `REQUIRES_NEW` | 总是新建事务，挂起外部事务 | 操作日志、独立记录 |
| `SUPPORTS` | 有事务就加入，没有就非事务执行 | 查询类方法 |
| `MANDATORY` | 必须存在事务，否则报错 | 强制在事务中运行 |
| `NOT_SUPPORTED` | 总是非事务执行 | 不需要事务的耗时操作 |
| `NEVER` | 必须非事务执行，否则报错 | 特殊场景 |
| `NESTED` | 嵌套事务，支持保存点 | 部分回滚场景 |

---

## 9. AOP 补充

`AOP` 适合处理横切逻辑，也就是很多业务方法都需要重复做的事情。

常见场景：

- 统一日志记录。
- 接口耗时统计。
- 权限校验。
- 参数脱敏。
- 防重复提交。
- 事务管理底层也依赖 AOP 思想。

示例：记录接口耗时。

```java
@Slf4j
@Aspect
@Component
public class TimeAspect {

    @Around("execution(* com.example.controller.*.*(..))")
    public Object recordTime(ProceedingJoinPoint joinPoint) throws Throwable {
        long begin = System.currentTimeMillis();
        try {
            return joinPoint.proceed();
        } finally {
            long cost = System.currentTimeMillis() - begin;
            log.info("方法：{}，耗时：{}ms", joinPoint.getSignature(), cost);
        }
    }
}
```

常见通知类型：

| 通知 | 执行时机 |
|---|---|
| `@Before` | 目标方法执行前 |
| `@After` | 目标方法结束后，无论是否异常 |
| `@AfterReturning` | 目标方法正常返回后 |
| `@AfterThrowing` | 目标方法抛出异常后 |
| `@Around` | 环绕目标方法，最强大 |

---

## 10. MyBatis 补充

### 10.1 `#{}` 与 `${}` 区别

| 写法 | 本质 | 是否预编译 | SQL 注入风险 | 使用场景 |
|---|---|---|---|---|
| `#{}` | 占位符 | 是 | 低 | 普通参数传递 |
| `${}` | 字符串拼接 | 否 | 高 | 动态表名、动态排序字段等少量场景 |

推荐优先使用 `#{}`：

```xml
<select id="getById" resultType="User">
    select * from user where id = #{id}
</select>
```

谨慎使用 `${}`：

```xml
<select id="listOrderBy" resultType="User">
    select * from user order by ${orderBy}
</select>
```

如果必须使用 `${}`，要在 Java 代码中对白名单进行校验。

### 10.2 常见动态 SQL 标签

| 标签 | 作用 |
|---|---|
| `<if>` | 条件判断 |
| `<where>` | 自动补充 `where`，并处理多余的 `and/or` |
| `<set>` | 自动补充 `set`，并处理多余逗号 |
| `<foreach>` | 遍历集合，常用于批量删除、批量插入 |
| `<sql>` / `<include>` | 抽取和复用 SQL 片段 |

批量删除示例：

```xml
<delete id="deleteByIds">
    delete from user
    where id in
    <foreach collection="ids" item="id" open="(" separator="," close=")">
        #{id}
    </foreach>
</delete>
```

### 10.3 MyBatis 一级缓存和二级缓存

| 缓存 | 范围 | 默认情况 |
|---|---|---|
| 一级缓存 | `SqlSession` 级别 | 默认开启 |
| 二级缓存 | `Mapper` namespace 级别 | 需要配置开启 |

实际业务中，二级缓存使用较谨慎，因为多个系统或多个数据修改入口可能导致缓存一致性问题。

### 10.4 数据库连接池

连接池的作用：复用数据库连接，避免频繁创建和销毁连接带来的性能开销。

常见连接池：

- `HikariCP`：Spring Boot 默认常用连接池，性能优秀。
- `Druid`：阿里开源连接池，监控功能较丰富。

---

## 11. 登录、认证与授权补充

### 11.1 认证与授权区别

| 概念 | 含义 | 示例 |
|---|---|---|
| 认证 Authentication | 判断“你是谁” | 登录、校验 Token |
| 授权 Authorization | 判断“你能做什么” | 是否能删除用户、访问管理后台 |

### 11.2 Cookie、Session、JWT 对比

| 方案 | 状态保存位置 | 优点 | 缺点 |
|---|---|---|---|
| `Cookie` | 浏览器 | 简单、自动携带 | 容量小，敏感信息不能直接存 |
| `Session` | 服务端 | 安全性较好，服务端可控 | 分布式场景需要共享 Session |
| `JWT` | 客户端保存 Token | 无状态，适合前后端分离 | 一旦签发，失效控制较麻烦 |

### 11.3 JWT 常见结构

```text
Header.Payload.Signature
```

- `Header`：令牌类型和签名算法。
- `Payload`：用户信息、过期时间等声明。
- `Signature`：签名，防止 Token 被篡改。

注意：`JWT` 的 `Payload` 只是 Base64URL 编码，不是加密，因此不能存储密码等敏感信息。

### 11.4 RBAC 权限模型

`RBAC` 是 Role-Based Access Control，基于角色的访问控制。

```text
用户 User
  ↓
用户-角色关系 UserRole
  ↓
角色 Role
  ↓
角色-权限关系 RolePermission
  ↓
权限 Permission
```

常见表设计：

| 表 | 作用 |
|---|---|
| `user` | 用户表 |
| `role` | 角色表 |
| `permission` | 权限表 |
| `user_role` | 用户和角色关系表 |
| `role_permission` | 角色和权限关系表 |

---

## 12. Spring Security 简要补充

`Spring Security` 是 Spring 体系中的安全框架，常用于认证、授权、密码加密、接口保护等场景。

常见概念：

| 概念 | 作用 |
|---|---|
| `SecurityFilterChain` | 安全过滤器链 |
| `UserDetailsService` | 加载用户信息 |
| `PasswordEncoder` | 密码加密与校验 |
| `Authentication` | 认证信息对象 |
| `SecurityContext` | 保存当前登录用户上下文 |
| `@PreAuthorize` | 方法级权限控制 |

密码不能明文存储，推荐使用：

```java
@Bean
public PasswordEncoder passwordEncoder() {
    return new BCryptPasswordEncoder();
}
```

方法级权限控制示例：

```java
@PreAuthorize("hasAuthority('user:delete')")
@DeleteMapping("/users/{id}")
public Result<Void> delete(@PathVariable Long id) {
    userService.delete(id);
    return Result.success();
}
```

---

## 13. Redis 与缓存补充

JavaWeb 项目中常用 `Redis` 解决以下问题：

- 登录 Token 存储。
- 验证码缓存。
- 热点数据缓存。
- 分布式锁。
- 限流计数。
- 排行榜、点赞数、浏览量。

### 13.1 缓存穿透、击穿、雪崩

| 问题 | 含义 | 常见解决方案 |
|---|---|---|
| 缓存穿透 | 查询不存在的数据，每次都打到数据库 | 缓存空值、布隆过滤器 |
| 缓存击穿 | 热点 Key 过期，大量请求同时打到数据库 | 互斥锁、逻辑过期、热点 Key 不过期 |
| 缓存雪崩 | 大量 Key 同时失效或 Redis 故障 | 过期时间加随机值、多级缓存、熔断降级 |

### 13.2 Redis 分布式锁基本思路

加锁：

```text
SET lock:order:1 randomValue NX EX 30
```

释放锁时要判断 value 是否一致，避免误删别人的锁。

伪代码：

```java
String value = UUID.randomUUID().toString();
Boolean success = redisTemplate.opsForValue()
        .setIfAbsent("lock:order:" + orderId, value, Duration.ofSeconds(30));

if (Boolean.TRUE.equals(success)) {
    try {
        // 执行业务逻辑
    } finally {
        // 使用 Lua 脚本判断 value 一致后再删除
    }
}
```

---

## 14. 接口幂等性补充

幂等性：同一个请求执行一次和执行多次，对系统造成的最终结果一致。

常见需要幂等的场景：

- 订单创建。
- 支付回调。
- 表单重复提交。
- MQ 消息重复消费。

常见解决方案：

| 方案 | 思路 |
|---|---|
| 唯一索引 | 数据库层防止重复插入 |
| Token 机制 | 提交前获取 Token，提交后删除 Token |
| 状态机 | 只允许状态按指定方向流转 |
| 分布式锁 | 同一资源同一时间只允许一个请求处理 |
| 去重表 | 记录业务唯一请求号，重复请求直接返回 |

Token 防重复提交流程：

```text
1. 前端进入页面，请求后端生成 submitToken
2. 后端将 submitToken 存入 Redis
3. 前端提交表单时携带 submitToken
4. 后端校验并删除 submitToken
5. 删除成功才执行业务，删除失败说明重复提交
```

---

## 15. 跨域 CORS 补充

跨域是浏览器的同源策略限制导致的。同源要求：协议、域名、端口都相同。

例如：

```text
http://localhost:8080
http://localhost:5173
```

端口不同，所以跨域。

Spring Boot 常见解决方式：

```java
@Configuration
public class WebConfig implements WebMvcConfigurer {
    @Override
    public void addCorsMappings(CorsRegistry registry) {
        registry.addMapping("/**")
                .allowedOriginPatterns("*")
                .allowedMethods("GET", "POST", "PUT", "DELETE", "OPTIONS")
                .allowedHeaders("*")
                .allowCredentials(true)
                .maxAge(3600);
    }
}
```

实际生产环境不建议直接 `allowedOriginPatterns("*")`，应配置明确的前端域名。

---

## 16. 接口文档：Swagger / OpenAPI

常见接口文档工具：

- `Swagger`。
- `OpenAPI`。
- `Knife4j`。
- `Apifox`。
- `YApi`。

Spring Boot 3 常见依赖示例：

```xml
<dependency>
    <groupId>org.springdoc</groupId>
    <artifactId>springdoc-openapi-starter-webmvc-ui</artifactId>
    <version>2.5.0</version>
</dependency>
```

常见注解：

```java
@Tag(name = "用户管理")
@RestController
@RequestMapping("/users")
public class UserController {

    @Operation(summary = "根据ID查询用户")
    @GetMapping("/{id}")
    public Result<UserVO> getById(@PathVariable Long id) {
        return Result.success(userService.getById(id));
    }
}
```

接口文档应该包含：

- 请求路径。
- 请求方法。
- 请求参数。
- 请求示例。
- 响应字段。
- 错误码。
- 权限要求。

---

## 17. 日志规范补充

### 17.1 日志级别

| 级别 | 使用场景 |
|---|---|
| `trace` | 极细粒度调试，生产很少开启 |
| `debug` | 开发调试信息 |
| `info` | 关键流程信息，如登录成功、订单创建 |
| `warn` | 可恢复或需要关注的问题 |
| `error` | 系统异常，需要排查 |

推荐写法：

```java
log.info("用户登录成功，userId={}", userId);
log.warn("库存不足，skuId={}, requestCount={}", skuId, count);
log.error("创建订单失败，userId={}", userId, e);
```

不推荐：

```java
log.info("用户登录成功，userId=" + userId);
```

原因：字符串拼接会提前执行，参数化日志更规范。

### 17.2 日志中不要打印敏感信息

不要直接打印：

- 密码。
- 身份证号。
- 手机号完整值。
- Token。
- 银行卡号。

---

## 18. 文件上传补充

### 18.1 本地上传注意事项

```java
@PostMapping("/upload")
public Result<String> upload(MultipartFile file) throws IOException {
    String originalFilename = file.getOriginalFilename();
    String suffix = originalFilename.substring(originalFilename.lastIndexOf("."));
    String filename = UUID.randomUUID() + suffix;
    file.transferTo(new File("D:/upload/" + filename));
    return Result.success(filename);
}
```

注意点：

- 文件名要重新生成，避免覆盖。
- 限制文件大小。
- 校验文件后缀和 MIME 类型。
- 不要把上传目录放在项目源码目录中。
- 生产环境通常上传到 OSS、MinIO、S3 等对象存储。

### 18.2 配置上传大小

```yaml
spring:
  servlet:
    multipart:
      max-file-size: 10MB
      max-request-size: 100MB
```

---

## 19. Nginx 补充

`Nginx` 常用于：

- 部署前端静态资源。
- 反向代理后端接口。
- 负载均衡。
- HTTPS 证书配置。
- 静态资源缓存。

### 19.1 前端部署 + 后端反向代理示例

```nginx
server {
    listen 80;
    server_name example.com;

    location / {
        root /usr/share/nginx/html;
        index index.html;
        try_files $uri $uri/ /index.html;
    }

    location /api/ {
        proxy_pass http://127.0.0.1:8080/;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    }
}
```

### 19.2 `try_files` 的作用

Vue 单页应用刷新页面时可能出现 `404`，因为浏览器直接请求 `/dept`，但服务器上没有这个真实文件。配置：

```nginx
try_files $uri $uri/ /index.html;
```

可以让不存在的路径回退到 `index.html`，再交给前端路由处理。

---

## 20. Docker 部署补充

### 20.1 Spring Boot Dockerfile 示例

```dockerfile
FROM eclipse-temurin:17-jre
WORKDIR /app
COPY target/app.jar app.jar
EXPOSE 8080
ENTRYPOINT ["java", "-jar", "app.jar"]
```

构建镜像：

```bash
docker build -t javaweb-demo:1.0 .
```

运行容器：

```bash
docker run -d -p 8080:8080 --name javaweb-demo javaweb-demo:1.0
```

### 20.2 常见部署形态

```text
用户
 ↓
Nginx
 ↓
Spring Boot 容器
 ↓
MySQL / Redis / OSS
```

生产环境通常需要额外考虑：

- 日志挂载。
- 配置外置化。
- 健康检查。
- JVM 参数。
- 容器重启策略。

---

## 21. Vue 工程化补充

### 21.1 Vue 2 与 Vue 3 简要区别

| 对比项 | Vue 2 | Vue 3 |
|---|---|---|
| API 风格 | Options API 为主 | Composition API 更常用 |
| 构建工具 | Vue CLI 常见 | Vite 更常见 |
| 状态管理 | Vuex | Pinia 更常见 |
| 性能 | 较好 | 更好 |

### 21.2 Axios 拦截器

常见用途：

- 请求前统一添加 Token。
- 响应后统一处理错误码。
- 统一跳转登录页。

```javascript
import axios from 'axios'

const request = axios.create({
  baseURL: '/api',
  timeout: 10000
})

request.interceptors.request.use(config => {
  const token = localStorage.getItem('token')
  if (token) {
    config.headers.Authorization = `Bearer ${token}`
  }
  return config
})

request.interceptors.response.use(response => {
  return response.data
}, error => {
  return Promise.reject(error)
})

export default request
```

### 21.3 前端代理配置

开发环境中，Vue 可以通过代理解决跨域：

```javascript
// vue.config.js
module.exports = {
  devServer: {
    proxy: {
      '/api': {
        target: 'http://localhost:8080',
        changeOrigin: true,
        pathRewrite: { '^/api': '' }
      }
    }
  }
}
```

---

## 22. Maven 进阶补充

### 22.1 `dependencyManagement` 与 `dependencies`

| 标签 | 作用 | 子模块是否直接拥有依赖 |
|---|---|---|
| `<dependencies>` | 直接声明依赖 | 是 |
| `<dependencyManagement>` | 只管理依赖版本 | 否，子模块还要自己引入 |

### 22.2 常见 scope

| scope | main 编译 | test 编译 | 是否打包 | 示例 |
|---|---|---|---|---|
| `compile` | 是 | 是 | 是 | 普通业务依赖 |
| `provided` | 是 | 是 | 否 | Servlet API、外部 Tomcat 提供的依赖 |
| `runtime` | 否 | 是 | 是 | JDBC 驱动 |
| `test` | 否 | 是 | 否 | JUnit |
| `system` | 是 | 是 | 通常不推荐 | 本地 jar |

### 22.3 常见命令

```bash
mvn clean
mvn compile
mvn test
mvn package
mvn install
mvn deploy
mvn dependency:tree
mvn clean package -DskipTests
```

---

## 23. 常见设计模式在 JavaWeb 中的体现

| 设计模式 | JavaWeb 中的体现 |
|---|---|
| MVC 模式 | `Controller`、`Service`、`Mapper` 分层思想 |
| 前端控制器模式 | `DispatcherServlet` 统一接收请求 |
| 代理模式 | Spring AOP、事务代理、MyBatis Mapper 代理 |
| 工厂模式 | Spring IOC 容器创建 Bean |
| 单例模式 | 默认 Bean 作用域是 singleton |
| 模板方法模式 | `JdbcTemplate`、`RestTemplate` 中固定流程 + 可变步骤 |
| 责任链模式 | `FilterChain`、Spring Security Filter Chain |
| 适配器模式 | `HandlerAdapter` 适配不同 Controller 调用方式 |
| 策略模式 | 不同支付方式、不同登录方式、不同文件存储方式 |

策略模式示例：

```java
public interface PayStrategy {
    void pay(Long orderId);
}

@Service("alipay")
public class AlipayStrategy implements PayStrategy {
    public void pay(Long orderId) {
        // 支付宝支付
    }
}

@Service("wechatPay")
public class WechatPayStrategy implements PayStrategy {
    public void pay(Long orderId) {
        // 微信支付
    }
}
```

通过 `Map<String, PayStrategy>` 注入所有实现类：

```java
@Service
@RequiredArgsConstructor
public class PayService {
    private final Map<String, PayStrategy> payStrategyMap;

    public void pay(String type, Long orderId) {
        PayStrategy strategy = payStrategyMap.get(type);
        if (strategy == null) {
            throw new BusinessException("不支持的支付方式");
        }
        strategy.pay(orderId);
    }
}
```

---

## 24. Web 安全基础补充

### 24.1 SQL 注入

错误写法：

```java
String sql = "select * from user where username = '" + username + "'";
```

推荐：

```sql
select * from user where username = #{username}
```

### 24.2 XSS

`XSS` 是攻击者向页面注入恶意脚本。

防护思路：

- 前端展示用户输入内容时做转义。
- 后端过滤危险标签。
- Cookie 设置 `HttpOnly`，降低 Token 被脚本读取的风险。

### 24.3 CSRF

`CSRF` 是攻击者诱导已登录用户发起非本人意愿的请求。

防护思路：

- 使用 CSRF Token。
- 校验 `Origin` / `Referer`。
- 关键操作使用验证码或二次确认。
- Cookie 设置 `SameSite`。

### 24.4 密码存储

不要明文保存密码，推荐：

```java
String encoded = passwordEncoder.encode(rawPassword);
boolean matches = passwordEncoder.matches(rawPassword, encoded);
```

---

## 25. WebSocket 与 SSE 补充

### 25.1 WebSocket

`WebSocket` 适合双向实时通信。

常见场景：

- 在线聊天。
- 实时通知。
- 协同编辑。
- 实时大屏。

### 25.2 SSE

`SSE` 是 Server-Sent Events，适合服务端向浏览器单向推送消息。

| 对比项 | WebSocket | SSE |
|---|---|---|
| 通信方向 | 双向 | 服务端到客户端单向 |
| 协议 | 独立升级协议 | 基于 HTTP |
| 适用场景 | 聊天、游戏、协作 | 通知、日志流、状态推送 |

---

## 26. JavaWeb 高频面试题补充

### 26.1 Spring Boot 自动配置原理是什么

简要回答：

`Spring Boot` 通过自动配置机制，根据当前类路径中的依赖、配置文件中的属性以及条件注解，自动向 IOC 容器中注册合适的 Bean，从而减少手动配置。

关键词：

- `@SpringBootApplication`
- `@EnableAutoConfiguration`
- `AutoConfiguration`
- `@ConditionalOnClass`
- `@ConditionalOnMissingBean`
- `@ConditionalOnProperty`

### 26.2 IOC 和 DI 的区别

- `IOC` 是思想：对象创建权从程序员手中反转给容器。
- `DI` 是实现方式：容器把对象依赖注入到需要它的地方。

### 26.3 AOP 解决什么问题

`AOP` 解决横切逻辑复用问题。比如日志、权限、事务、接口耗时统计等逻辑，如果写在每个业务方法中会造成代码重复，AOP 可以统一增强。

### 26.4 `@Controller` 和 `@RestController` 的区别

- `@Controller` 通常返回视图页面。
- `@RestController = @Controller + @ResponseBody`，通常返回 JSON 数据。

### 26.5 `@RequestParam`、`@PathVariable`、`@RequestBody` 区别

| 注解 | 参数位置 | 示例 |
|---|---|---|
| `@RequestParam` | Query/Form 参数 | `/users?id=1` |
| `@PathVariable` | URL 路径 | `/users/1` |
| `@RequestBody` | 请求体 JSON | `{"name":"Tom"}` |

### 26.6 Bean 默认作用域是什么

默认是 `singleton`，即 IOC 容器中只有一个 Bean 实例。

### 26.7 MyBatis 如何防止 SQL 注入

优先使用 `#{}`，因为它会使用预编译参数；不要直接使用字符串拼接；必须使用 `${}` 时，需要做白名单校验。

### 26.8 为什么不建议直接返回 Entity

- 可能泄露敏感字段。
- 接口返回结构和数据库结构强耦合。
- 不利于版本演进。

### 26.9 Filter、Interceptor、AOP 的区别

| 技术 | 作用范围 | 常见场景 |
|---|---|---|
| `Filter` | Servlet 请求级别 | 编码、跨域、粗粒度登录校验 |
| `Interceptor` | Spring MVC 请求级别 | 登录校验、权限、接口耗时 |
| `AOP` | Spring Bean 方法级别 | 业务方法增强、日志、事务 |

### 26.10 如何排查接口很慢

排查顺序：

1. 看日志中的接口耗时。
2. 看 SQL 是否慢，是否缺索引。
3. 看是否有远程调用耗时。
4. 看是否存在锁等待或连接池耗尽。
5. 看 JVM、CPU、内存、GC 情况。
6. 看网络和 Nginx 转发是否异常。

---

## 27. 推荐继续补充的项目实战主题

后续如果继续完善 JavaWeb 笔记，建议重点补这些实战专题：

- RBAC 权限系统设计。
- 商品、订单、库存业务建模。
- 支付回调幂等处理。
- Redis 缓存和分布式锁。
- Spring Security + JWT 实战。
- MyBatis-Plus 常用开发方式。
- Docker Compose 部署前后端项目。
- GitHub Actions / Jenkins 自动化部署。
- 接口压测与性能优化。
- 日志链路追踪和问题排查。

---

## 28. 官方资料参考

- Spring Boot Reference Documentation：`https://docs.spring.io/spring-boot/`
- Spring Framework Web MVC Documentation：`https://docs.spring.io/spring-framework/reference/web/webmvc.html`
- Apache Maven Documentation：`https://maven.apache.org/guides/`
- MyBatis Documentation：`https://mybatis.org/mybatis-3/`
- MyBatis Spring Boot Starter Documentation：`https://mybatis.org/spring-boot-starter/`
- MDN Web Docs：`https://developer.mozilla.org/`
- Vue Documentation：`https://vuejs.org/`
- Axios Documentation：`https://axios-http.com/`



# 原始笔记整理版主体

> 以下内容来自原笔记，已做标题、缩进、列表、代码标记等格式整理；原有知识点尽量保持完整，不做删减。

# Web

## 初识web

通过浏览器转化(解析和渲染)，将代码变成用户看到的网页。浏览器中对代码进行解析渲染的部分，称为**浏览器内核**。


### web网站的工作流程

如图下所示，分为前后端分离和不分离。箭头从上往下依次执行

`<img src="D:\Users\作业\知识点\java\图片\web网站开发流程.png" style="zoom: 50%;" />`

`<img src="D:\Users\作业\知识点\java\图片\前后端不分离.png" style="zoom: 50%;" />`


### Web标准

Web标准也称为网页标准,由一系列的标准组成， 大部分由W3C ( World Wide Web Consortium,万维网联盟)负责制定。

三个组成部分：

HTML：负责网页的结构(页面元素和内容)。

CSS：负责网页的表现(页面元素的外观、位置等页面样式，如:颜色、大小等)。

JavaScript：负责网页的行为(交互效果)。


## HTML和CSS

HTML (HyperText Markup Language)：超文本标记语言。

CSS (Cascading Style Sheet)：层叠样式表，用于控制页面的样式(表现)。

超文本：超越了文本的限制，比普通文本更强大。除了文字信息，还可以定义图片、音频、视频等内容。

标记语言：由标签构成的语言

HTML标签都是预定义好的。例如:使用`<a>`展示超链接,使用`<img>`展示图片, `<video>`展示视频。

HTML代码直接在浏览器中运行，HTML标签由浏览器解析。

HTML特点：

- ① HTML标签不区分大小写。

- ② HTML标签属性值单双引号都可以。

- ③ HTML语法松散。

在插入img标签时，分为绝对路径和相对路径。

绝对路径分为绝对磁盘路径和绝对网络路径(即以网址的形式展示)

相对路径：

./：当前目录(./可以省略的)

../：上一级目录


### CSS引入方式

- ① 行内样式：写在标签的style属性中(不推荐)

- ② 内嵌样式：通过选择器，写在style标签中(style可以写在页面任何位置，但通常约定写在head标签中)

- ③ 外联样式：写在一个单独的css文件中( 需要通过link标签在网页中引入)


颜色的表示形式：

- ① 关键字：预定义的颜色名，如red、green、blue...

- ② rgb表示法：红绿蓝三原色，每项取值范围: 0-255，如rgb(0,0,0)、rgb(255,255,255)、 rgb(255,0,0)

- ③ 十六进制表示法：#开头，将数字转换成十六进制表示，如#000000、#ff0000、 #cccccc， 简写: #000、#ccc


### CSS选择器

概述：用来选取需要设置样式的元素(标签)

分类：

- ① 元素选择器

格式：标签名{}

- ② id选择器

格式：#id名{}

- ③ 类选择器

格式：.类名称{}

优先级：id>类>元素


### 盒子模型

盒子：页面中所有的元素(标签) ,都可以看做是一个盒子，由盒子将页面中的元素包含在一个矩形区域内,通过盒子的视角更方便的进行页面布局。

盒子模型组成：内容区域( content)、内边距区域(padding) 、边框区域(border) 、外边距区域(margin)

`<img src="D:\Users\作业\知识点\java\图片\盒子模型.png" style="zoom:74%;" />`

```
布局标签: <div> <span>
●div标签:
    ●一行只显示一个(独占一行)
    ●宽度默认是父元素的宽度，高度默认由内容撑开
    ●可以设置宽高(width、 height)
span标签:
    ●一行可以显示多个
    ●宽度和高度默认由 内容撑开
    ●不可以设置宽高(width、height)
```

```
超链接标签
●标签:<a href="... ”target="...">央视网</a>
●属性:
	href:指定资源访问的url
	target:指定在何处打开资源链接
		_self: 默认值,在当前页面打开
		_blank: 在空白页面打开

音频、视频标签<audio>，<video>
换行: <br> ;段落: <p>
文本加粗标签
<b>或者<strong>
    CSS样式
        line-height:设置行高
        text- indent:定义第一个行内容的缩进
        text-align:规定元素中的文本的水平对齐方式
注意：在HTML中无论输入多少个空格，只会显示一一个。可以使用空格占位符: &nbsp;

表格标签
<table>：定义表格整体，可以包裹多个<tr>
    border:规定表格边框的宽度
    width:规定表格的宽度
    cellspacing:规定单元之间的空间。
<tr>：表格的行，可以包裹多个<td>
<td>：表格单元格(普通)，可以包裹内容
	如果是表头单元格，可以替换为<th>

表单标签
标签: <form>
表单项:不同类型的input元素、下拉列表、文本域等。
    ●<input>: 定义表单项,通过type属性控制输入形式
    ●<select>: 定义下拉列表
    ●<textarea>: 定义文本域
属性:
    ●action:规定当提交表单时向何处发送表单数据，URL
    ●method:规定用于发送表单数据的方式。GET、POST

表单项标签
<input>:表单项，通过type属性控制输入形式。
<select>:定义下拉列表，<option> 定义列表项。
<textarea>:文本域

```


## JavaScript

```
JavaScript (简称: JS)是一门跨平台、面向对象的脚本语言。是用来控制网页行为的，它能使网页可交互。
JavaScript和Java是完全不同的语言，不论是概念还是设计。但是基础语法类似。
JavaScript在1995年由Brendan Eich发明，并于1997年成为ECMA标准。
ECMAScript 6 (ES6)是最新的JavaScript版本(发布于2015年)。
```


### js引入方式

内部脚本：将js代码定义在HTML页面中

```
JavaScript代码必须位于<script></script>标签之间
在HTML文档中， 可以在任意地方，放置任意数量的<script>
一般会把脚本置于<body>元素的底部，可改善显示速度
```

外部脚本：将js代码定义在外部js文件中，然后引入到HTML页面中

```
外部JS文件中, 只包含S代码,不包含<script>标签
<script>标签不能自闭合
```


### js基础语法

书写语法

```
区分大小写:与Java一样，变量名、函数名以及其他一切东西都是区分大小写的
每行结尾的分号可有可无
注释:
●单行注释: // 注释内容
●多行注释: /* 注释内容*/
大括号表示代码块

输出语句
使用window.alert()写入警告框
使用document.write()写入HTML输出
使用console.log()写入浏览器控制台
```


变量

```
JavaScript中用var关键字(variable的缩写)来声明变量。
JavaScript是一门弱类型语言，变量可以存放不同类型的值。
变量名需要遵循如下规则:
➢组成字符可以是任何字母、数字、下划线(_)或美元符号($)
➢数字不能开头
➢建议使用驼峰命名

特点1：作用域比较大，全局变量
特点2：可以重复定义的
注意事项
●ECMAScript 6新增了let关键字来定义变量。它的用法类似于var,但是所声明的变量，只在let关键字所在的代码块内有效,且不允许重复声明。
●ECMAScript 6新增了const关键字，用来声明一个只读的常量。一旦声明，常量的值就不能改变。
```


数据类型

```
JavaScript中分为:原始类型和引用类型。
原始类型
    ●number:数字(整数、小数、NaN(Not a Number))
    ●string:字符串，单双引皆可
    ●boolean:布尔。true, false
    ●null:对象为空
    ●undefined: 当声明的变量未初始化时,该变量的默认值是undefined
使用typeof运算符可以获取数据类型，如typeof a

===全等运算符
==会进行类型转换, ===不会进行类型转换

类型转换
字符串类型转为数字:
	●parseInt()方法将字符串字面值转为数字。 如果字面值不是数字，则转为NaN。
其他类型转为boolean:
    ●Number: 0和NaN为false,其他均转为true。
    ●String: 空字符串为false,其他均转为true。
    ●Null和undefined :均转为false。
```


### js函数

```
概述：函数(方法)是被设计为执行特定任务的代码块。
定义: JavaScript函数通过function关键字进行定义，语法为:
function functionName(参数1,参数2..){
	//要执行的代码
}
注意:
    ●形式参数不需要类型。因为JavaScript是弱类型语言
    ●返回值也不需要定义类型,可以在函数内部直接使用return返回即可
    ●JS中，函数调用可以传递任意个数的参数。
调用:函数名称(实际参数列表)

定义方式二：
var functionName = function (参数1,参数2..){
	//要执行的代码
}
```


### js对象

#### Array

```
JavaScript中Array对象用于定义数组。
定义：
    var 变量名 = new Array(元素列表); //方式一
    var 变量名 = [元素列表]; //方式二
访问：
    arr[索引]=值;
特点:长度可变类型可变

属性：
    length：设置或返回数组中元素的数量。
方法：
    forEach(function(e){}) ：遍历数组中的每个有值的元素，并调用一次传入的函数
    	简化后forEach((e) => {})，可以通过var给箭头函数起名字
    push()：将新元素添加到数组的末尾，并返回新的长度。
    splice()：从数组中删除元素。


```

#### String

```
String字符串对象创建方式有两种:
var 变量名 = new String(".."); //方式一
var 变量名 =".." ;//方式二
单引号或双引号都一样
方法
	length：字符串的长度。
    charAt( )：返回在指定位置的字符。
    indexOf( )：检索字符串。
    trim()：去除字符串两边的空格
    substring( )：提取字符串中两个指定的索引号之间的字符。
```


#### `JSON`

```
javascript自定义对象
定义格式:
var对象名={
    属性名1:属性值1,
    属性名2:属性值2,
    属性名3:属性值3,
    函数名称: function(形参列表){}
};
这里: function(形参列表)可省略为(形参列表)
调用格式:
    对象名.属性名;
    对象名.函数名();

概念: JavaScript Object Notation, JavaScript对象标记法。JSON是通过JavaScript对象标记法书写的文本。
作用：多用于作为数据载体,在网络中进行数据传输。
注意：json中所有的key都必须用双引号包括起来，他的本质是字符串

定义
var 变量名 = '{"key1": value1, "key2": value2}';

value的数据类型为:
    数字(整数或浮点数)
    字符串 (在双引号中)
    逻辑值(true 或false)
    数组(在方括号中)
    对象(在花括号中)
    null

JSON字符串转为JS对象
	var jsObject = JSON.parse(userStr);
JS对象转为JSON字符串
	var jsonStr = JSON.stringify(jsObject);

json数据和java对象转换
	Fastjson概述：Fastjson 是阿里巴巴提供的一个Java语言编写的高性能功能完善的 JSON 库，是目前Java语言中最快的 JSON 库，可以实现 Java 对象和 JSON 字符串的相互转换。

Fastjson使用
	①导入坐标
		<dependency>
			<groupId>com.alibaba</groupId>
			<artifactId>fastjson</artifactId>
			<version>1.2.62</version>
		</dependency>
	②Java对象转JSON
		String jsonStr = JSON.toJSONString(obj);
	③JSON字符串转Java对象
		User user = JSON.parseObject(jsonStr, User.class);
```


#### BOM

```
概念: Browser object Model浏览器对象模型,允许JavaScript 与浏览器对话，JavaScript将浏览器的各个组成部分封装为对象。
组成:
    ●Window: 浏览器窗口对象
    ●Navigator: 浏览器对象
    ●Screen: 屏幕对象
    ●History: 历史记录对象
    ●Location: 地址栏对象

Window
介绍:浏览器窗口对象。
获取:直接使用window,其中window.可以省略。
如
    window.alert( "Hello Window");
    alert("Hello Window");
属性
    history:对History对象的只读引用。请参阅History对象。
    location:用于窗口或框架的Location对象。请参阅Location对象。
    navigator:对Navigator对象的只读引用。请参阅Navigator对象。
方法
    alert():显示带有一段消 息和一-个确认按钮的警告框。
    confirm():显示带有一段消息以及 确认按钮和取消按钮的对话框。
    setInterval(function(){},time):按照指定的周期(以毫秒计)来调用函数或计算表达式。
    setTimeout():在指定的毫秒数后调用函数或计算表达式。

Location
介绍:地址栏对象。
获取:使用window.location获取,其中window.可以省略。
如
    window.location.属性;
    location.属性;
属性:
    href:设置或返回完整的URL。		如location.href = "https://www.baidu.com";
```


#### DOM

```
概念: Document Object Model，文档对象模型。
将标记语言的各个组成部分封装为对应的对象:
    Document:整个文档对象
    Element:元素对象
    Attribute: 属性对象
    Text: 文本对象
    Comment: 注释对象
JavaScript通过DOM,就能够对HTML进行操作:
    改变HTML元素的内容
    改变 HTML元素的样式(CSS)
    对HTML DOM事件作出反应
    添加和删除HTML元素

DOM是W3C(万维网联盟)的标准，定义了访问HTML和XML文档的标准，分为3个不同的部分:
1.Core DOM -所有文档类型的标准模型
    Document:整个文档对象
    Element: 元素对象
    Attribute: 属性对象
    Text: 文本对象
    Comment:注释对象
2. XML DOM - XML文档的标准模型
3. HTML DOM - HTML文档的标准模型(即把所有html标签封装成了DOM对象)
    Image: <img>
    Button: <input type='button'>

HTML中的Element对象可以通过Document对象获取，而Document对象是通过window对象获取的。
Document对象中提供了以下获取Element元素对象的函数:
1. 根据id属性值获取,返回单个Element对象
var h1 = document.getElementById('h1');
2.根据标签名称获取,返回Element对象数组
var divs = document.getElementsByTagName('div');
3.根据name属性值获取，返回Element对象数组
var hobbys = document.getElementsByName('hobby');
4.根据class属性值获取， 返回Element对象数组
var clss = document.getElementsByClassName('cls');
```


### js事件监听

```
事件: HTML事件是发生在HTML元素上的“事情”
比如:
    按钮被点击
    鼠标移动到元素上
    按下键 盘按键
事件监听: JavaScript可以在事件被侦测到时执行代码。

事件绑定
方式一:通过HTML标签中的事件属性进行绑定(将js和html耦合在一起了)
<input type="button" onclick="on()" value=" 按钮1">
<script>
	function on(){		//方法名自定义
		alert( '我被点击了!');
	}
</script>

方式二:通过DOM元素属性绑定
<input type= "button" id="btn" value= "按钮2">
<script>
document.getElementById('btn').onclick=function(){
		alert('我被点击了!');
	}
</script>

常见事件
    onclick：鼠标单击事件
    onblur：元素失去焦点
    onfocus：元素获得焦点
    onload：某个页面或图像被完成加载
    onsubmit：当表单提交时触发该事件
    onkeydown：某个键盘的键被按下
    onmouseover：鼠标被移到某元素之上
    onmouseout：鼠标从某元素移开
使用这些事件的方法如上述方法一或二
```


## `Vue`

概述：Vue是一套前端框架，免除原生JavaScript中的DOM操作，简化书写。

本质：基于MVVM(Model-View-ViewModel)思想，实现数据的双向绑定，将编程的关注点放在数据上。Model表示数据模型，里面包含了很多数据，以及数据的处理方法。View时视图层，他只负责视图的展示。ViewModel是View和Model的通信桥梁，通过他将View和Model数据绑定了起来。

官网: https://v2.cn.vuejs.org/


### Vue快速入门

```
●新建HTML页面， 引入Vue.js文件
	<script src="js/vue.js"></script>
●在JS代码区域，创建Vue核心对象，定义数据模型
<script>
	new Vue({
		el:' '#app" ,		//el表示vue要控制的区域，类似于选择器
		data: {			// data是定义的模型，是一个对象
			message: "Hello Vue!"
		}
	})
</script>
●编写视图
<div id="app">
    <input type="text" v-model= "message">		//这个v-model叫指令
    {{ message }}	//这是一个插值表达式
</div>

插值表达式
	形式: {{表达式}}。
	内容可以是:
        变量
        三元运算符
        函数调用
        算术运算

常用指令
●指令: HTML标签上带有v-前缀的特殊属性,不同指令具有不同含义。例如: v-if, v-for...
●常用指令
    v-bind：为HTML标签绑定属性值，如设置href，css样式等
    v-model：在表单元素上创建双向数据绑定
    v-on：为HTML标签绑定事件
    v-if：条件性的渲染某元素，判定为true时渲染，否则不渲染
    v-else-if：条件性的渲染某元素，判定为true时渲染，否则不渲染
    v-else：条件性的渲染某元素，判定为true时渲染，否则不渲染
    v-show：根据条件展示某元素，和v-if等系列的区别在于v-show切换的是display属性的值，而v-if是直接不渲染(在源代码中看不到)
    v-for：列表渲染，遍历容器的元素或者对象的属性
注意：通过v-bind或者v-model绑定的变量，必须在数据模型中声明。
如
v-bind例子
    <a v-bind:href="url">传智教育</a>		//这个url就表示数据模型中data对象的url属性
    	//bind:href可简写为:href
    <script>
        new Vue({
            el: "#app",
            data: {
                url: "https ://www. itcast. cn"
        })
    </script>

v-model例子
	<input type="text" v-model="ur1">

V-on例子
    <input type="button" value="按钮" v-on:click="handle()">		//v-on:click可简写为@click
    <script>
        new Vue({
            el:"#app" ,
            data: {
                //...
            },
            methods: {
                handle:function(){
                	alert( '我被点击了');
            	}
            }
        })
    </script>

v-for例子
    <div v-for="addr in addrs">{{addr}}</div>		//这里就好比python中的 for i in list
    <div v-for="(addr, index) in addrs">{{index + 1}} : {{addr}}</div>		//这是获取索引
    data: {
        addrs: ['北京','上海',广 州',深圳', '成都', '杭州']
    },
```


### Vue生命周期

```
生命周期:指一个对象从创建到销毁的整个过程。
生命周期的八个阶段:每触发一个生命周期事件，会自动执行一个生命周期方法(钩子)。
    beforeCreate：创建前
    created：创建后
    beforeMount：挂载前
    mounted：挂载完成
    beforeUpdate：更新前
    updated：更新后
    beforeDestroy：销毁前
    destroyed：销毁后

mounted:挂载完成，Vue初始化成功，HTML页面渲染成功。(发送请求到服务端，加载数据)
<script>
    new Vue({
        el: "#app'
        data: {
        },
        mounted() {
        	console.1og( "Vue挂载完毕发送请求获取数据");
        },
        methods: {
        },
    })
</script>
```

![](D:\Users\作业\知识点\java\图片\Vue生命周期.png)


## Ajax

```
概念: Asynchronous JavaScript And XML，异步的JavaScript和XML。
作用:
    数据交换:通过Ajax可以给服务器发送请求，并获取服务器响应的数据。
    异步交互:可以在不重新加载整个页面的情况下，与服务器交换数据并更新部分网页的技术，如:搜索联想、用户名是否可用的校验等等。

同步和异步
	同步：服务器在处理请求的时候，客户端要等服务器执行完毕才可进行其他动作
	异步：服务器在处理请求的时候，客户端不需要等待服务器，可以执行其他动作
    同步一般是需要先请求服务器，并且等待响应，然后会重新刷新页面加载资源，他的操作是不连续的
    而异步是不需要请求服务器，而是直接加载，并且不会刷新页面，他的操作是同步的
    异步需要写全路径

原生ajax的步骤：
    创建XMLHttpRequest对象:用于和服务器交换数据
    向服务器发送请求
    获取服务器响应数据

```


### `Axios`

```
介绍: Axios对原生的Ajax进行了封装，简化书写，快速开发。
官网: https://www.axios-http.cn/

快速入门
1.引入Axios的js文件
	<script src="js/ axios-0.18.0.js"></script>
2.使用Axios发送请求，并获取响应结果
	get请求
    axios({
    	method: "get",
    	url: "http://yapi.smart-xwork.cn/mock/169327/emp/list"		//url代表请求路径
    }).then((result) => {			//如果想要获取服务端响应的数据，就用到了then这个成功回调函数，这个是ajax调用成功自动调用的函数，其中result是js对象，里面该对象中的data属性就封装了服务端响应的数据
    	console.log(result.data);
    });


    post请求
    axios({
        method: "post" ,
        url: "http://yapi.smart-xwork.cn/mock/169327/emp/deleteByld",
        data: "id=1"		//data表示请求体中带的参数
    }).then((result) => {
    	console.log(result.data);
    });

请求方式别名(简写)
axios.get(url[,config])			//[]中的内容表示可选
axios.delete(url[,config])
axios.post(url[,data[,config])
axios.put(url[,data[,config])
如下简写
发送GET请求
axios.get("http://yapi.smart-xwork.cn/mock/169327/emp/list").then((result) => {
	console.log(result.data);
});

发送POST请求
axios.post("http://yapi.smart-xwork.cn/mock/169327/emp/deleteByld","id=1").then((result)=> {
	console.log(result.data);
});

axios() 是用来发送异步请求的，小括号中使用 js 对象传递请求相关的参数：
method 属性：用来设置请求方式的。取值为 get 或者 post 。
url属性：用来书写请求的资源路径。如果是 get 请求，需要将请求参数拼接到路径的后面，格式为： url?参数名=参
数值&参数名2=参数值2 。
data属性：作为请求体被发送的数据。也就是说如果是 post 请求的话，数据需要作为 data 属性的值。
then() 需要传递一个匿名函数。我们将 then() 中传递的匿名函数称为回调函数，意思是该匿名函数在发送请求时不会被调用，而是在成功响应后调用的函数。而该回调函数中的 resp 参数是对响应的数据进行封装的对象，通过 resp.data 可以获取到响应的数据。
```


## 前后端分离开发

### 开发流程

- ① 需求分析

- ② 接口定义(API接口文档)

- ③ 前后端并行开发

- ④ 测试


### YApi

```
介绍: YApi 是高效、易用、功能强大的api管理平台(也就是为API接口文档准备的)，旨在为开发、产品、测试人员提供更优雅的接口管理服务
地址:http://yapi.smart-xwork.cn/

使用步骤：
    添加项目
    添加分类
    添加接口
```


## 前端工程化

```
环境准备
vue-cli
介绍: Vue-cli是Vue官方提供的一个脚手架，用于快速生成一个Vue的项目模板。
Vue-cli提供了如下功能:
    ◆统一的目录结构
    ◆本地调试
    ◆热部署
    ◆单元测试
    ◆集成打包上线
依赖环境: NodeJS
使用npm安装vue-cli，并用vue --version查看是否安装成功

Vue项目创建
    ●命令行:在对应vue项目目录的cmd中
        vue create vue-project01
    ●图形化界面:
        vue ui

基于Vue脚手架创建出来的工程，有标准的目录结构,如下:
工程名
	node_ modules：整个项目的依赖包
    public：存放项目的静态文件

	src：存放项目的源代码
		assets：静态资源
		components：可重用的组件
		router：路由配置
		views：视图组件(页面)
		App.vue：入口页面(根组件)
		main.js：入口js文件
	package.json：模块基本信息，项目开发所需要模块，版本信息
	vue.config.js：保存vue配置的文件，如:代理、端口的配置等

启动
方式一：点击npm脚本中package.json下的vue-cli-service serve即可
方式二：npm run serve

修改vue项目的端口
在vue.config.js中修改
const { defineConfig } = require('@vue/cli-service')
module. exports = defineConfig({
    transpileDependencies: true,
    devServer: {
		port: 7000 ,
	}
})

Vue的组件文件以.vue结尾， 每个组件由三个部分组成: <template>(html模板代码)、<script>(控制数据来源和行为)，<style>(控制css样式)。

Vue项目中使用Axios:
在项目目录下安装axios: npm install axios;
需要使用axios时，导入axios: import axios from 'axios';
```


## Element

Element：是饿了公团队研发的，一套为开发者、设计师和产品经理准备的基于Vue 2.0的桌面端组件库。

组件：组成网页的部件，例如超链接、按钮、图片、表格、表单、分页条等等。

官网: https://element.eleme.cn/#/zh-CNListener

```
快速入门
    安装ElementUl组件库(在当前工程的目录下)，在命令行执行指令:
        npm install element-ui@2.15.3
    在main.js文件中弓|入ElementUl组件库
        import ElementUI from 'element-ui' ;
        import 'element-ui/lib/theme-chalk/index.css';
        Vue.use(ElementUI);
	将ElementUI的代码放在template下面的div标签，然后在APP.vue的template下使用element-view进行引入

常见组件
	Table表格
	Pagination分页
	Dialog对话框
	Form表单
	Container布局容器
Element 是基于Vue的，所以使用Element时必须要创建Vue对象
```


## Vue路由

```
前景：在管理系统中，我们点击部门管理展现部门相关的，点击员工管理就展现员工相关的，这就要用到路由
前端路由: URL中的hash(#号)与组件之间的对应关系。

Vue Router
介绍: Vue Router是Vue的官方路由。
组成:
    VueRouter: 路由器类,根据路由请求在路由视图中动态渲染选中的组件
    <router-link>: 请求链接组件，浏览器会解析成<a>
    <router-view>: 动态视图组件，用来渲染展示与路由路径对应的组件

安装(创建Vue项目时已选择)
npm install vue-router@3.5.1

定义路由
在router下的index.js修改routes里面的配置。然后在对应的vue页面中的组件中插入<router-link to="/dept">部门管理</router-link>进行展示，再然后在APP.vue的template的div下加入<router-view></router-view>进行动态展示即可
注意设置根路径
```


## 打包部署

```
打包
先使用npm run build进行打包，然后会在项目工程下生成一个dist文件
这里可以使用nginx进行部署前端静态资源
介绍: Nginx是一款轻量级的Web服务器/反向代理服务器及电子邮件(IMAP/POP3)代理服务器。其特点是占有内存少，并发能力
强。

部署
部署:将打包好的dist目录下的文件，复制到nginx安装目录的html目录下。

启动:双击nginx.exe文件即可，Nginx服务器默认占用80端口号，可以修改为90
Nginx默认占用80端口号,如果80端口号被占用，可以在nginx.conf中修改端口号。(netstat -ano | findStr 80)


jar:普通模块打包，springboot项目基本都是jar包( 内嵌tomcat运行)
war:普通web程 序打包，需要部署在外部的tomcat服务器中运行
pom:父工程或聚合工程，该模块不写代码,仅进行依赖管理


```


# `Maven`

概述：Maven是apache旗下的一个开源项目，是一款用于管理和构建java项目的工具。

作用：依赖管理，统一项目结构，项目构建

前景：比如在以前使用其他jar包，一般都是在工程目录下创建lib文件夹，将jar包放进去并加载到项目里。但有了maven可以直接在`pom.xml`导入对应jar包的坐标即可。

仓库地址：https://mvnrepository.com/

中央仓库地址：https://repo1.maven.org/maven2/

坐标：坐标主要由如下组成，坐标就是为了方便寻找想要的jar包

groupld：定义当前maven项目属于的组织名称(通常是域名反写：org.mybatis)

artifactld：定义maven项目名称(通常是模块名称，例如CRM，SMS)

version：定义当前项目版本号

packaging：定义该项目的打包方式

maven项目目录结构

resources：一般写配置文件

IDEA导入Maven项目

方式一：打开IDEA,选择右侧Maven面板，点击+号,选中对应项目的`pom.xml`文件,双击即可。

方式二：可以直接在项目结构中的moudle导入这个项目的`pom.xml`文件


## 分模块开发

概述：将一个项目根据其各个功能，拆分成相对应的模块。

实现：可以将通用的模块(如poho，utils)单独以maven模块分别创建出来，其他各个功能模块使用时，通过依赖坐标进行使用。

比如将实体类和工具类都单独创建一个模块，将其他功能放入一个模块。

步骤：

- ① 先进行模块拆分

- ② 在该模块pom依赖里面引入另一个模块的坐标(在pom里面有)

- ③ 然后对该模块进行install打包，把他打包到本地仓库(因为idea能找不到坐标，但是本地仓库没有)

注意：分模块开发需要先针对模块功能进行设计，再进行编码。不会先将工程开发完毕，然后进行拆分。


## 依赖管理

### 依赖传递

依赖具有传递性，可间接传递也可也直接传递，但是会出现冲突。也就是说C依赖了B，但B依赖了A，则C中的依赖就含有A

在idea中可以通过右键-Diagrams-Show-Dependencies进行展示项目依赖的关系


### 依赖冲突问题

路径优先：当依赖中出现相同的资源时，层级越深，优先级越低，层级越浅，优先级越高

声明优先：当资源在相同层级被依赖时，配置顺序靠前的覆盖配置顺序靠后的

特殊优先：当同级配置了相同资源的不同版本，后配置的覆盖先配置的


### 可选依赖

概述：可选依赖指对外隐藏当前所依赖的资源---不透明

在依赖里面加一个属性optional，设置为true表示隐藏

可选依赖是隐藏当前工程所依赖的资源，隐藏后对应资源将不具有依赖传递


### 排除依赖

概述：排除依赖指主动断开依赖的资源，被排除的资源无需在对应的exclusion声明版本。

步骤：

在主要模块pom里面的依赖添加标签`<exclusions> <exclusion>添入坐标</exclusion> </exclusions>`,并把不需要的依赖坐标写里面。


### 依赖范围

前景：依赖的jar包，默认情况下，可以在任何地方使用。

使用：在对应坐标里添加< scope>test / provided / runtime </ scope >设置其作用范围。

作用范围：

- ① 主程序范围有效。 (main文件夹范围内)

- ② 测试程序范围有效。 (test文件夹范围内)

- ③ 是否参与打包运行。(package指令范围内)

`<img src="D:\Users\作业\知识点\java\图片\依赖范围.png" style="zoom:67%;" />`


## 生命周期

概述：Maven的生命周期就是为了对所有的maven项目构建过程进行抽象和统一。

每套生命周期包含一些阶段(phase)，阶段是有顺序的，后面的阶段依赖于前面的阶段。

Maven中有3套相互独立的生命周期：

- ① clean: 清理工作。

clean：移除上一次构建生成的文件

- ② default: 核心工作，如:编译、测试、打包、安装、部署等。

compile：编译项目源代码

test：使用合适的单元测试框架运行测试(junit)

package：将编译后的文件打包，如: jar、 war等

install：安装项目到本地仓库

- ③ site: 生成报告、发布站点等。

注意：在**同一套**生命周期中，当运行后面的阶段时，前面的阶段都会运行。

相关命令(在maven项目跟目录下打开cmd)

mvn compile编译 mvn clean清理 mvn test测试 mvn package打包 mvn install安装到本地仓库

可以在idea中点击闪电的图标跳过测试阶段，并且在idea中分为Lifecycle和Plugins，其实点击Lifecycle的命令就是使用了Plugins来执行。


## 继承

概述：描述的是两个工程间的关系，与java中的继承相似，子工程可以继承父工程中的配置信息，常见于依赖关系的继承。只能单继承。

作用：简化依赖配置，减少版本冲突，统一管理依赖。

思路：可以将各个模块中共有的依赖将其抽取出来，创建一个父工程，并将共有依赖放入其中，子工程只需继承就可使用。

实现：

- ① 可以创建一个maven模块父工程，通过`<packaging>`标签设置打包方式为pom(默认jar)，并将该工程继承spring-boot-starte-parent起步依赖。该模块不编写代码，只进行依赖管理。

- ② 在子工程中的pom文件里面用< parent>标签输入groupId，artifactId，version，relativePath。其中relativePath表示父工程`pom.xml`的相对路径，如果不填写，默认从本地仓库寻找。

注意：

- ① 子工程一旦继承，就不需要定义自身的groupId了。子工程不用定义依赖，可以直接用父类的，并且跟父类是同步的，方便统一管理。

- ② 若要统一管理版本。可以在 只进行依赖管理的父工程中的pom定义依赖管理< dependencyManagement>，在这个标签里面定义可选依赖的坐标，子类如果想用需要直接在依赖导入可选依赖坐标即可，但是不能配置版本，如果配了就不属于父类管理了

尽量将项目结构根据父子工程加上层级关系，可以更加清晰。

```
<dependencies>是直接依赖,在父工程配置了依赖,子工程会直接继承下来。
<dependencyManagement>是统一管理依赖版本,不会直接依赖，还需要在子工程中引入所需依赖(无需指定版本)
```


## 聚合

前景：在经过分模块开发后，有时需要将部分模块统一打包部署，但此时需要一个一个手动打包，需要一键打包等管理。因此有了聚合。

概述：将多个模块组织成一个整体，同时进行项目构建的过程称为聚合

聚合工程：通常是一个不具有业务功能的"空"工程（有且仅有一个pom文件）。一般和只进行依赖管理的父亲工程是一个工程，所以即是聚合工程，又是依赖管理的父工程。

作用：快速构建项目。使用聚合工程可以将多个工程编组，通过对聚合工程对其进行统一构建，实现对所包含的模块进行同步构建。

注意：

当工程中某个模块发生更新（变更）时，必须保障工程中与已更新模块关联的模块同步更新，此时可以使用聚合工程来解决批量模块同步构建的问题。

聚合工程中所包含的模块，在构建时，会自动根据模块间的依赖关系设置构建顺序，与聚合工程中模块的配置书写位置无关。

实现：

- ① 创建一个maven模块，然后设置打包类型为pom

- ② 使用moudle标签设置当前聚合工程所包含的子模块名称

```
<moudles>
	<moudle>../tlias-pojo</module>
	<moudle>../tlias-utils</module>
	<moudle>../tlias-web-management</module>
</modules>
注意：顺序不用管，他自己会按照依赖关系进行顺序构建
```


聚合和继承的区别

两种之间的作用:

聚合用于快速构建项目，对项目进行管理

继承用于快速配置和管理子项目中所使用jar包的版本

聚合和继承的相同点:

聚合与继承的`pom.xml`文件打包方式均为pom，可以将两种关系制作到同一个pom文件中

聚合与继承均属于设计型模块，并无实际的模块内容。

聚合和继承的不同点:

聚合是在当前模块中配置关系，聚合可以感知到参与聚合的模块有哪些

继承是在子模块中配置关系，父模块无法感知哪些子模块继承了自己


## 属性

### 定义属性

```
在pom文件里面定义标签<properties>然后直接定义属性即可用<属性>值</属性>，直接给版本号用属性代替，方便统一管理。
取值时用${属性名}即可
```

### 资源引用属性

如果要把数据库的properties文件的属性也放到pom里面统一管理，如下操作

步骤：

- ① 先把jdbc.url等属性定义到pom里面

- ② 配置文件中引用属性，就是把值换成取值符合那种形式${}

- ③ 然后开启资源文件目录加载属性的过滤器

```
	  	<build>
			<resources>
				<!--设置资源目录-->
				<resource>
					<directory>${project.basedir}/src/main/resources</directory>
					<!--设置能够解析${}，默认是false -->
					<filtering>true</filtering>
				</resource>
			</resources>
		</build>
	注意：${project.basedir}: 当前项目所在目录,子项目继承了父项目，相当于所有的子项目都添加了资源目录的过滤

	此时打包发现出现错误，需要web.xml文件才能打包
	解决方法一：直接加一个web.xml
	解决方法二：在bulld里面加一个插件属性重新编辑maven-war-plugin，并且给出配置属性<configuration>定义<failOnMissingWebXml>为false

	maven还有他内置的系统属性，${project.basedir}就属于内置的
```


## 多环境开发

```
定义多坏境<profiles>，<profile>定义各个环境，并给出id即为名字然后可以设置属性值，同时可以用<activation>里面设置默认环境，不过一般还是在idea右边的maven里面点击m图标，输入mvn install -P id进行用该环境打包
```


## 跳过测试

方法一点击maven，里面有个蓝色的M图标，点击即可跳过所有测试

方法二还是插件的方法，编辑maven-surefire-plugin设置配置属性即可

方法三指令mvn package -D skipTests


## 私服

概述：私服是一种特殊的远程仓库，它是架设在局域网内的仓库服务，用来代理位于外部的中央仓库，用于解决团队内部的资源共享与资源同步问题。

依赖查找顺序：本地仓库--私服--中央仓库

仓库分三大类

- ① 宿主仓库hosted

作用：保存无法从中央仓库获取的资源

自主研发，第三方非开源项目,比如Oracle,因为是付费产品，所以中央仓库没有，关联上传操作

- ② 代理仓库proxy

作用：代理远程仓库，通过nexus访问其他公共仓库，例如中央仓库，关联下载操作

- ③ 仓库组group

作用：将若干个仓库组成一个群组，简化配置，仓库组不能保存资源，属于设计型仓库

```
资源上传
	现在项目的pom中设置上传资源的url，然后在maven中配置私服的用户名和密码，之后进行deploy上传即可。
    <server>
        <id>maven-releases</id>
        <username>admink</username>
        <password>admin</password>
    </server>
    <server>
        <id>maven-snapshots</id>
        <username>admin</username>
        <password>admin</password>
    </server>

    <distributionManagement>
        <repository>
            <id>maven-releases</id>
            <ur1>http://192.168.150.101:8081/repository/maven-releases/</ur1>
        </repository>
        <snapshotRepository>
            <id>maven-snapshots</id>
            <ur1>http://192.168.150.101:8081/repository/maven-snapshots/</url>
        </snapshotRepository>
    </distributionManagement>

资源下载
	需要在maven中配置连接私服仓库组的url
	<mirror>
        <id>maven-pub1ic</id>
        <mirror0f>*</mirror0f>
        <ur1>http://192.168.150.101:8081/repository/maven-public/</url>
    </mirrpr>


在工程中配置私服的位置
    ①<distributionManagement>该标签配置私服
    ②然后配置<repository>表示正式版本,<snapshotRepository>表示快照版本
    ③然后配置对应的id和地址即可
    ④然后用deploy指令

项目版本:
RELEASE (发行版本) :功能趋于稳定、当前更新停止，可以用于发行的版本，存储在私服中的RELEASE仓库中。
SNAPSHOT (快照版本) :功能不稳定、尚处于开发中的版本，即快照版本，存储在私服的SNAPSHOT仓库中。

```


# `SpringBoot`

```
当前后端不分离时，Springboot项目的静态资源(html, CSS, js等 前端资源)默认存放目录为:
	classpath:/static	这个classpath一般是resources，一般使用这个
	classpath:/public
	classpath:/resources	在创建一个resources，不推荐
```


## 所有注解解释

```
@RestController：
	类型：类注解
	位置：Controller类上
	作用：表明这是一个请求处理类。将所有方法进行响应返回
	说明: @RestController = @Controller + @ResponseBody ;
@RequestMapping("/访问路径")：
    类型：类，方法注解
    位置：添加在表现层的方法上或者表现层的类上。
    作用：添加在方法上表示该方法的访问路径，添加在类上表示所有方法的公共访问路径。
    说明：完整的请求路径时公共路径+fang里面有value表示访问路径，method表示请求方法
@GetMapping：同上，不过直接代表以get形式方式，类似的有Put，Delete，Post等

@RequestParam(name="name")
    类型：参数注解
    位置：用在表现层的方法形参的前面
    作用：如果方法形参名称与请求参数名称不匹配，使用他完成映射。
    说明：@RequestParam中的required属性默认为true,代表该请求参数必须传递,如果不传递将报错400。如果该参数是可选的，可以将required属性设置为false。也可以通过属性defaultValue设置默认值。

@DateTimeFormat：完成日期参数格式转换，添加在方法形参的前面
	例如@DateTimeFormat(pattern="yyyy-MM-dd HH:mm:ss")

@RequestBody
    类型：参数注解
    位置：用在表现层的方法形参的前面
    作用：接收json参数封装到该形参对象中
    说明：

@PathVariable：表明接收的是路径参数，同时@RequestMapping也要将路径参数用{}表明起来。添加在方法形参的前面

@ResponseBody
    类型:方法注解、类注解
    位置: Controller方法上/类上
    作用:将方法返回值直接响应，如果返回值类型是实体对象/集合，将会转换为JSON格式响应
@Component
    类型：类注解
    位置：需要被IOC容器管理的类上
    作用：将该类交给IOC容器进行管理，成为IOC容器中的bean
    说明：常用于工具类
@Autowired
    类型：对象注解
    位置：进行依赖注入的对象
    作用：运行时，IOC容器会提供该类型的bean对象，并赋值给该变量。
    说明：默认按照类型装配
    注意：只有一个类型的bean可以自动装配
@Controller
    类型：类注解
    位置：需要被IOC容器管理的controller类上
    作用：将该类交给IOC容器进行管理，成为IOC容器中的bean
	说明：@Component的衍生注解。
@Service
    类型：类注解
    位置：需要被IOC容器管理的Services实现类上
    作用：将该类交给IOC容器进行管理，成为IOC容器中的bean
	说明：@Component的衍生注解。
@Repository(由于与mybatis整合，用的少)
    类型：类注解
    位置：需要被IOC容器管理的dao层的类上
    作用：将该类交给IOC容器进行管理，成为IOC容器中的bean
    说明：@Component的衍生注解。
@ComponentScan
    类型：类注解
    位置：在启动类上
    作用：使bean对象能够加入到容器中
    说明：默认启动类中注解中带有，默认扫描的范围是启动类所在包及其子包。手动设置的时候需要将默认扫描的重新添加上。
@Primary
    类型：类注解
    位置：在被IOC管理的类上
    作用：设置该bean对象的优先级
@Qualifier
    类型：对象注解
    位置：在声明对象上方
    作用：设置该bean对象要使用谁
    说明：搭配autowired使用
@Resource
    类型：对象注解
    位置：在声明对象上方
    作用：设置该bean对象要使用谁
    说明：去掉autowired，直接在对象上方的注解，在name属性中标明要使用的bean对象名称。默认按照名称注入。它是由jdk提供的
@Mapper
    类型：类注解
    位置：在持久层的类上方
    作用：在运行时,会自动生成该接口的实现类对象(代理对象)，并且将该对象交给IOC容器管理
@Options
	说明：@Options(keyProperty = "id", useGeneratedKeys = true)表示开启主键返回，并将主键封装到id属性中
@Slf4j
    位置：在表现层类上
    作用：简化提供一个名为log的日志对象，不用手动的去创建
@Value
    类型：属性注解
    位置：需要外部值注入的属性
    作用：只能一个一个的进行外部属性的注入。
    说明：
@ConfigurationProperties
    类型：
    位置：
    作用：可以批量的将外部的属性配置注入到bean对象的属性中
    说明：因为使用@Value进行一个一个标注太繁琐，故有了它
	属性：prefix指定配置文件中的属性前缀
@WebFilter
@ServletComponentScan
@RestControllerAdvice
    类型：
    位置：
    作用：可以批量的将外部的属性配置注入到bean对象的属性中
    说明：@RestControllerAdvice = @ControllerAdvice + @ResponseBody
@ExceptionHandler
@Transactional
	位置：service层的方法上、类上(将当前类中所有方法都交给spring事务管理)、接口上(将当前实现该接口的所有类的所有方法都交给spring事务管理)
	作用:将当前方法交给spring进行事务管理，方法执行前，开启事务;成功执行完毕，提交事务;出现异常，回滚事务
	注意：还需要在配置文件开启事务管理日志
@Aspect
	位置：AOP类上
	作用：说明这是一个切面类
@Around
	位置：AOP类中的方法上
	作用：指定针对哪些特定方法，也叫切入点表达式
@Poincut
	位置：方法上
	作用：该注解的作用是将公共的切点表达式抽取出来，需要用到时引用该切点表达式即可。
	使用：Poincut("切入点表达式")
	说明：当方法的修饰符为私有只能在本类中使用，public表示都能用
@Order
	位置：AOP类上
	作用：定义通知的优先级
	使用：@Order(数字)
	说明：目标方法前的通知方法:数字小的先执行。目标方法后的通知方法:数字小的后执行
@Lazy
	位置：类上
	作用：延迟到第一次使用bean时初始化
	使用：
	说明：
@Scope
@Bean
	位置：方法上
	作用：将方法返回值交给I0C容器管理，成为IOC容器的bean对象
	说明：可以通过@Bean注解的name/value属性指定bean名称， 如果未指定，默认是方法名。
@Conditional
    作用:按照一定的条件进行判断,在满足给定条件后才会注册对应的bean对象到Spring I0C容器中。
    位置:方法、类
	说明：@Conditional本身是一个父注解，派生出大量的子注解:
    ●@ConditionalOnClass: 判断环境中是否有对应字节码文件,才注册bean到IOC容器。
    ●@ConditionalOnMissingBean:判断环境中没有对应的bean (类型或名称)，才注册bean到I0C容器。
    ●@ConditionalOnProperty:判断配置文件中有对应属性和值,才注册bean到IOC容器。
```


## 快速入门

- ① 创建springboot工程(在idea新建项目中选择Spring Initializr)，并勾选web开发相关依赖。

- ② 在controller包下定义HelloController类，在类上添加`@RestController`注解，添加方法hello,在方法上添加`@RequestMapping`("/hello")注解。

- ③ 运行启动方法

起步依赖：

- ① spring-boot-starter-web: 包含了web应用开发所需要的常见依赖(内置了Tomcat)。

- ① spring-boot-starter-test:包含了单元测试所需要的常见依赖。(自带)


## 请求和响应

请求(`HttpServletRequest`)：获取请求数据

响应( `HttpServletResponse`)：设置响应数据

BS架构：Browser/Server, 浏览器/服务器架构模式。客户端只需要浏览器，应用程序的逻辑和数据都存储在服务端。

CS架构: Client/Server, 客户端/服务器架构模式。

前端控制器DispatcherServlet

其实正常浏览器向Web服务器发送请求时，是无法直接与controller层进行连接，因为tomcat是servlet的容器，需要先通过springboot提供的DispatcherServlet类，再将请求转给controller层。简单来说就是浏览器的请求和controller层需要通过DispatcherServlet类来塔桥。

然后前端控制器进行分析Http内容，将其封装到HttpServletRequest对象中。响应的时候是HttpServletResponse对象


### 接口测试工具

前景：post请求等需要用到APIFox，Postman等测试工具

项目中的接口文档，就是对controller类中的所有方法也就是接口进行解释说明


### 简单参数

原始方式：在原始的web程序中,获取请求参数,需要通过HttpServletRequest对象手动获取。

```
在controller当中定义一个如下方法进行接收参数
@RequestMapping("/simpleParam")
public String simpleParam(HttpServletRequest request){
    String name = request.getParameter("name");		//这个就是页面传输过来的参数名称
    String ageStr = request.getParameter("age"); 	//这个就是页面传输过来的参数名称
    int age = Integer.parselnt(ageStr);
    System.out.println(name+" : "+age);
    return "OK";		//会响应一个ok
}
在get后面添加?name=Tom&age=10。这个就是get添加参数的方式
```

Springboot：保证参数名与形参变量名相同，定义形参即可接收参数。

```
在controller当中定义一个如下方法进行接收参数
@RequestMapping("/simpleParam")
public String simpleParam(String name , Integer age){	//会自动进行类型转换
    System.out.println(name+" : "+age);
    return "OK";
}

如果方法形参名称与请求参数名称不匹配，可以使用@RequestParam完成映射。
@RequestMapping("/simpleParam")
public String simpleParam(@RequestParam(name = "name") String username , Integer age){
	System.out.println(username + " : " + age);
	return "OK";
}
注意：@RequestParam中的required属性默认为true,代表该请求参数必须传递,如果不传递将报错400。如果该参数是可选的，可以将required属性设置为false。

```


### 实体参数

简单实体对象：保证请求参数的各个名与形参对象属性名相同，然后定义POJO(实体类对象)在形参处接收即可

复杂实体对象：请求参数名与形参对象属性名相同，按照对象层次结构关系即可接收嵌套POJO属性参数。

```
比如在name中嵌套了address对象，且address里面有city和province，使用address.city进行传值。
```


### 数组集合参数

前景：比如多选框的场景下，需要传输多个值

数组参数：确保请求参数名与形参数组名称相同且请求参数为多个，定义数组类型形参即可接收参数

集合参数：请求参数名与形参集合名称相同且请求参数为多个，`@RequestParam` 绑定参数关系

```
采用数组来接收
@RequestMapping("/arrayParam' )
public String arrayParam(String[] hobby){}	//数组名和请求参数名保持一致，因为在请求参数中加有多个相同名字的不同值

采用集合来接收
@RequestMapping("/listParam")
public String listParam(@RequestParam List<String> hobby){}
```


### 日期参数

日期参数：使用`@DateTimeFormat`注解完成日期参数格式转换

```
@RequestMapping(" /dateParam")
public String dateParam(@DateTimeFormat(pattern="yyyy-MM-dd HH:mm:ss") LocalDateTime updateTime){}

```


### Json参数

JSON参数：保证JSON数据键名与形参对象属性名相同，定义POJ0类型形参即可接收参数，需要使用`@RequestBody`标识

服务端一般使用对象来接收json数据

```
@RequestMapping("/jsonParam")
public String jsonParam(@RequestBody User user){}
```


### 路径参数

该参数既是参数，也是请求路径的一部分

路径参数：通过请求URL直接传递参数，在`@RequestMapping`中使用 {...} 来标识该路径参数，同时需要使用`@PathVariable`获取路径参数

```
@RequestMapping("/path/{id}")
public String pathParam( @PathVariable Integer id){}
```

传递多个路径参数，只需要在请求路径中用/分隔进行添加即可，同时在controller接收时添加即可


### 设置响应数据

`@ResponseBody`：将方法返回值直接响应，如果返回值类型是实体对象/集合，将会转换为JSON格式响应

如果需要返回的值有多个，这时需要考虑将需要返回的属性封装到实体类中，并将该实体类返回


### 统一响应结果

前景：保证能够使用统一的响应格式进行返回，便于维护

步骤：

- ① 在pojo实体类中创建一个Result类，里面至少包含code，msg，data

```java
public class Result {
    private Integer code ;//1 成功 , 0 失败
    private String msg; //提示信息
    private Object data; //数据 data

    public Result() {}	//无参构造
    public Result(Integer code, String msg, Object data) {		//有参构造
        this.code = code;
        this.msg = msg;
        this.data = data;
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
	// 下面这些静态方法主要是快速提供一个result对象，有的可以带数据，有的不带数据
    public static Result success(Object data){
        return new Result(1, "success", data);
    }
    public static Result success(){
        return new Result(1, "success", null);
    }
    public static Result error(String msg){
        return new Result(0, msg, null);
    }

    @Override
    public String toString() {
        return "Result{" +
                "code=" + code +
                ", msg='" + msg + '\'' +
                ", data=" + data +
                '}';
    }
}
```


## 分层解耦

前景：如果把所有数据的逻辑处理代码都放controller的方法中，会造成代码的复用性和扩展性非常差。因此需要解耦


### 三层架构

controller：控制层，接收前端发送的请求，对请求进行处理，并响应数据。

controller需要service进行业务处理，因此需要在controller中创建私有的service对象

service：业务逻辑层，处理具体的业务逻辑。

service层同dao层一样，也分为接口层和实现层。实现层放具体数据逻辑处理的代码。

service需要调用dao层的对象获取数据，因此需要在service的实现层创建私有的dao层对象。

dao：数据访问层(Data Access Object) (持久层) ,负责数据访问操作，包括数据的增、删、改、查。

而dao层可能需要从文档中进行数据处理，也可能从数据库取数据，因此可以采用接口编程提高适用性。

所以dao层分为接口层和impl实现类层

访问顺序：浏览器的请求发给controller，然后controller调用service，然后service需要先调用dao层，拿到数据再处理。


### 分层解耦

内聚：软件中各个功能模块内部的功能联系。

耦合：衡量软件中各个层/模块之间的依赖、关联的程度。

原则：高内聚低耦合

```
例子：上面三层架构中，controller和service之间的耦合。
要将类中自己new对象的情况进行去掉达到解耦，然光移除会造成声明对象的地方出现空指针异常，也就是对象没赋值。所以需要容器进行依赖注入
解耦思路：service对象的名字如果来回变换，那controller也需要改，因此可以考虑先将controller中new service对象去掉。
此时可以假设有一个容器，service将他的对象放进去，然后controller从里面寻找她所需要的对象。然后他将这个对象赋值给所声明的service对象。

其中这个容器叫做IOC容器或者叫Spring容器，service将他的对象放到容器的思想叫做控制反转，容器为controller提供她所需要的对象叫做依赖注入。容器中的对象都叫bean对象
```

控制反转：Inversion Of Control,简称I0C。对象的创建控制权由程序自身转移到外部(容器)，这种思想称为控制反转。

依赖注入：Dependency Injection,简称DI。容器为应用程序提供运行时，所依赖的资源，称之为依赖注入。

Bean对象：I0C容器中创建、管理的对象，称之为bean。


### `IOC`

控制反转：Inversion Of Control,简称I0C。对象的创建控制权由程序自身转移到外部(容器)，这种思想称为控制反转。

反转之前，需要对象直接再类中new对象，反转之后由容器统一管理。

步骤：

- ① Service层及Dao层的实现类,交给I0C容器管理。需要在类上声明`@Component`注解

- ② 为Controller及Service注入运行时，依赖的对象。在声明对象上方标上`@Autowired`注解

```
要把某个对象交给I0C容器管理，需要在对应的类上加上如下注解之一:

@Component：声明bean的基础注解。不属于以下三类时，用此注解，比如工具类
@Controller：@Component的衍生注解。位置标注在控制器类上
@Service：@Component的衍生注解。位置标注在业务类上
@Repository：@Component的衍生注解。位置标注在数据访问类上(由于与mybatis整合，用的少)

```


### `DI`

依赖注入：Dependency Injection,简称DI。容器为应用程序提供运行时，所依赖的资源，称之为依赖注入。


### `Bean`

Bean对象：I0C容器中创建、管理的对象，称之为bean。

在IOC容器中，每一个bean都有名字，在声明bean的时候，可以通过注解当中的value属性来指定名字。默认名字为类名首字母小写。


#### Bean组件扫描

前面声明bean的四大注解，要想生效，还需要被组件扫描注解`@Component`Scan扫描。

`@Component`Scan注解虽然没有显式配置,但是实际上已经包含在了启动类声明注解`@SpringBootApplication`中，默认扫描的范围是启动类所在包及其子包。

手动设置value属性可以设置扫描范围，如com.itheima.dao


#### Bean注入

当出现多个相同类型的bean对象进行`@Autowired`自动装配时，会发生错误，此时需要采取一下措施

@Primary：在类上直接设置bean的优先级高

`@Qualifier`：在声明对象上面指定该注解的value属性，指定需要的bean对象名字

`@Resource`：默认按照名称注入，在对象上添加该注解，并将name属性指定要注入的bean对象名


## 开发规范

### Restful

概述：`REST` (REpresentational State Transfer)， 表述性状态转换，它是一种软件架构风格

注意：描述模块的功能通常使用复数，也就是加s的格式来描述，表示此类资源，而非单个资源。如: users、 emps、books...


### 统一响应结果

前景：保证能够使用统一的响应格式进行返回，便于维护

步骤：

- ① 在pojo实体类中创建一个Result类，里面至少包含code，msg，data

```java
public class Result {
    private Integer code ;//1 成功 , 0 失败
    private String msg; //提示信息
    private Object data; //数据 data

    public Result() {}	//无参构造
    public Result(Integer code, String msg, Object data) {		//有参构造
        this.code = code;
        this.msg = msg;
        this.data = data;
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
	// 下面这些静态方法主要是快速提供一个result对象，有的可以带数据，有的不带数据
    public static Result success(Object data){
        return new Result(1, "success", data);
    }
    public static Result success(){
        return new Result(1, "success", null);
    }
    public static Result error(String msg){
        return new Result(0, msg, null);
    }

    @Override
    public String toString() {
        return "Result{" +
                "code=" + code +
                ", msg='" + msg + '\'' +
                ", data=" + data +
                '}';
    }
}
```

```java
@Data
@NoArgsConstructor
@AllArgsConstructor
public class Result {
    private Integer code;//响应码，1 代表成功; 0 代表失败
    private String msg;  //响应信息 描述字符串
    private Object data; //返回的数据

    //增删改 成功响应
    public static Result success(){
        return new Result(1,"success",null);
    }
    //查询 成功响应
    public static Result success(Object data){
        return new Result(1,"success",data);
    }
    //失败响应
    public static Result error(String msg){
        return new Result(0,msg,null);
    }
}
```


## 开发流程

- ① 查看页面原型

- ② 阅读接口文档

- ③ 思路分析

- ④ 接口开发

- ⑤ 接口测试

- ⑥ 前后端联调

- ⑦ 明确需求


## 文件上传

简介：文件上传，是指将本地图片、视频、音频等文件.上传到服务器，供其他用户浏览或下载的过程。

应用场景：文件上传在项目中应用非常广泛，我们经常发微博、发微信朋友圈都用到了文件上传功能。

实现：他的实现涉及到前端和服务端

```
在前端必备的
<form action="/upload" method="post" enctype="multipart/form-data">
    姓名: <input type=" text" name="username"><br>
    年龄: <input type="text" name="age"><br>
    头像: <input type="file" name="image"><br>
    <input type=" submit" value="提交">
</form>
注意：enctype设置表单编码格式，默认的www的编码格式只会提交文件名字，而不会提交文件的内容。而multipart/form-data，会将表单数据分成多个部分进行提交。


服务端接收文件
@RestController
public class UploadController {
    @PostMapping("/upload")
    public Result upload(String username, Integer age, MultipartFile image) {
    	log.info("文件上传:{},{},{}", username ,age,image) ;
    	return Result.success();
    }
}
注意：
	①普通的表单项可以直接用参数接收，但文件类型的需要用MultipartFile image(表单项目名称)参数来接收。
	②当文件传输过来时，在没有响应完毕之前，他会以临时文件的形式保存到本地，但在响应完之后就会删除，因此需要保存文件。
```


### 本地存储

概述：在服务端，接收到上传，上来的文件之后,将文件存储在本地服务器磁盘中。

缺点：无法在浏览器直接访问到本地文件，花销磁盘空间大。

```
MultipartFile的方法：
    ●String getOriginalFilename(); //获取原始文件名
    ●void transferTo(File dest); //将接收的文件转存到磁盘文件中
    ●long getSize(); //获取文件的大小，单位:字节
    ●byte[] getBytes(); //获取文件内容的字节数组
    ●InputStream getInputStream(); //获取接收到的文件内容的输入流

接收文件以原始名称保存
    @PostMapping("/upload")
    public Result upload(String username, Integer age, MultipartFile image) {
    	log.info("文件上传:{},{},{}", username ,age,image) ;
    	//获取原始文件名
        String originalFilename = image.getOriginalFilename() ;
        //将文件存储在服务器的磁盘目录中E:\images
        image.transferTo (new File("E:\\images\\" + originalFilename));
    	return Result.success();
    }
问题：会以文件的原名称保存，容易出现覆盖问题，优化构造唯一的文件名，如uuid，但需要拼接文件后缀名

优化
    @PostMapping("/upload")
    public Result upload(String username, Integer age, MultipartFile image) {
    	log.info("文件上传:{},{},{}", username ,age,image) ;
    	//获取原始文件名
        String originalFilename = image.getOriginalFilename() ;

        // 构造唯一的文件名
        int index = originalFilename.lastIndexOf(".") ;	// 获取文件后缀
        String extname = originalFilename.substring (index) ;
        String newFileName = UUID.randomUUID().toString() + extname;
        log.info("新的文件名: {}", newFileName);

        //将文件存储在服务器的磁盘目录中E:\images
        image.transferTo(new File("E:\\images\\" + newFileName));
    	return Result.success();
    }
问题：会出现内存大小超出的问题，boot默认单个文件最大为1MB，如需要传输大文件添加以下配置：
    #配置单个文件最大上传大小
    spring.servlet.multipart.max-file-size=10MB
    #配置单个请求最大上传大小(一次请求可以上传多个文件)
    spring.servlet.multipart.max-request-size=100MB
```


### 阿里云OSS

简介：阿里云对象存储OSS ( Object Storage Service)，是一款海量、安全、低成本、高可靠的云存储服务。使用OSS,您可以通过网络随时存储和调用包括文本、图片、音频和视频等在内的各种文件。

```
使用步骤：
	①引入依赖
	②使用官方提供的上传文件的代码，将本地文件上传到oss，该类放在utils包下，并用@Component注解交给IOC容器管理
	③将其对象注入到表现层进行使用
注意：这种方式在数据库存的是url，而本地存储存的是图片路径
```


## 配置文件

### 参数配置化

前景：比如使用第三方的服务，如oss中需要的key等，这时可以将他们的赋值移交给配置文件，方便维护

```
#自定义的阿里云Oss配置信息
aliyun.os3.endpoint=https://oss-cn-hangzhou.aliyuncs.com
aliyun.oss.accessKeyId=你的AccessKeyId
aliyun.oss.accessKeySecret=你的AccessKeySecret
aliyun.os3.bucketName=web-tlias
@Value注解通常用于外部配置的属性注入,具体用法为: @Value("${配置文件中的key}")

#在工具类修改
@Component
public class AlioSSUtils {
    @Value ("S{aliyun.oss.endpoint}")
    private String endpoint;

    @Value ("${aliyun.oss.accessKeyId}")
    private String accessKeyId;

    @Value ("${aliyun.oss.accessKeySecret}")
    private String accessKeySecret;

    @Value ("${aliyun.oss.bucketName}")
    private String bucketName;
```


### yml配置文件

规范：

- 大小写敏感

- 数值前边必须有空格, 作为分隔符

- 使用缩进表示层级关系, 缩进时，不允许使用Tab键,只能用空格(idea中会自动将Tab转换为空格)

- 缩进的空格数目不重要，只要相同层级的元素左侧对齐即可

- #表示注释,从这个字符一直到行尾，都会被解析器忽略

```
#定义对象/Map集合
user:
  name: Tom
  age: 20
  address: beijing

#定义数组/List/Set
hobby:
  - java
  - C
  - game
  - sport
```


### ConfigurationProperties注解

前景：因为通过value注入需要为每一个需要赋值的属性进行标注，太繁琐，可以使用该注解统一配置。

使用条件：

- ① 创建一个新的类，需要为该类提供get和set方法

- ② 需要交给IOC容器管理，即添加`@Component`注解

- ③ 在该类添加注解``@Configuration`Properties`，并指定prefix属性

- ④ 在配置文件处的名字要与属性名相同

使用：

- ① 在使用这个类的地方声明对象，进行自动注入即可


```
该类一般在utils包下，类名如AliOSSProperties，在下例中，prefix就是"aliyun.oss"
类如下：
@Data
@Component
@ConfigurationProperties(prefix = "aliyun.oss")
public class Al iOSSProperties {
    private String endpoint;
    private String accessKeyId;
    private String accessKeySecret;
    private String bucketName;
}
配置如下：
aliyun:
	oss:
        endpoint: https:/ /oss-cn-hangzhou. aliyuncs . com
        accessKeyId: 你的AccessKeyId
        accessKeySecret: 你的AccessKeySecret
        bucketName: web-tlias

同时可选择导入配置时提示的相关依赖
<dependency>
    <groupId>org.springframework.boot</ groupId>
    <artifactId> spring-boot-configuration-processor</artifactId>
</dependency>
```


### 配置优先级

常用yml。优先级：命令行参数>java系统属性>properties>yml>yaml

注意：不同配置文件中相同配置按照加载优先级相互覆盖，不同配置文件中不同配置全部保留

SpringBoot除了支持配置文件属性配置，还支持Java系统属性和命令行参数的方式进行属性配置。

Java系统属性

-Dserver.port=9000

命令行参数

--server.port=10010

注意：在idea中编辑运行环境中的VM options即代表配置java系统属性。其中的Program arugments是用来配置命令行参数的


## 事务管理&`AOP`

### 事务管理

概述：事务是一组操作的集合，它是一个不可分割的工作单位，这些操作要么同时成功，要么同时失败。

相关操作：

- ① 开启事务(一组操作开始前，开启事务) : start transaction / begin ;

- ② 提交事务(这组操作全部成功后，提交事务) : commit ;

- ③ 回滚事务( 中间任何一个操作出现异常，回滚事务) : rollback ;


#### spring事务管理

`@Transactional`

位置：service层的方法上、类上(将当前类中所有方法都交给spring事务管理)、接口上(将当前实现该接口的所有类的所有方法都交给spring事务管理)

作用：将当前方法交给spring进行事务管理，方法执行前，开启事务;成功执行完毕，提交事务;出现异常，回滚事务

注意：还需要在配置文件开启事务管理日志

```
#开启事务管理日志
logging:
	level:
		org.springframework.jdbc.support.jdbcTransactionManager: debug
```


#### 使用细节

关于`@Transactional`的属性

rollbackFor：默认情况下，只有出现RuntimeException才回滚异常。rollbackFor属性用于控制出现何种异常类型，回滚事务。

propagation：指定事务传播行为

```
事务传播行为：指的就是当一个事务方法被另一个事务方法调用时，这个事务方法应该如何进行事务控制。
关于propagation的值：
    REQUIRED：[默认值]需要事务，有则加入，无则创建新事务
    REQUIRES_NEW：需要新事务，无论有无，总是创建新事务
    SUPPORTS：支持事务，有则加入，无则在无事务状态中运行
    NOT_ SUPPORTED：不支持事务，在无事务状态下运行,如果当前存在已有事务，则挂起当前事务
    MANDATORY：必须有事务，否则抛异常
    NEVER：必须没事务，否则抛异常
简单来说
REQUIRED:大部分情况下都是用该传播行为即可。
REQUIRES_NEW :当我们不希望事务之间相互影响时，可以使用该传播行为。比如:下订单前需要记录日志，不论订单保存成功与否，都需要保证日志记录能够记录成功。
```


### AOP基础

概述：Aspect Oriented Programming ( 面向切面编程、面向方面编程)，其实就是面向特定方法编程。

简单来说就是想要对一部分的方法进行功能的增加或修改，然后使用AOP选住这些特定方法，为他们添加或修改。

场景：记录操作日志，权限控制，事务管理。

实现：动态代理是面向切面编程最主流的实现。而SpringAOP是Spring框架的高级技术，旨在管理bean对象的过程中，主要通过底层的动态代理机制，对特定的方法进行编程。

优点：代码无侵入，减少重复代码，提高开发效率，维护方便


相关概念

连接点：JoinPoint，可以被AOP控制的方法(暗含方法执行时的相关信息)

通知： Advice, 指哪些重复的逻辑，也就是共性功能( 最终体现为一个方法)

切入点：PointCut, 匹配连接点的条件，通知仅会在切入点方法执行时被应用

切面：Aspect, 描述通知与切入点的对应关系(通知+切入点)

目标对象：Target, 通知所应用的对象

```
快速入门：统计各个业务层方法执行耗时
①导入依赖:在pom.xml中导入AOP的依赖
<dependency>
    <groupId>org. springframework. boot</ groupId>
    <artifactId>spring- boot-starter- aop</artifactId>
</dependency>
②编写AOP程序:针对于特定方法根据业务需要进行编程
在aop层创建该AOP类
@Component
@Aspect
public class TimeAspect {
	@Around("execution(com.itheima.service.*.*(..))")		//指定针对哪些特定方法，也叫切入点表达式。
    public object recordTime(ProceedingJoinPoint proceedingJoinPoint) throws Throwable {
        long begin = System.currentTimeMillist();
        object object = proceedingJoinPoint.proceed(); //使用ProceedingJoinPoint的对象调用原始方法运行
        long end = System.currentTimeMillis();
        Log.info(proceedingJoinPoint.getSignature()+"执行耗时: {}ms", end - begin);
        return object;
    }
}
```


### AOP进阶

#### 通知类型

```
@Around:环绕通知，此注解标注的通知方法在目标方法前、后都被执行
@Before:前置通知，此注解标注的通知方法在目标方法前被执行
@After:后置通知，此注解标注的通知方法在目标方法后被执行，无论是否有异常都会执行。也叫最终通知
@AfterReturning :返回后通知，此注解标注的通知方法在目标方法后被执行，有异常不会执行
@AfterThrowing :异常后通知，此注解标注的通知方法发生异常后执行

注意：
    @Around环绕通知需要自己调用ProceedingJoinPoint . proceed()来让原始方法执行,其他通知不需要考虑目标方法执行
    @Around环绕通知方法的返回值，必须指定为0bject, 来接收原始方法的返回值。
当出现大量重复的切入点表达式的时，可以抽取出来，创建一个私有方法(表示只在当前类使用)，表上注解@Poincut("切入点表达式")，别人在使用时直接调该方法名即可。
@Poincut("切入点表达式")：该注解的作用是将公共的切点表达式抽取出来，需要用到时引用该切点表达式即可。

```


##### 通知顺序

当有多个切面的切入点都匹配到了目标方法，目标方法运行时，多个通知方法都会被执行。

```
不同切面类中，默认按照切面类的类名字母排序:
    ◆目标方法前的通知方法:字母排名靠前的先执行
    ◆目标方法后的通知方法:字母排名靠前的后执行
可以用@Order(数字)加在切面类上来控制顺序
    ◆目标方法前的通知方法:数字小的先执行
    ◆目标方法后的通知方法:数字小的后执行

```


##### 切入点表达式

概述：描述切入点方法的一种表达式。

作用：主要用来决定项目中的哪些方法需要加入通知

常见形式：

execution(......)：根据方法的签名来匹配

```
execution主要根据方法的返回值、包名、名、方法名、方法参数等信息来匹配。
语法为：execution(访问修饰符? 返回值 包名.类名.?方法名(方法参数) throws 异常?)
其中带?的表示可以省略的部分
    ◆访问修饰符:可省略(比如: public、protected)
    ◆包名.类名:可省略
    ◆throws 异常:可省略(注意是方法上声明抛出的异常,不是实际抛出的异常)
例如：@Pointcut("execution(public void com.itheima.service.impl.DeptServiceImpl.delete(java.lang.Integer))")

可以使用通配符描述切入点
    *：单个独立的任意符号，可以通配任意返回值、包名、类名、方法名、任意类型的一个参数,也可以通配包、类、方法名的一部分
    例如：execution(* com.*.service.*.update*(*))
    ..：多个连续的任意符号，可以通配任意层级的包，或任意类型、任意个数的参数
    例如：execution(* com.itheima..DeptService.*(..))

注意：根据业务需要，可以使用且(&&)、或(|)、非(!)来组合比较复杂的切入点表达式。

书写建议
    ●所有业务方法名在命名时尽 量规范,方便切入点表达式快速匹配。如:查询类方法都是find开头,更新类方法都是update开头。
    ●描述切入点方法通常基于接口描述,而不是直接描述实现类,增强拓展性。
    ●在满足业务需要的前提下， 尽量缩小切入点的匹配范围。如:包名匹配尽量不使用..，使用*匹配单个包。
```

@annotation(......) : 根据注解匹配

```
@annotation切入点表达式，用于匹配标识有特定注解的方法。
@annotation(注解全类名)
@Before("@annotation (com.itheima.anno.Log) ")
public void before() {
	log.info("before . ...") ;
}
```


##### 连接点

```
在Spring中用JoinPbint抽象了连接点，用它可以获得方法执行时的相关信息，如目标类名、方法名、方法参数等。
➢对于@Around 通知，获取连接点信息只能使用 ProceedingJoinPoint
➢对于其他四种通知，获取连接点信息只能使用JoinPoint(aspect.lang包下)，它是ProceedingJoinPoint的父类型

例如：
public object around(ProceedingJoinPoint joinPoint) throws Throwable {
    String className = joinPoint. getTarget(). getC1ass().getName(); 	//获取目标类名
    Signature signature = joinPoint.getSignature(); 	//获取目标方法签名
    String methodName = joinPoint.getSignature().getName(); 	//获取目标方法名
    object[] args = joinPoint.getArgs(); 	//获取目标方法运行参数
    object res = joinPoint.proceed(); 	//执行原始方法,获取返回值(环绕通知)
    return res;
}
```


### AOP案例

```
AOP核心：在于如何根据需求选择通知类型和切入点表达式

需求：将案例中增、删、改相关接口的操作日志记录到数据库表中。
操作日志：日志信息包含:操作人、操作时间、执行方法的全类名、执行方法名、方法运行时参数、返回值、方 法执行时长

思路分析
    需要对所有业务类中的增、删、改方法添加统一功能，使用AOP技术最为方便@Around 环绕通知
    由于增、删、改方法名没有规律，可以自定义@Log注解完成目标方法匹配

步骤
    准备
        ●在案例工程中引入AOP的起步依赖
        ●导入资料中准 备好的数据库表结构,并引入对应的实体类
    编码
        ●自定义注解 @Log。起到标识的作用
        ●定义切面类, 完成记录操作日志的逻辑
注意：获取当前登录用户
	●获取request对象， 从请求头中获取到jwt令牌,解析令牌获取出当前用户的id。

```


## Bean管理

### 获取bean

```
默认情况下，Spring项目启动时,会把bean都创建好放在I0C容器中。
如果想要主动获取这些bean,可以通过使用IOC对象的方法:
@Autowired
private ApplicationContext applicationContext; //IOC容器对象直接注入即可
    ◆根据name获取bean：Object getBean(String name)
    ◆根据类型获取bean：<T> T getBean(Class<T> requiredType)
    ◆根据name获取bean(带类型转换)：<T> T getBean(String name, Class<T> requiredType)
注意：上述所说的[Spring项目启动时, 会把其中的bean都创建好]还会受到作用域及延迟初始化影响，这里主要针对于默认的单例非延迟加载的bean而言。
```


### bean作用域

```
Spring支持五种作用域,后三种在web环境才生效:
    singleton：容器内同名称的bean只有一个实例(单例) (默认)
    prototype：每次使用该bean时会创建新的实例(非单例)
    request：每个请求范围内会创建新的实例(web环境中，了解)
    session：每个会话范围内会创建新的实例(web环境中，了解)
    application：每个应用范围内会创建新的实例(web环境中，了解)

可以通过@Scope注解来进行配置作用域:
    @Scope("prototype")
    @RestController
    @RequestMapping("/depts")
    public class DeptController{}
注意事项：
    默认singleton的bean,在容器启动时被创建，可以使用@Lazy注解来延迟初始化(延迟到第一次使用时 )。
    prototype的bean,每一次使用该bean的时候都会创建一个新的实例。
    实际开发当中，绝大部分的Bean是单例的,也就是说绝大部分Bean不需要配置scope属性。
```


### 第三方bean

前景：之前管理自定义的bean直接使用@component注解就可以，但现在导入的第三方bean是无法修改的。

实现：如果要管理的bean对象来自于第三方(不是自定义的) ,是无法用`@Component`及衍生注解声明bean的,就需要用到`@Bean`注解。

解释：`@Bean`注解就是将方法返回值交给I0C容器管理，成为IOC容器的bean对象。可以通过`@Bean`注解的name/value属性指定bean名称， 如果未指定，默认是方法名。

注意：添加`@Bean`注解的方法尽量创建在config层中

```
如下所示：
@Configuration
public class CommonConfig {
    @Bean
    public SAXReader saxReader(){
        return new SAXReader();
    }
}
//若第三方bean需要依赖其他bean对象，也就是需要依赖注入，直接在方法中定义对应的形参即可。spring容器会自动对其装配

适应场景：
项目中自定义的，使用@Component及其衍生注解
项目中引入第三方的，使用@Bean注解;
```


## SpringBoot原理

### 起步依赖

作用：大大的简化了pom文件的依赖配置。他的原理就是maven的依赖传递


### 自动配置

概述：SpringBoot的自动配置就是当spring容器启动后，一些配置类、bean对象就自动存入到了I0C容器中，不需要我们手动去声明，从而简化了开发,省去了繁琐的配置操作。

作用：大大的简化了框架使用时bean的声明和bean的配置。

```
注意：第三方包中如果不在包扫描范围内，是无法自动注入的，可以通过添加包扫描注解进行解决，但如果第三方包多了就不推荐了，在此推荐以下解决方案。
方案二: @lmport导入。使用@Import导入的类会被Spring加载到I0C容器中，导入形式主要有以下几种:
    ◆导入普通类
    ◆导入配置类
    ◆导入ImportSelector接口实现类
    ◆推荐的用法：先创建@EnableXxxxj自定义注解，使用@EnableXxxxj注解在启动类上，然后在自定义注解上使用@Import注解，导入需要依赖的第三方类，然后再对应的第三方类中定义String[ ] selectImports(...)


@Import({TokenParser.class, HeaderConfig.class})
@SpringBootApplication
public class SpringbootWebConfig2Application{}
```

```
●@SpringBootApplication注解标识在SpringBoot工程引导类上,是SpringBoot中最重要的注解。该注解由三个部分组成:
    ◆@SpringBootConfiguration:该注解与@Configuration注解作用相同，用来声明当前也是一一个配置类。
    ◆@ComponentScan:组件扫描，默认扫描当前引导类所在包及其子包。
    ◆@EnableAutoConfiguration: SpringBoot实现自动化配置的核心注解。

```


#### 条件配置

```
@Conditional
●作用:按照一定的条件进行判断,在满足给定条件后才会注册对应的bean对象到Spring I0C容器中。
●位置:方法、类
●@Conditional本身是一个父注解，派生出大量的子注解:
    ●@ConditionalOnClass: 判断环境中是否有对应字节码文件(通过name属性指定),才注册bean到IOC容器。
    ●@ConditionalOnMissingBean:判断环境中没有对应的bean (类型value或名称name)，才注册bean到I0C容器。
    ●@ConditionalOnProperty:判断配置文件中有对应属性和值,才注册bean到IOC容器。
```


### 自定义starter

场景：在实际开发中, 经常会定义一些公共组件,提供给各个项目团队使用。而在SpringBoot的项目中，一般会将这些公共组件封装为SpringBoot的starter。

```
需求:自定义aliyun-oss-spring-boot-starter, 完成阿里云OSS操作工具类AliyunOSSUtils的自动配置。
目标:引入起步依赖引入之后，要想使用阿里云OSS，注入AliyunOSSUtils直接使用即可。
步骤：
    ●创建aliyun-oss-spring-boot-starter模块(依赖管理功能)
    ●创建 aliyun-oss-spring-boot-autoconfigure模块(自动配置功能),在starter中引入该模块
    ●在aliyun-oss-spring-boot-autoconfigure模块中的定义自动配置功能,并定义自动配置文件META-INF/spring/xxxx.imports

```


# 登录

## 登录功能

实现思路：在controller中创建LoginController类，并且不需要创建新的mapper和service，可以使用员工管理相关的逻辑处理。


## 登录校验

前景：因为http协议是无状态的，因此你可以不登陆就能直接访问员工管理界面，因此需要作出登录校验。但如果在每一个功能前面都加上判断又特别繁琐，所以需要使用统一拦截。

分析：校验涉及到登录标记(会话技术)和统一拦截(Filter或者Interceptor)。


### 会话技术

会话：用户打开浏览器，访问web服务器的资源，会话建立,直到有一方断开连接，会话结束。在- -次会话中可以包含多次请求和响应。

会话跟踪：一种维护浏览器状态的方法，服务器需要识别多次请求是否来自于同一浏览器，以便在同一次会话的多次请求间共享数据。

会话跟踪方案:

- 客户端会话跟踪技术: `Cookie`

- 服务端会话跟踪技术: `Session`

- 令牌技术


#### `Cookie`

概述：客户端会话技术，将数据保存到客户端(浏览器)，以后每次请求都携带Cookie数据进行访问。

会话跟踪时的核心：请求头(用来携带cookie)和响应头(set-cookie用来设置cookie)

优点: HTTP协议中支持的技术

缺点:

- ① 移动端APP无法使用Cookie

- ② 不安全，用户可以自己禁用Cookie

- ③ Cookie不能跨域(协议，IP/域名，端口号，只要有一个不同就是跨域)

```
设置响应数据cookie：
可以直接在表现层方法中的形参添加HttpServletResponse response获取响应对象
①创建Cookie对象，并设置数据
	Cookie cookie = new Cookie("key","value");
②发送Cookie到客户端：使用response对象
	response.addCookie(cookie);

Cookie的获取：
可以直接在表现层方法中的形参添加HttpServletRequest Request获取请求对象
③获取客户端携带的所有Cookie，使用request对象
	Cookie[] cookies = request.getCookies();
④遍历数组，获取每一个Cookie对象
⑤使用Cookie对象方法获取数据
cookie.getName();
cookie.getValue();
```

```
使用细节：
	Cookie存活时间
		①默认情况下，Cookie存储在浏览器内存中，当浏览器关闭，内存释放，则Cookie被销毁
		②可以设置Cookie存活时间，用setMaxAge(int seconds)
	参数值为:
		1.正数：将Cookie写入浏览器所在电脑的硬盘，持久化存储。到时间自动删除
		2.负数：默认值，Cookie在当前浏览器内存中，当浏览器关闭，则Cookie被销毁
		3.零：删除对应Cookie

	Cookie存储中文(tomcat8以后就没了)
	步骤：
		1.在AServlet中对中文进行URL编码，采用URLEncoder.encode()，将编码后的值存入Cookie中
		2.在BServlet中获取Cookie中的值,获取的值为URL编码后的值
		3.将获取的值在进行URL解码,采用URLDecoder.decode()，就可以获取到对应的中文值
```


#### `Session`

概述：服务端会话跟踪技术，将数据保存到服务端。

优点：存储在服务端，安全

缺点：

服务器集群环境下无法直接使用Session

Cookie的缺点也包含了

```
在JavaEE中提供了HttpSession接口，来实现一次会话的多次请求之间数据共享功能。
存储值：
	①可以直接在表现层方法中的形参添加HttpSession session获取对象(若有则用，无则创)
	②使用setAttribute方法进行定义值

从HttpSession中取值：
	①在表现层方法中的形参添加HttpServletRequest Request获取请求对象
	②获取Session对象,使用的是request对象，即通过request对象的方法获取
	③使用getAttribute方法进行取值
		HttpSession session = request.getSession();
	Session对象提供的功能:
		①存储数据到session域中：void setAttribute(String name, Object o)
		②根据key，获取值：Object getAttribute(String name)
		③根据key，删除该键值对：void removeAttribute(String name)
```

```
使用细节：
Session的实现是居于Cookie的
	Sesion钝化和活化
		钝化：在服务器正常关闭后，Tomcat会自动将Session数据写入硬盘的文件中
		钝化的数据路径为:项目目录\target\tomcat\work\Tomcat\localhost\项目名称\SESSIONS.ser
		活化：再次启动服务器后，从文件中加载数据到Session中数据加载到Session中后，路径中的SESSIONS.ser文件会被删除掉
	小结
		Session的钝化和活化介绍完后，需要我们注意的是:
		session数据存储在服务端，服务器重启后，session数据会被保存
		浏览器被关闭启动后，重新建立的连接就已经是一个全新的会话，获取的session数据也是一个新的对象
		session的数据要想共享，浏览器不能关闭，所以session数据不能长期保存数据
		cookie是存储在客户端，是可以长期保存
```

```
Session的销毁：
	session的销毁会有两种方式:
		①默认情况下，无操作，30分钟自动销毁，对于这个失效时间，是可以通过配置进行修改的
		在项目的web.xml中配置：
		<session-config>
		<session-timeout>100</session-timeout>
		</session-config>
		②调用Session对象的invalidate()进行销毁
小结：如果需要登录后成功进行保存相关数据用session(安全)，长时间保存用cookie(不安全)
```


#### 令牌技术

优点：

支持PC端、移动端

解决集群环境下的认证问题

减轻服务器端存储压力

缺点：需要自己(前后端配合)实现

实现思路：在用户登录完成后，服务端生成一个jwt令牌，然后将该令牌下发给客户端，然后每次客户端请求时，服务端进行统一拦截并对其进行校验。


##### jwt令牌

概述：`JSON` Web Token。定义了一种简洁的、自包含的格式，用于在通信双方以json数据格式安全的传输信息。由于数字签名的存在，这些信息是可靠的。

组成(中间用.分隔)：

第一部分: Header(头) ，记录令牌类型、签名算法等。例如: {"alg":"HS256","type":"WT"}。实际上看到的是被Base64编码过的

第二部分: Payload(有效载荷) ，携带一些自定义信息、 默认信息等。例如: {"id":"I","username":"Tom"}

第三部分: Signature(签名) ,防止Token被篡改、确保安全性。将header. payload, 并加入指定秘钥,通过指定签名算法计算而来。

场景：登录认证

- ① 登录成功后，生成令牌

- ② 后续每个请求, 都要携带JWT令牌，系统在每次请求处理之前,先校验令牌,通过后,再处理

```
生成jwt令牌步骤：
①先导入依赖
<dependency>
    <groupld>io.jsonwebtoken</groupld>
    <artifactld>jjwt</artifactld>
    <version>0.9.1</version>
</dependency>
②使用提供的API,jwts进行生成
@Test
public void genJwt(){
    Map<String,Object> claims = new HashMap<>();
    claims.put( "id" ,1);
    claims.put(“username"，"Tom" );
    String jwt = Jwts.builder()
        .setClaims(claims) //自定义内容(载荷)
        .signWith(SignatureAlgorithm.HS256, "itheima” ) //设置签名算法和签名密钥
        .setExpiration(new Date(System. currentTimeMillis() + 12*3600*1000)) //有效期12小时
        .compact();
    System.outprintln(jwt);
}

解析jwt令牌
@Test
public void genJwt(){
    String jwt = Jwts.parser()
    	.setSigningKey ( "itheima" )	//指定签名密钥
		.parseClaimsJws("jwt内容")	//解析令牌
		.getBody() ;
    System.outprintln(jwt);
}
注意事项：
	JWT校验时使用的签名秘钥,必须和生成JWT令牌时使用的秘钥是配套的。
	如果JWT令牌解析校验时报错,则说明JWT令牌被篡改或失效了，令牌非法。
```

```
实现
①令牌生成：登录成功后，生成JWT令牌，并返回给前端。
//先引入JWT令牌操作工具类。
package com.itheima.utils;
import io.jsonwebtoken.Claims;
import io.jsonwebtoken.Jwts;
import io.jsonwebtoken.SignatureAlgorithm;
import java.util.Date;
import java.util.Map;

public class JwtUtils {
	private static String signKey = "itheima";
    private static Long expire = 43200000L;
    /**
     * 生成JWT令牌
     * @param claims JWT第二部分负载 payload 中存储的内容
     * @return
     */
    public static String generateJwt(Map<String, Object> claims){
        String jwt = Jwts.builder()
                .addClaims(claims)
                .signWith(SignatureAlgorithm.HS256, signKey)
                .setExpiration(new Date(System.currentTimeMillis() + expire))
                .compact();
        return jwt;
    }
    /**
     * 解析JWT令牌
     * @param jwt JWT令牌
     * @return JWT第二部分负载 payload 中存储的内容
     */
    public static Claims parseJWT(String jwt){
        Claims claims = Jwts.parser()
                .setSigningKey(signKey)
                .parseClaimsJws(jwt)
                .getBody();
        return claims;
    }
}

//修改登录接口
@PostMapping ("/login")
public Result login (@RequestBody Emp emp) {
    log.info("员工登录: {}"， emp);
    Emp e = empService. login (emp);
    //登录成功,生成令牌,下发令牌
    if (e != null) {
        Map<String, object> claims = new HashMap<>() ;
        claims.put("id", e.getId()) ;
        claims.put ("name", e.getName()) ;
        claims.put ("username", e.getUsername ()) ;
        String jwt = JwtUtils.generateJwt (claims); //jwt包含 了当前登录的员工信息
        return Result.success(jwt) ;
	}
    //登录失败，返回错误信息
    return Result.error ("用户名或密码错误") ;
}

②令牌校验：在请求到达服务端后，对令牌进行统一拦截、校验。

```


### 过滤器Filter

概述：Filter表示过滤器，是 JavaWeb 三大组件(`Servlet`、`Filter`、Listener)之一。

功能：

- ① 过滤器可以把对资源的请求拦截下来，从而实现一些特殊的功能。

- ② 过滤器一般完成一些通用的操作。如登录验证，权限控制，统一编码处理、敏感字符处理等


#### 快速入门

```
①定义Filter
//定义一个类,实现Filter接口,并重写其所有方法。
public class DemoFilter implements Filter {
    public void init (FilterConfig filterConfig) throws ServletException {	//初始化方法，Web服务器启动，创建Filter时调用，只调用一次
    	//一般时资源和环境的一些准备操作
    	Filter.super.init(filterConfig) ;
    }
    public void doFilter (ServletRequest request, ServletResponse response, FilterChain chain){		//拦截到请求时，调用该方法，可调用多次
        System.out.println("拦截方法执行，拦截到了请求...");
        chain.doFilter(request, response);	//放行，也就是让其访问本该访问的资源。
    }
    public void destroy() {		//销毁方法，服务器关闭时调用，只调用一次
		//一般做资源的释放和环境的清理工作
    	Filter.super.destroy() ;
    }
}
②配置Filter
//Filter类上加@WebFilter注解，配置拦截资源的路径。该注解的urlPatterns属性值/*表示拦截所有的资源。
//引启动类上加@ServletComponentScan表示开启Servlet组件支持。


```


#### 详解

##### 执行流程

先执行放行前逻辑，然后放行，然后访问资源，然后执行放行后逻辑。


##### 拦截路径

拦截路径有如下四种配置方式：
- ① 拦截具体的资源：urlPatterns值为：/index.jsp，表示只有访问index.jsp时才会被拦截
拦截具体的路径：urlPatterns值为：/login，表示只有访问/login路径时才会被拦截
- ② 目录拦截：urlPatterns值为：/user/*，表示访问/user下的所有资源，都会被拦截
- ③ 后缀名拦截：urlPatterns值为：*.jsp，表示访问后缀名为jsp的资源，都会被拦截
- ④ 拦截所有：urlPatterns值为：/*，表示访问所有资源，都会被拦截


##### 过滤器链

概述：过滤器链是指在一个Web应用，可以配置多个过滤器，这多个过滤器称为过滤器链。

执行逻辑：如下图

`<img src="D:\Users\作业\知识点\java\图片\过滤器执行逻辑.png" style="zoom:50%;" />`

顺序：注解配置的Filter，它的优先级是按照过滤器类名(字符串)的自然排序。


#### 登录校验案例

```
登录校验Filter步骤
    ●获取请求url。
    ●判断请求url中 是否包含login,如果包含，说明是登录操作,放行。
    ●获取请求头中的令牌(token) 。
    ●判断令牌是否存在， 如果不存在,返回错误结果(未登录)。
    ●解析token, 如果解析失败,返回错误结果(未登录)。
    ●放行。
实现
	//在filter层创建过滤类然后完成以上步骤的代码
    @Override
    public void doFilter(ServletRequest request, ServletResponse response, FilterChain chain) throws IOException, ServletException {
        HttpServletRequest req = (HttpServletRequest) request;
        HttpServletResponse resp = (HttpServletResponse) response;

        //1.获取请求url。
        String url = req.getRequestURL().toString();
        log.info("请求的url: {}",url);

        //2.判断请求url中是否包含login，如果包含，说明是登录操作，放行。
        if(url.contains("login")){
            log.info("登录操作, 放行...");
            chain.doFilter(request,response);
            return;	//放行之后不希望执行下面的代码了，因此直接return
        }

        //3.获取请求头中的令牌（token）。
        String jwt = req.getHeader("token");

        //4.判断令牌是否存在，如果不存在，返回错误结果（未登录）。
        if(!StringUtils.hasLength(jwt)){
            log.info("请求头token为空,返回未登录的信息");
            Result error = Result.error("NOT_LOGIN");
            //手动转换 对象--json --------> 阿里巴巴fastJSON
            String notLogin = JSONObject.toJSONString(error);
            resp.getWriter().write(notLogin);
            return;	// 当未登录后，下面的代码就不用执行了，因此return
        }

        //5.解析token，如果解析失败，返回错误结果（未登录）。
        try {
            JwtUtils.parseJWT(jwt);
        } catch (Exception e) {//jwt解析失败
            e.printStackTrace();
            log.info("解析令牌失败, 返回未登录错误信息");
            Result error = Result.error("NOT_LOGIN");
            //手动转换 对象--json --------> 阿里巴巴fastJSON
            String notLogin = JSONObject.toJSONString(error);
            resp.getWriter().write(notLogin);
            return;	//当token解析失败，下面的代码也不希望执行，故return
        }

        //6.放行。
        log.info("令牌合法, 放行");
        chain.doFilter(request, response);
    }
注意：之前在表现层有RestController注解，因此方法的返回值会自动转为json格式，但这里需要自己手动转为json对象，因此使用fastjson。
```


### 拦截器Interceptor

概述：是一种动态拦截方法调用的机制，类似于过滤器。Spring框架 中提供的，用来动态拦截控制器方法的执行。

作用：拦截请求,在指定的方法调用前后,根据业务需要执行预先设定的代码。

#### 快速入门

```
①定义拦截器，实现HandlerInterceptor接口，并重写其所有方法
	public boolean preHandle(HttpServletRequest req, HttpServletResponse resp, Object handler) throws Exception {	//目标资源方法运行前运行, 返回true: 放行, 放回false, 不放行
		System.out.println("preHandle ...");
		return true;
	}
    @Override
    public void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler, ModelAndView modelAndView) throws Exception {	//目标资源方法运行后运行
        System.out.println("postHandle ...");
    }

    @Override
    public void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler, Exception ex) throws Exception {		//视图渲染完毕后运行, 最后运行
        System.out.println("afterCompletion...");
    }
	注意要交给IOC容器管理，添加注解@Component

②注册拦截器,在config层创建配置类，并重写方法，以及注册拦截器的代码
@Configuration //配置类
public class WebConfig implements WebMvcConfigurer {

	//注入拦截类
    @Autowired
    private LoginCheckInterceptor loginCheckInterceptor;

    @Override
    public void addInterceptors(InterceptorRegistry registry) {
    	// 同过滤器不同，拦截路径/**，表示拦截所有，并且不拦截登录请求
        registry.addInterceptor(loginCheckInterceptor).addPathPatterns("/**").excludePathPatterns("/login");
    }
}
```


#### 详解

##### 拦截路径

```
/*：一级路径，例如能匹配/depts, /emps, /login, 不能匹配/depts/1
/**：任意级路径，例如能匹配/depts，/depts/1, /depts/1/2
/depts/*：/depts下的一级路径。例如能匹配/depts/1,不能匹配/depts/1/2, /depts
/depts/**：/depts下的任意级路径，例如能匹配/depts，/depts/1, /depts/1/2, 不能匹配/emps/1

addPathPatterns("拦截路径")：该方法表示拦截哪些资源
excludePathPatterns("拦截路径")：该方法表示不拦截哪些资源

```


##### 执行流程

![](D:\Users\作业\知识点\java\图片\拦截器执行逻辑.png)


#### 登录校验案例

```
登录校验Filter步骤
    ●获取请求url。
    ●判断请求url中 是否包含login,如果包含，说明是登录操作,放行。
    ●获取请求头中的令牌(token) 。
    ●判断令牌是否存在， 如果不存在,返回错误结果(未登录)。
    ●解析token, 如果解析失败,返回错误结果(未登录)。
    ●放行。
实现
@Slf4j
@Component
public class LoginCheckInterceptor implements HandlerInterceptor {
    @Override //目标资源方法运行前运行, 返回true: 放行, 放回false, 不放行
    public boolean preHandle(HttpServletRequest req, HttpServletResponse resp, Object handler) throws Exception {
        //1.获取请求url。
        String url = req.getRequestURL().toString();
        log.info("请求的url: {}",url);

        //2.判断请求url中是否包含login，如果包含，说明是登录操作，放行。
        if(url.contains("login")){
            log.info("登录操作, 放行...");
            return true;
        }

        //3.获取请求头中的令牌（token）。
        String jwt = req.getHeader("token");

        //4.判断令牌是否存在，如果不存在，返回错误结果（未登录）。
        if(!StringUtils.hasLength(jwt)){
            log.info("请求头token为空,返回未登录的信息");
            Result error = Result.error("NOT_LOGIN");
            //手动转换 对象--json --------> 阿里巴巴fastJSON
            String notLogin = JSONObject.toJSONString(error);
            resp.getWriter().write(notLogin);
            return false;
        }

        //5.解析token，如果解析失败，返回错误结果（未登录）。
        try {
            JwtUtils.parseJWT(jwt);
        } catch (Exception e) {//jwt解析失败
            e.printStackTrace();
            log.info("解析令牌失败, 返回未登录错误信息");
            Result error = Result.error("NOT_LOGIN");
            //手动转换 对象--json --------> 阿里巴巴fastJSON
            String notLogin = JSONObject.toJSONString(error);
            resp.getWriter().write(notLogin);
            return false;
        }

        //6.放行。
        log.info("令牌合法, 放行");
        return true;
    }

    @Override //目标资源方法运行后运行
    public void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler, ModelAndView modelAndView) throws Exception {
        System.out.println("postHandle ...");
    }

    @Override //视图渲染完毕后运行, 最后运行
    public void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler, Exception ex) throws Exception {
        System.out.println("afterCompletion...");
    }
}
```


### 过滤器和拦截器

接口规范不同：过滤器需要实现Filter接口,而拦截器需要实现HandlerInterceptor接口。

拦截范围不同：过滤器Filter会拦截所有的资源，而Interceptor只会拦截Spring环境中的资源。


## 异常处理

前景：程序在开发中会不可避免的出现异常，但所响应的数据并不是统一的json返回格式，而是由spring框架提供的返回格式，因此需要做统一异常处理。


### 全局异常处理器

前景：Mapper层出现问题不用处理，抛给Service，Service出问题不用处理，抛给Controller，Controller出现问题不用处理，交给全局异常处理器。

```
在exception层定义全局异常处理器
/**
 * 全局异常处理器
 * 这里Result通过@RestControllerAdvice转化为了Json对象
 */
@RestControllerAdvice
public class GlobalExceptionHandler {

    @ExceptionHandler(Exception.class)//捕获所有异常
    public Result ex(Exception ex){
        ex.printStackTrace();
        return Result.error("对不起,操作失败,请联系管理员");
    }
}
注意：@RestControllerAdvice = @ControllerAdvice + @ResponseBody
```


# `HTTP`

概述：Hyper Text Transfer Protocol,超文本传输协议,规定了浏览器和服务器之间数据传输的规则。

特点：

- ① 基于TCP协议(3次握手，4次挥手)，面向连接，安全

- ② 基于请求-响应模型的：一次请求对应一次响应

- ③ HTTP协议是无状态的协议:：对于事务处理没有记忆能力。每次请求一响应都是独立的。

缺点：多次请求间不能共享数据。

优点：速度快。


## 请求数据格式

请求行：请求数据第一行(请求方式、资源路径、协议)

请求头：第二行开始，格式key: value

请求体：`POST`请求特有的，存放请求参数

```
常见的请求头
    Host：请求的主机名
    User-Agent：浏览器版本，例如Chrome浏览器的标识类似Mozilla/5.0.. Chrome/79, IE浏览器的标识类似Mozilla/5.0 (Windows NT ...) like Gecko
    Accept：表示浏览器能接收的资源类型，如text/*, image/*或者* /*表示所有;
    Accept-Language：表示浏览器偏好的语言，服务器可以据此返回不同语言的网页;
    Accept- Encoding：表示浏览器可以支持的压缩类型，例如gzip, deflate等。
    Content-Type：请求主体的数据类型。
    Content-Length：请求主体的大小(单位:字节)。

请求方式-GET:请求参数在请求行中，没有请求体，如: /brand/ findA11?name=OPPO&status= =1。GET请求大小是有限制的。
请求方式-POST:请求参数在请求体中，POST请求大小是没有限制的。

请求体的内容在控制台对应请求的Payload里面

```


## 响应数据格式

响应行：响应数据第一行(协议、状态码、描述)

响应头：第二行开始，格式key: value

响应体：也就是响应正文，最后一部分，存放响应数据

```
状态码分类
    1xx：响应中-临时状态码，表示请求已经接收，告诉客户端应该继续请求或者如果它已经完成则忽略它。
    2xx：成功-表示请求已经被成功接收，处理已完成。
    3xx：重定向-重定向到其他地方;让客户端再发起一次请求以完成整个处理。
    4xx：客户端错误处理发生错误，责任在客户端。如:请求了不存在的资源、客户端未被授权、禁止访问等。
    5xx：服务器错误-处理发生错误，责任在服务端。如:程序抛出异常等。

常见的响应头
Content-Type：表示该响应内容的类型，例如text/html, application/json。
Content-Length：表示该响应内容的长度(字节数)。
Content-Encoding：表示该响应压缩算法，例如gzip。
Cache-Control：指示客户端应如何缓存，例如max-age=300表示可以最多缓存300秒。
Set-Cookie：告诉浏览器为当前页面所在的域设置cookie。
```


### 常见的响应状态码

| 状态码 | 英文描述 | 解释 |
| ------- | -------------------------------------- | ------------------------------------------------------------ |
| 200 | **`OK`** | 客户端请求成功，即**处理成功**，这是我们最想看到的状态码 |
| 302 | **`Found`** | 指示所请求的资源已移动到由`Location`响应头给定的 URL，浏览器会自动重新访问到这个页面 |
| 304 | **`Not Modified`** | 告诉客户端，你请求的资源至上次取得后，服务端并未更改，你直接用你本地缓存吧。隐式重定向 |
| 400 | **`Bad Request`** | 客户端请求有**语法错误**，不能被服务器所理解 |
| 403 | **`Forbidden`** | 服务器收到请求，但是**拒绝提供服务**，比如：没有权限访问相关资源 |
| ==404== | **`Not Found`** | **请求资源不存在**，一般是URL输入有误，或者网站资源被删除了 |
| 405 | **`Method Not Allowed`** | 请求方式有误，比如应该用GET请求方式的资源，用了POST |
| 428 | **`Precondition Required`** | **服务器要求有条件的请求**，告诉客户端要想访问该资源，必须携带特定的请求头 |
| 429 | **`Too Many Requests`** | 指示用户在给定时间内发送了**太多请求**（“限速”），配合 Retry-After(多长时间后可以请求)响应头一起使用 |
| 431 | **` Request Header Fields Too Large`** | **请求头太大**，服务器不愿意处理请求，因为它的头部字段太大。请求可以在减少请求头域的大小后重新提交。 |
| ==500== | **`Internal Server Error`** | **服务器发生不可预期的错误**。服务器出异常了，赶紧看日志去吧 |
| 503 | **`Service Unavailable`** | **服务器尚未准备好处理请求**，服务器刚刚启动，还未初始化好 |


## 协议解析

### `Tomcat`

前景：Web服务器是一个软件程序，对HTTP协议的操作进行封装,使得程序员不必直接对协议进行操作，让Web开发更加便捷。主要功能是"提供网上信息浏览服务"。

概述：Tomcat是Apache软件基金会一个核心项目, 是一个开源免费的轻量级Web服务器，支持Servlet/JSP少 量avaEE规范。

其他：Tomcat也被称为Web容器、Servlet容 器。Servlet程序需要依赖于Tomcat才能运行。

本质：tomcat是一个Web服务器，本质上就是对Http协议进行了封装，方便我们处理和解析http。我们可以把写好java程序部署在tomcat服务器上，然后就可以直接打开浏览器进行访问应用程序。并且别人也可也通过电脑ip进行访问。

tomcat目录结构：

- ① bin：可执行文件

- ② conf：配置文件

- ③ lib：Tomcat依赖的jar包

- ④ logs：日志文件

- ⑤ temp：临时文件

- ⑥ webapps：应用发布目录

- ⑦ work：工作目录

启动：双击bin\startup.bat

控制台输出中文乱码：修改conf/logging.properties的编码为GBK

注意：JAVA_HOME的变量一定要正确设置，并且注意端口默认为8080

修改端口：修改conf/server.xml的port即可

部署：就是把war包或者文件夹直接复制到目录workapps下就好，他会自动解压，这时在浏览器访问localhost:8080/hello/a.html


创建Maven Web项目(不常用)

```
创建方式有两种:使用骨架和不使用骨架
**使用骨架**
> 具体的步骤包含:
> 1.创建Maven项目
> 2.选择使用Web项目骨架
> 3.输入Maven项目坐标创建项目
> 4.确认Maven相关的配置信息后，完成项目创建
> 5.删除pom.xml中多余内容
> 6.补齐Maven Web项目缺失的目录结构

**不使用骨架**
>具体的步骤包含:
>1.创建Maven项目
>2.选择不使用Web项目骨架
>3.输入Maven项目坐标创建项目
>4.在pom.xml设置打包方式为war
>5.补齐Maven Web项目缺失webapp的目录结构
>6.补齐Maven Web项目缺失WEB-INF/web.xml的目录结构
使用Maven中的Tomcat插件来部署项目
在pom.xml中添加Tomcat插件
	   <build>
	       <plugins>
	       	<!--Tomcat插件 -->
	           <plugin>
	               <groupId>org.apache.tomcat.maven</groupId>
	               <artifactId>tomcat7-maven-plugin</artifactId>
	               <version>2.2</version>
	           </plugin>
	       </plugins>
	   </build>
```


# JDBC

概述：JDBC: ( Java DataBase Connectivity ),就是使用Java语言操作关系型数据库的一套规范(API)。

其实就是sun公司定义了一套接口，每一个关系型数据库都有对应的类来实现，也就是提供驱动jar包。

```
步骤：
①创建工程，导入驱动jar包
②注册驱动
	Class.forName("")
③获取连接
	DriverManager.getConnection()返回的是对象，需要有Connection   conn来接收
④定义SQL语句
⑤获取执行SQL的对象，然后执行SQl并返回结果
	Statement  stmt  = conn.createStatement()
⑥处理结果
⑦释放资源

```


## API详解

### `DriverManager`

作用：注册驱动，获取数据库连接

获取连接url语法：jdbc:mysql://ip地址(域名):端口号/数据库名称?参数值对1&参数键值对2....

细节：

- ① 如果连接是本机的mysql，并且端口为3306，则url可以写"jdbc:mysql:///db1"

- ② 配置useSSl=false参数，禁用安全连接方式，解决警告提示


### `Connection`

作用：获取执行SQL的对象

```
(1)普通执行SQL对象		Statement  createStatement()
(2)预编译SQL的执行对象：防止SQL注入		PreparedStatement  prepareStatement(sql)
(3)执行存储过程的对象	CallableStatement  prepareCall(sql)
```

事务管理：用Connection接口定义3给个方法对进行事务管理

```
(1)setAutoCommit(boolean autoCommit)：true为自动提交事务，false为手动提交事务，即开启事务
(2)commit()		#在执行SQL前开启事务，执行结束后提交事务
(3)rollback()回滚事务	#在java中可以用try  catch来捕获异常，如果有异常则在catch中回滚
```


### `Statement`

作用：执行SQL语句

```
int  executeUpdate(sql)：执行DML，DDL语句，返回值：DML语句受影响的行数，或者DDL语句执行后，成功也可能是0
ResultSet  executeQuery(sql)：执行DQL语句，返回值：ResultSet结果集对象
```


### `ResultSet`

作用：封装了DQL查询语句的结果

```
ResultSet  stmt.executeQuery(sql)：执行DQL语句，返回这个对象
获取查询结果
Boolean  next()：(1)将光标从当前位置向下移动一行 (2)判断当前行是否为有效行
	返回值：true，有效行，当前行有数据，false则反之
xxx   getXxx(参数)：获取数据
xxx：数据类型;如ing getint(参数);String  getString(参数);    参数：int，列的编号，从1开始;	String：列的名称
```

```
使用步骤：
①游标向下移动一行，并判断该行是否有数据：next()
②获取数据：getXxx(参数)
while(rs.next()){	//循环判断游标是否是最后一行末尾
	//获取数据
	rs.getXxx(参数);
}
```


### `PreparedStatement`

概念：一个接口，继承自Statement。

作用：预编译SQL语句并执行，预防SQL注入问题

SQL注入：SQL注入是通过操作来修改事先定义好的SQL语句，用以达到执行代码对服务器进行攻击的方法

```
使用步骤：
(1)获取PreparedStatement对象
	//SQL语句中的参数值，使用？占位符替代
	String sql = "select  *  from  user  where  usename=?  and  password=?";
	//通过Connection对象获取，并传入对应的sql语句
	PreparedStatement  pstmt = conn.PreparedStatement(sql);
(2)设置参数值
	//上面的sql语句中参数使用 ? 进行占位，在之前之前肯定要设置这些 ? 的值。
    PreparedStatement对象：setXxx(参数1，参数2)：给 ? 赋值
    Xxx：数据类型 ； 如 setInt (参数1，参数2)
    参数1： ？的位置编号，从1 开始
    参数2： ？的值
(3)执行SQL语句
    executeUpdate(); 执行DDL语句和DML语句
    executeQuery(); 执行DQL语句
注意：调用这两个方法时不需要传递SQL语句，因为获取SQL语句执行对象时已经对SQL语句进行预编译了。
	useServerPrepStmts=true	//开启预编译功能
//在代码中编写url时需要加上以下参数。而我们之前根本就没有开启预编译功能，只是解决了SQL注入漏洞。
配置MySQL执行日志（重启mysql服务后生效）
在mysql配置文件（my.ini）中添加如下配置
    log-output=FILE
    general-log=1
    general_log_file="D:\mysql.log"
    slow-query-log=1
    slow_query_log_file="D:\mysql_slow.log"
    long_query_time=2
```


# Mybatis

概述：`MyBatis` 是一款优秀的持久层框架，用于简化 JDBC 开发。

持久层：在dao层负责数据库和数据之间的处理。

开发中会将操作数据库的Java代码作为持久层。而Mybatis就是对jdbc代码进行了封装。

mybatis可以通过注解开发(简单)和xml映射文件(复杂)开发

Mapper代理：也就是mapper层接口，解决原生方式中的硬编码，简化后期执行SQL。


## 快速入门

```
1.准备工作(创建springboot工程、数据库表user、实体类User)
2.引入Mybatis的相关依赖， 配置Mybatis(数据库的连接信息)
	创建项目时选中或导入MyBatis Framework和MySQl Driver依赖。
	将以下配置信息加入boot项目的配置文件
        spring.datasource.driver-class-name=com.mysql.cj.jdbc.Driver
        spring.datasource.url=jdbc:mysql://localhost:3306/库名
        spring.datasource.username=root
        spring.datasource.password=1234
3.在持久层定义BrandMapper接口，并加上@Mapper注解，同时编写SQL语句(注解编写/XML编写)
    @Mapper
    public interface UserMapper {
    	@Select("select * from user")	//会将sql语句的执行结果封装到方法的返回值当中
    	public List<User> list();
    }
4.在test里面定义测试类进行测试
    @SpringBootTest
    class SpringbootMybatisQuickstart1ApplicationTests {
        @Autowired	//在测试类中通过依赖注入的形式使用mapper接口的对象。
        private UserMapper userMapper;
        @Test
        public void test1(){
            List<User> userList = userMapper.list();
            userList.stream().forEach(user -> {
            System. out.println(user);
            });
        }
     }
注意：Mybatis的开发中，只需要定义mapper接口就行，框架底层会自动生成对应接口的实现类

配置Sql提示：默认在mybatis中编写sql语句是不提示的，需要在idea中进行设置提示
```


## 数据库连接池

概念：数据库连接池是个容器，负责分配、管理数据库连接(`Connection`)，它允许应用程序重复使用一个现有的数据库连接，而不是再重新建立一个。

注意：释放空闲时间超过最大空闲时间的数据库连接，来避免因为没有释放数据库连接而引起的数据库连接遗漏。

好处：资源重用，提升系统响应速度，避免数据库连接遗漏。


### 连接池的实现

标准接口：`DataSource`，官方(SUN) 提供的数据库连接池标准接口，由第三方组织实现此接口。

功能：获取连接。对应的方法为Connection getConnection() throws SQLException;

常见的数据库连接池

DBCP，C3P0，Druid(推荐)，Hikari(boot默认)

提示：输出System.getProperty("user.dir");输出当前的路径

```
boot切换druid步骤：
	①直接在pom.xml导入相关依赖即可
        <dependency>
            <groupld>com.alibaba</groupld>
            <artifactld>druid-spring-boot-starter</artifactld>
            <version>1.2.8</version>
    	</dependency>
   	②修改配置信息（可以不用换）
        spring.datasource.druid.driver-class-name=com.mysql.cj.jdbc.Driver
        spring.datasource.druid.url=jdbc:mysql://localhost:3306/库名
        spring.datasource.druid.username=root
        spring.datasource.druid.password=1234


Druid使用步骤：
    ①导入jar包 druid-1.1.12.jar
    ②定义配置文件
    ③加载配置文件
        Properties prop = new Properties();
        prop.load(new FileInputStream("jdbc-demo/src/druid.properties"));
    ④获取数据库连接池对象
        DataSource dataSource = DruidDataSourceFactory.createDataSource(prop);
    ⑤获取连接
        Connection connection = dataSource.getConnection();
        System.out.println(connection);
        //修改数据库的编码格式alter database db1 character set utf8;
        show variables like 'char%';
```


## `Lombok`

概述：Lombok是一个实用的Java类库，能通过注解的形式自动生成构造器、getter/setter. equals、 hashcode、 toString等方法。

作用：可以自动化生成日志变量，简化java开发、提高效率。

注解：

@Getter/@Setter：为所有的属性提供get/set方法

@ToString：会给类自动生成易阅读的toString方法

@EqualsAndHashCode：根据类所拥有的非静态字段自动重写equals方法和hashCode方法

@Data：提供了更综合的生成代码功能(@Getter + @Setter + @ToString + @EqualsAndHashCode)

@NoArgsConstructor：为实体类生成无参的构造器方法

@AllArgsConstructor：为实体类生成除了static修饰的字段之外带有各参数的构造器方法。

使用：先导入Lombok的依赖，然后在实体类上方添加注解即可

```
<dependency>	//boot已经集成了lombok，不需要添加版本号
    <groupId>org.projectlombok</groupId>
    <artifactId>lombok</artifactId>
</dependency>
注意：Lombok会在编译时，自动生成对应的java代码。 我们使用lombok时,还需要安装一个名为lombok的插件(idea自带)。
```


## 注解开发的基础操作

### 细节

```
数据库中的date类型，对应java中的LocalDate类型
数据库的datetime类型，对应java中的LocalDtaeTime类型
数据库中的字段名是下划线连接，java中的实体类属性是小驼峰命名法
```


### 根据主键删除

```
// 根据ID删除数据
@Delete("delete from emp where id = #{id}")	//使用 #{}占位符将参数传进去
public void delete (Integer id);	//这个方法里面传的参数就是要删除的主键id
注意：
	●如果方法有返回值，返回值代表此次操作影响了多条数据。一般不需要这个返回值
	●如果mapper接口方法形参只有一个普通类型的参数, #{..}里面的属性名可以随便写，如: #{id}、 #{value}。
	●使用 #{}占位符会自动开启预编译防止sql注入
	●方法的参数传入的是需求的参数，如果有多个参数，一般使用实体类封装起来，然后传入实体类对象
```

- ③ 测试


### 日志输出

想要看到mybatis底层到底执行了什么，可以在`application.properties`中，打开mybatis的日志，并指定输出到控制台。

```
#指定mybatis输出日志的位置输出控制台
mybatis.configuration.log-impl=org.apache.ibatis.logging.stdout.Std0utlmpl

此时可以在控制台看到如下：
===> Preparing: delete from emp where id=?
==>  Parameters : 16 (Integer)
这就是mybatis底层所作的处理，？是占位符，这两行也被称为预编译SQL

预编译SQL的优势
    ●减少相同语句重复编译，性能更高
    ●更安全(防止SQL注入)
正常的流程：java先连接数据库，然后将sql发给数据库，如果没用预编译，数据库要先对sql进行语法检查，优化sql以及编译sql的步骤，最后才执行sql。

SQL注入
SQL注入是通过操作输入的数据来修改事先定义好的SQL语句，以达到执行代码对服务器进行攻击的方法。
分析：之所以能够注入成功就是因为直接将参数拼接到了sql语句当中

参数占位符
#{..}
    ●使用时机:参数传递,都使用#{..}
    ●执行SQL时，会将#..替换为?,生成预编译SQL,会自动设置参数值。

${..}
    ●拼接SQL。直接将参数拼接在SQL语句中，存在SQL注入问题。
    ●使用时机:如果对表名、列表进行动态设置时使用。
```


### 新增

步骤：

- ① 先尝试写出sql语句，注意新增时比如id，password等字段是不需要添加的。

- ② 然后在mapper层进行代码编写

```
//新增员工
@Insert(
	"insert into emp (username, name, gender, image, job, entrydate, dept_id, create_time, update_time)" + "values (#{username},#{name},#{gender},#{image},#{job},#{entrydate},#{deptId},#{createTime},#{updateTime})")
public void insert (Emp emp);		//添加的字段有很多，因此直接传入实体类对象，然后传参时注意传的是实体类的属性而不是下划线的字段名。
```

- ③ 测试


主键返回

描述：在数据添加成功后，需要获取插入数据库数据的主键。

如：添加套餐数据时，还需要维护套餐菜品关系表数据。

直接通过getId方法拿会返回null

实现：在mapper层的方法上方添加`@Options`(keyProperty = "id", useGeneratedKeys = true)

结果：此时再通过getID方法就可以拿到了


### 更新

分析：点击更新时需要先进行查询，将待更新的数据先全部复现出来，然后根据主键修改信息。

```
//Mapper层方法编写
@Update (
	"update emp set username = #{username}, name = #{name}, gender = #{gender}， image = #{image}," +
	"job = #{job}， entrydate = #{entrydate}, dept_id = #{deptId}, update_time = #{updateTime} where id = #{id}")
public void update(Emp emp);

//测试编写
//更新员工
@Test
public void testUpdate() {
    //构造员工对象
    Emp emp = new Emp() ;
    emp.setId(18) ;
    emp.setUsername ("Tom1") ;
    emp.setName ("汤姆1") ;
    emp.setImage ("1.jpg") ;
    emp.setGender ( (short)1) ;
    emp.setJob( (short)1) ;
    emp.setEntrydate (LocalDate.of( year: 2000, month: 1, dayOfMonth: 1)) ;
    emp.setUpdateTime (LocalDateTime . now()) ;
    emp.setDeptId(1) ;
    //执行更新员工操作
    empMapper.update(emp) ;
}

注意：这里有问题，没有采用动态sql，导致如果部分字段不填写会置为null。解决办法看xml映射文件开发中的动态sql

```


### 查询

#### 根据ID查询

```
@Select ("select * from emp where id = #{id}")
public Emp getById(Integer id) ;	//只查询一条数据不需要List集合进行封装，直接返回Emp一条即可
注意：此时查询出来的下划线和驼峰命名不一样的字段为null

数据封装
    实体类属性名和数据库表查询返回的字段名-致，mybatis会自动封装。
    如果实体类属性名和数据库表查询返回的字段名不一致,不能自动封装。
解决方法
	①给字段起别名，让别名与实体类属性一致
	实现：修改sql语句，将需要不一致的进行起别名
	@Select ("select id, username, password,name, gender, image, job, entrydate," +
	"dept_id deptId, create_time createTime, update_time updateTime from emp where id = #{id}")

	②通过@Results, @Result注解手动映射封装
	实现：在这个mapper层的方法上添加@Results注解。其中的column代表字段名，property代表属性名
	@Results({
        @Result (column = "dept_id", property = "deptId") ,
        @Result (column = "create_time", property = "createTime") ,
        @Result (column = "update_time", property = "updateTime")
    })

    ③开启mybatis的驼峰命名自动映射开关(推荐)
	实现：在配置文件添加信息开启，然后按照原来的代码写即可
	mybatis.configuration.map-underscore-to-camel-case=true
	也就是从数据库的字段映射到java中的属性。a_column --> aCloumn
```


#### 条件查询

```
先拟定一个条件进行编写sql语句，写出大致sql语句，随后使用占位符代替

//条件查询员工
@Select ("select * from emp where name like concat('%',#{name},'%') and gender = #{gender} and" +
	     "entrydate between #{begin} and #{end} order by update_time desc ")
public List<Emp> list (String name, Short gender, LocalDate begin，LocalDate end) ;

注意：
	返回的数据是多条，用List集合去接收。
	方法参数没有传入实体类对象是因为有些属性对象并没有，因此选择指定参数传入。
	模糊查询是在引号内，其中两个%表示这是模糊查询，但#{}是不能出现在引号之内的，因为它生成的是预编译的sql，他最终是要被？替代的，而？占位符是不能出现在引号之内的。所以可以使用${}直接拼接。但${}有sql注入等风险，因此最终采用concat()函数进行拼接。
```


## XML映射文件开发

规范：

XML映射文件的名称与Mapper接口名称一致,并且将XML映射文件和Mapper接口放置在相同包下(同包同名)。也就是xml文件在配置文件目录(resources)下要创建同包同名的xml文件

XML映射文件的namespace属性为Mapper接口全限定名(copy reference)一致。

XML映射文件中sql语句的id与Mapper接口中的方法名一致, 并保持返回类型一致(设置resultType属性)。

一般一个mapper接口对应resources下的一个xml映射文件

注意在resources配置文件目录下创建包结构时，每一个包之间要使用/来分隔，因为他是目录。

```
如下所示：
Mapper接口中的
@Mapper
public interfacelEmpMapper {
	public List<Emp> list (String name, Short gender , LocalDate begin , LocalDate end);
}

XML映射文件中的
<mapper namespace="com.itheima.mapper.EmpMapper">
    <select id="list" resultType="com.itheima.pojo.Emp">	//这里的resultType表示单挑记录所封装的类型
        select * from emp where name like concat('%',#{name},'%') and gender = #{gender}
        	and entrydate between #{begin} and #{end} order by update_time desc
    </select>
</mapper>

```


### 动态SQL

概述：随着用户的输入或外部条件的变化而变化的SQL语句，我们称为动态SQL。

```
如下例子：条件查询时，不会所有的条件都填写。会导致写死的条件都是null，从而查不到数据
select * from emp where name like concat('%',#{name},'%') and gender = #{gender} and entrydate between #{begin} and #{end} order by update_time desc。
通过动态sql标签优化后
select * from emp
where
    <if test="name != null">
        name like concat('%',#{name}, '%')
    </if>
    <if test="gender != null">
    	and gender = #{gender}
    </if>
    <if test="begin != null and end != null">
        and entrydate between #{begin} and #{end}
    </if>
order by update_time desc
但出现了新的问题：如果第一个条件不添加，那么后面会直接查询第二个会出现sql语法错误，会出现where and语法错误
		解决办法：
           1.可以在第一个前面加and，然后在where后面加入恒等式
           2.用<where>替换where	注意：需要给每个条件前都加上 and 关键字。


动态sql标签：
    if:用于判断条件是否成立。使用test属性进行条件判断，如果条件为true,则拼接SQL。
    	test属性：逻辑表达式
    choose:从多个条件选择一个，类似于switch
    where：where元素只会在子元素有内容的情况下才插入where子句。而且会自动去除子句的开头的AND或OR。
    set：动态地在行首插入SET关键字，并会删掉额外的逗号。(用在update语句中)
    trim
	foreach：用来迭代任何可迭代的对象（如数组，集合）。
		collection属性：指的是要遍历的集合。mybatis会将数组参数，封装为一个Map集合。默认：array = 数组。使用@Param注解改变map集合的默认key的名称
        item属性：本次迭代获取到的元素。
        separator属性：集合项迭代之间的分隔符。 foreach标签不会错误地添加多余的分隔符。也就是最后一次迭代不会加
        分隔符。
        open属性：该属性值指的是在拼接SQL语句之前拼接的语句，只会拼接一次
        close属性：该属性值指的是在拼接SQL语句拼接后拼接的语句，只会拼接一次
    sql和include：这两个配合使用，目的是为了增加代码的复用性。sql表示抽取的sql片段，并用id属性记名字，include通过refid属性实现调用

例子2：动态更新，之前更新的时候需要填写全部字段，如果只填写部分字段，其余没填的就变为了空
<update id="update2">
    update emp
    set		//修改为<set>
        <if test="username != null">username = #{username},</if>
        <if test="name != null">name = #{name},</if>
        <if test="gender != null">gender = #{gender},</if>
        <if test="image != null">image = #{image},</if>
        <if test="job != null">job = #{job},</if>
        <if test="entrydate != null">entrydate = #{entrydate},</if>
        <if test="deptId != null">dept_id = #{deptId} ,</if>
        <if test="updateTime != null">update_time = # {updateTime}</if>
    where id = #{id}
</update>
但这个会有新的问题，比如只更新username字段，会导致多一个逗号，从而导致语法错误。此时可以通过将set改为<set>

例子3：批量删除
	//在mapper层方法中添加
	public void deleteByIds(List<Integer> ids);		//需要批量删除的id号封装到List集合中，在xml文件使用foreach标签遍历list集合进行删除

	//xml文件配置，先写出sql语句然后写这个
	<delete id="deleteByIds">
        delete from emp where id in
        <foreach collection="ids" item="id" separator="," open="(" close=")" >
            #{id}
        </foreach>
    </delete>
```


### 条件查询

```
条件查询中参数接收的3种方式
1.散装参数：如果方法中有多个参数,使用 @Param("参数名称") 标记每一个参数，在映射配置文件中就需要使用 #{参数名称} 进行占位
2.对象参数:将多个参数封装成一个实体对象 ，将该实体对象作为接口的方法参数。该方式要求在映射配置文件的SQL中使用 #{内容} 时，里面的内容必须和实体类属性名保持一致。
3.map集合的参数
```


## MyBatisX插件

概述：MybatisX 是一款基于 IDEA 的快速开发插件，为效率而生。

功能：方便操作执行语句

- ① XML映射配置文件 和 接口方法 间相互跳转

- ② 根据接口方法生成 statement

- ③ 红色头绳的表示映射配置文件，蓝色头绳的表示mapper接口。在mapper接口点击红色头绳的小鸟图标会自动跳转

- ④ 到对应的映射配置文件，在映射配置文件中点击蓝色头绳的小鸟图标会自动跳转到对应的mapper接口。

- ⑤ 也可以在mapper接口中定义方法，自动生成映射配置文件中的 statement

蓝色的小鸟代表usermapper接口

使用步骤：

```
一般第一步为编写接口方法：Mapper接口；
	第二部然后看是否需要参数，和返回结果类型是什么，在这里是List<Brand>
	第三步编写SQL语句：SQLxml映射文件
	第四步执行方法，进行测试
```

```
//1. 获取SqlSessionFactory
String resource = "mybatis-config.xml";
InputStream inputStream = Resources.getResourceAsStream(resource);
SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);
//2. 获取SqlSession对象
SqlSession sqlSession = sqlSessionFactory.openSession();
//3. 获取Mapper接口的代理对象
BrandMapper brandMapper = sqlSession.getMapper(BrandMapper.class);
//4. 执行方法
List<Brand> brands = brandMapper.selectAll();
System.out.println(brands);
//5. 释放资源
sqlSession.close();
```


## 分页插件PageHelper

前景：在默认情况下，如果要实现分页查询，需要在mapper层定义两个方法，封装一个新的对象用来返回总记录数和列表数据，并在service层执行数据处理并完成返回封装的对象，在mapper层的两个方法，一个用来查询总记录数，一个用来分页查询，获取列表数据。

```
<!-- PageHelper分页插件- ->
<dependency>
    <groupId>com.github.pagehelper</groupId>
    <artifactId>pagehelper-spring-boot-starter</artifactId>
    <version>1.4.2</version>
</dependency>
```

```
优化前：
//在Mapper层
@Select ("select count(*) from emp")
public Long count() ;
//分页查询,获取列表数据
@Select ("select * from emp limit #{start}, # {pageSize}")
public List<Emp> page (Integer start, Integer pageSize) ;

//在service实现层
@Override
public PageBean page (Integer page, Integer pageSize) {
    //1.获取总记录数
    Long count = empMapper.count () ;
    //2.获取分页查询结果列表
    Integer start = (page - 1) * pageSize;
    List<Emp> empList = empMapper.page (start, pageSize) ;
    //3.封装PageBean对象
    PageBean pageBean = new PageBean (count, empList) ;
    return pageBean;
}

优化后：
//在Mapper层
@Select ("select * from emp")
public List<Emp> list();

//在service实现层
@Override
public PageBean page (Integer page, Integer pageSize) {
    //1. 设置分页参数
    PageHelper.startPage(page, pageSize) ;
    //2.执行查洵
    List<Emp> empList = empMapper.list() ;
    Page<Emp> P = (Page<Emp>) empList;
    //3.封装PageBean对象
    PageBean pageBean = new PageBean (p.getTotal()，p.getResult()) ;
    return pageBean;
}
```


## 注意事项

```
当出现数据库里面的属性为下划线，而idea中是驼峰命名
	这个问题可以通过两种方式进行解决：
	①给字段起别名：
		使用resultMap定义字段和属性的映射关系
	②SQL片段：
		将需要复用的SQL片段抽取到 sql 标签中
		id属性值是唯一标识，引用时也是通过该值进行引用。
		在原sql语句中进行引用
		使用 include 标签引用上述的 SQL 片段，而 refid 指定上述 SQL 片段的id值。
	上面这两个都不灵活
	③所以我们可以使用resultMap解决上述问题
		起别名 + sql片段的方式可以解决上述问题，但是它也存在问题。如果还有功能只需要查询部分字段，而不是查询所有字段，
		那么我们就需要再定义一个 SQL 片段，这就显得不是那么灵活。
		那么我们也可以使用resultMap来定义字段和属性的映射关系的方式解决上述问题。
		在映射配置文件中使用resultMap定义 字段 和 属性 的映射关系
		id：完成主键字段的映射
		column：表的列名
		property：实体类的属性名
		result：完成一般字段的映射
		column：表的列名
		property：实体类的属性名
```


# Servlet知识点

概述：

```
* Servlet是JavaWeb最为核心的内容，它是Java提供的一门==动态==web资源开发技术。
* 使用Servlet就可以实现，根据不同的登录用户在页面上动态显示不同内容。
* Servlet是JavaEE规范之一，其实就是一个接口，将来我们需要定义Servlet类实现Servlet接口，并由web服务器运行Servlet
```

执行流程：

```
* 浏览器发出`http://localhost:8080/web-demo/demo1`请求，从请求中可以解析出三部分内容，分别是`localhost:8080`、`web-demo`、`demo1`
* 根据`localhost:8080`可以找到要访问的Tomcat Web服务器
* 根据`web-demo`可以找到部署在Tomcat服务器上的web-demo项目
* 根据`demo1`可以找到要访问的是项目中的哪个Servlet类，根据@WebServlet后面的值进行匹配
* 找到ServletDemo1这个类后，Tomcat Web服务器就会为ServletDemo1这个类创建一个对象，然后调用对象中的service方法
* ServletDemo1实现了Servlet接口，所以类中必然会重写service方法供Tomcat Web服务器进行调用
* service方法中有ServletRequest和ServletResponse两个参数，ServletRequest封装的是请求数据，ServletResponse封装的是响应数据，后期我们可以通过这两个参数实现前后端的数据交互

Servlet由web服务器创建，Servlet方法由web服务器调用

因为我们自定义的Servlet,必须实现Servlet接口并复写其方法，而Servlet接口中有service方法
```


## 生命周期

概述：对象的生命周期指一个对象从被创建到被销毁的整个过程。

```
  	1.加载和实例化：默认情况下，当Servlet第一次被访问时，由容器创建Servlet对象
	默认情况，Servlet会在第一次访问被容器创建，但是如果创建Servlet比较耗时的话，那么第一个访问的人等待的时间就比较长，用户的体验就比较差，那么我们能不能把Servlet的创建放到服务器启动的时候来创建，具体如何来配置?
	  @WebServlet(urlPatterns = "/demo1",loadOnStartup = 1)
	loadOnstartup的取值有两类情况
	  	1）负整数:第一次访问时创建Servlet对象
	  	2）0或正整数:服务器启动时创建Servlet对象，数字越小优先级越高
```

初始化：在Servlet实例化之后，容器将调用Servlet的init()方法初始化这个对象，完成一些如加载配置文件、创建连接等初始化的工作。该方法只调用一次

请求处理：每次请求Servlet时，Servlet容器都会调用Servlet的service()方法对请求进行处理

服务终止：当需要释放内存或者容器关闭时，容器就会调用Servlet实例的destroy()方法完成资源的释放。在destroy()方法调用之后，容器会释放这个Servlet实例，该实例随后会被Java的垃圾收集器所回收

```
* 初始化方法，在Servlet被创建时执行，只执行一次
void init(ServletConfig config)

* 提供服务方法， 每次Servlet被访问，都会调用该方法
void service(ServletRequest req, ServletResponse res)

* 销毁方法，当Servlet被销毁时，调用该方法。在内存释放或服务器关闭时销毁Servlet
void destroy()

* 获取Servlet信息
String getServletInfo()
//该方法用来返回Servlet的相关信息，没有什么太大的用处，一般我们返回一个空字符串即可
public String getServletInfo() {
	return "";
}

* 获取ServletConfig对象
ServletConfig getServletConfig()
ServletConfig对象，在init方法的参数中有，而Tomcat Web服务器在创建Servlet对象的时候会调用init方法，必定会传入一个ServletConfig对象，我们只需要将服务器传过来的ServletConfig进行返回即可。
操作步骤：
	1. HttpServlet的使用步骤
		继承HttpServlet
		重写doGet和doPost方法

	2. HttpServlet原理
		获取请求方式，并根据不同的请求方式，调用不同的doXxx方法
```


## urlPattern配置

一个Servlet,可以配置多个urlPattern


## Request（请求）&Response（响应）

```
	概念：
		request:获取请求数据
			浏览器会发送HTTP请求到后台服务器[Tomcat]
			HTTP的请求中会包含很多请求数据[请求行+请求头+请求体]
			后台服务器[Tomcat]会对HTTP请求中的数据进行解析并把解析结果存入到一个对象中
			所存入的对象即为request对象，所以我们可以从request对象中获取请求的相关参数
			获取到数据后就可以继续后续的业务，比如获取用户名和密码就可以实现登录操作的相关业务
		response:设置响应数据
			业务处理完后，后台就需要给前端返回业务处理的结果即响应数据
			把响应数据封装到response对象中
			后台服务器[Tomcat]会解析response对象,按照[响应行+响应头+响应体]格式拼接结果
			浏览器最终解析结果，把内容展示在浏览器给用户浏览
	小结：request对象是用来封装请求数据的对象
		response对象是用来封装响应数据的对象
```


### Requst继承体系

```
Request的继承体系为ServletRequest-->HttpServletRequest-->RequestFacade
①Tomcat需要解析请求数据，封装为request对象,并且创建request对象传递到service方法
httpservlet是一个类需要继承使用
②使用request对象，可以查阅JavaEE API文档的HttpServletRequest接口中方法说明
```


### Request获取请求数据

请求数据分为3部分：

```
		1.请求行：
		获取请求方式: GET
		String getMethod()
		动态获取虚拟目录(项目访问路径): /request-demo
		String getContextPath()
		获取URL(统一资源定位符): http://localhost:8080/request-demo/req1
		StringBuffer getRequestURL()
		获取URI(统一资源标识符): /request-demo/req1
		String getRequestURI()
		获取请求参数(GET方式): username=zhangsan&password=123
		String getQueryString()
	2.请求头：
		getHeader(String name)根据请求头名称获取其对应的值
	3.请求体：
		注意: 浏览器发送的POST请求才有请求体
		如果是纯文本数据:getReader()
		如果是字节数据如文件数据:getInputStream()
```


### request通用方式请求参数

```
	方法：
		获取所有参数Map集合
		Map<String,String[]> getParameterMap()
		根据名称获取参数值（数组）
		String[] getParameterValues(String name)
		根据名称获取参数值(单个值)
		String getParameter(String name)

	 	req.getParameter()方法使用的频率会比较高,获取单个参数
```


### 解决post请求乱码问题

```
	分析出现中文乱码的原因：
		POST的请求参数是通过request的getReader()来获取流中的数据
		TOMCAT在获取流的时候采用的编码是ISO-8859-1
		ISO-8859-1编码是不支持中文的，所以会出现乱码
	解决方案：
		页面设置的编码格式为UTF-8
		把TOMCAT在获取流数据之前的编码设置为UTF-8
		通过request.setCharacterEncoding("UTF-8")设置编码,UTF-8也可以写成小写
```


### 解决get请求中文乱码问题

```
	request的请求转发
		实现方法：req.getRequestDispatcher("/req6").forward(req,resp)
		请求转发资源间共享数据:使用Request对象
		①存储数据到request域[范围,数据是存储在request对象]中：
		void setAttribute(String name键,Object o值);
		②根据key获取值：
		Object getAttribute(String name);
		③根据key删除该键值对：
		void removeAttribute(String name);
		请求转发的特点：
		①浏览器地址栏路径不发生变化
		②只能转发到当前服务器的内部资源，不能从一个服务器通过转发访问另一台服务器
		③一次请求，可以在转发资源间使用request共享数据，虽然后台从/req5转发到/req6，但是这个只有一次请求
```


## Response对象

```
响应数据分为响应行，响应头，响应体
①响应行
设置响应状态码:void setStatus(int sc);
②响应头
设置响应头键值对：void setHeader(String name,String value);
③响应体
获取字符输出流:PrintWriter getWriter();
获取字节输出流：ServletOutputStream getOutputStream();
```


### Response完成重定向

重定向：一种资源跳转方式

```
重定向实现的两种方式:
	①resp.setStatus(302);
	resp.setHeader("location（固定的)","资源B的访问路径");
	②resposne.sendRedirect("/request-demo/resp2")
重定向的特点：
	浏览器地址栏路径发送变化
	可以重定向到任何位置的资源(服务内容、外部均可)
	两次请求，不能在多个资源使用request共享数据，因为浏览器发送了两次请求，是两个不同的request对象，就无法通过request对象进行共享数据

路径问题：
	浏览器发起使用:需要加虚拟目录(项目访问路径)
	服务端发起使用:不需要加虚拟目录
```


### Response响应字符数据

```
步骤实现：
	设置流的编码:response.setContentType("text/html;charset=utf-8");
	①通过Response对象获取字符输出流： PrintWriter writer = resp.getWriter();
	②通过字符输出流写数据: writer.write("aaa");
	注意:一次请求响应结束后，response对象就会被销毁掉，所以不要手动关闭流。
```


### Response响应字节数据

```
	实现步骤：
	①通过Response对象获取字节输出流：ServletOutputStream outputStream =resp.getOutputStream();
	②通过字节输出流写数据: outputStream.write(字节数据);
```


# JSP知识点

概述：`JSP`（全称：Java Server Pages）：Java 服务端页面。是一种动态的网页技术，其中既可以定义 HTML、JS、CSS等静态内容，还可以定义 Java代码的动态内容，也就是 `JSP` = HTML + Java。

快速入门：

```
	①导入坐标
	<dependency>
		<groupId>javax.servlet.jsp</groupId>
		<artifactId>jsp-api</artifactId>
		<version>2.2</version>
		<scope>provided</scope>
	</dependency>
	该依赖的 scope 必须设置为 provided ，因为 tomcat 中有这个jar包了，所以在打包时我们是不希望将该依赖打进到我们工程的war包中。
	②建立文件，在webapp下建立jsp文件
	③写代码
```


## jsp原理

```
	他本质上就是一个Servlet
	1. 浏览器第一次访问 hello.jsp 页面
	2. tomcat 会将 hello.jsp 转换为名为 hello_jsp.java 的一个 Servlet
	3. tomcat 再将转换的 servlet 编译成字节码文件 hello_jsp.class
	4. tomcat 会执行该字节码文件，向外提供服务
```


## jsp脚本

```
	JSP 脚本有如下三个分类：
		<%...%>：内容会直接放到_jspService()方法之中
		<%=…%>：内容会放到out.print()中，作为out.print()的参数
		<%!…%>：内容会放到_jspService()方法之外，被类直接包含

	注意：jsp虽然逐渐被淘汰但还是要会，以后开发更多的是使用HTML+Ajax来替代，但是目前可以采用不在jsp里写java代码，使用servlet进行逻辑处理，封装数据，然后用jsp获取数据，遍历展现数据
```


## EL表达式

概述：

```
	EL(全称Expression Language)表达式语言，用于简化 JSP 页面内的 Java 代码。
	EL表达式的主要作用是 获取数据。其实就是从域对象中获取数据，然后将数据展示在页面上。
	而EL表达式的语法也比较简单，${expression} 。
```


### 域对象

```
	JavaWeb中有四大域对象，分别是：
	page：当前页面有效
	request：当前请求有效
	session：当前会话有效
	application：当前应用有效
	el 表达式获取数据，会依次从这4个域中寻找，直到找到为止
```


## JSTL标签

概述：JSP标准标签库(Jsp Standarded Tag Library) ，使用标签取代JSP页面上的Java代码。

```
	常用的标签：
		①if标签：<c:if> ：相当于 if 判断
		属性：test，用于定义条件表达式
		②forEach 标签
		<c:forEach> ：相当于 for 循环。java中有增强for循环和普通for循环，JSTL 中的 <c:forEach> 也有两种用法
	用法一
		类似于 Java 中的增强for循环。涉及到的 <c:forEach> 中的属性如下
		items：被遍历的容器
		var：遍历产生的临时变量
		varStatus：遍历状态对象

	如下代码，是从域对象中获取名为 brands 数据，该数据是一个集合；遍历遍历，并给该集合中的每一个元素起名为brand ，是 Brand对象。在循环里面使用 EL表达式获取每一个Brand对象的属性值
		<c:forEach items="${brands}" var="brand">
		<tr align="center">
		<td>${brand.id}</td>
		<td>${brand.brandName}</td>
		<td>${brand.companyName}</td>
		<td>${brand.description}</td>
		</tr>
		</c:forEach>

	用法二
	类似于 Java 中的普通for循环。涉及到的 <c:forEach> 中的属性如下
	begin：开始数
	end：结束数
	step：步长
	实例：从0循环到10，变量名是 i ，每次自增1
		<c:forEach begin="0" end="10" step="1" var="i">
		${i}
		</c:forEach>
```


jstl的使用步骤：

```
		①导入坐标
			<dependency>
				<groupId>jstl</groupId>
				<artifactId>jstl</artifactId>
				<version>1.2</version>
			</dependency>
			<dependency>
				<groupId>taglibs</groupId>
				<artifactId>standard</artifactId>
				<version>1.1.2</version>
			</dependency>
		②在JSP页面上引入JSTL标签库
		<%@ taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core" %>
```


## MVC模式

```
	MVC 是一种分层开发的模式，其中：
	M：Model，业务模型，处理业务
	V：View，视图，界面展示
	C：Controller，控制器，处理请求，调用模型和视图
	控制器（serlvlet）用来接收浏览器发送过来的请求，控制器调用模型（JavaBean）来获取数据，比如从数据库查询数据；控制器获取到数据后再交由视图（JSP）进行数据展示。
	JavaBean 是一种JAVA语言写成的可重用组件。为写成JavaBean，类必须是具体的和公共的，并且具有无参数的构造器。JavaBean 通过提供符合一致性设计模式的公共方法将内部域暴露成员属性，set和get方法获取。众所周知，属性名称符合这种模式，其他Java 类可以通过自省机制(反射机制)发现和操作这些JavaBean 的属性。
```


## 三层架构

```
	数据访问层：对数据库的CRUD基本操作
	业务逻辑层：对业务逻辑进行封装，组合数据访问层层中基本功能，形成复杂的业务逻辑功能。例如 注册业务功能 ，我
	们会先调用 数据访问层 的 selectByName() 方法判断该用户名是否存在，如果不存在再调用 数据访问层 的 insert()
	方法进行数据的添加操作
	表现层：接收请求，封装数据，调用业务逻辑层，响应数据
	而整个流程是，浏览器发送请求，表现层的Servlet接收请求并调用业务逻辑层的方法进行业务逻辑处理，而业务逻辑层方法调用数据访问层方法进行数据的操作，依次返回到serlvet，然后servlet将数据交由 JSP 进行展示。
	每一层都有特有的包名：
	表现层： com.itheima.controller 或者 com.itheima.web
	业务逻辑层： com.itheima.service
	数据访问层： com.itheima.dao 或者 com.itheima.mapper
```


# Listener(不常用)

概述：

```
Listener 表示监听器，是 JavaWeb 三大组件(Servlet、Filter、Listener)之一。
监听器可以监听就是在 application ， session ， request 三个对象创建、销毁或者往其中添加修改删除属性时自动执行代码的功能组件。
```

使用：

```
只有 ServletContextListener 这个监听器后期我们会接触到， ServletContextListener 是用来监听

ServletContext 对象的创建和销毁。
ServletContextListener 接口中有以下两个方法
void contextInitialized(ServletContextEvent sce) ： ServletContext 对象被创建了会自动执行的方法
void contextDestroyed(ServletContextEvent sce) ： ServletContext 对象被销毁时会自动执行的方法
```


实现步骤：

```
		①定义类，实现ServletContextListener接口
		②在类上加@WebListener注解

	注意：ServletContextListener在web项目启动时，tomcat会自动帮你生成对象
```
