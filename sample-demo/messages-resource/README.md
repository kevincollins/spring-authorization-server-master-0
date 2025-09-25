# Spring Authorization Server - 资源服务器模块

## 模块概述

资源服务器模块(`messages-resource`)是一个OAuth 2.0资源服务器，提供受保护的API资源，只允许持有有效访问令牌的客户端访问。该模块演示了如何使用Spring Security OAuth2 Resource Server保护REST API，并验证由授权服务器颁发的JWT令牌。

## 主要特性

1. **OAuth 2.0资源保护**:
   - 使用JWT令牌验证请求
   - 基于Spring Security的资源访问控制

2. **REST API**:
   - 提供简单的消息API
   - 演示受保护资源的访问控制

3. **安全特性**:
   - JWT令牌验证
   - 与授权服务器集成
   - 跨域资源共享(CORS)配置

## 技术架构

### 核心依赖

- Spring Boot OAuth2 Resource Server
- Spring Boot Security
- Spring Boot Web (使用Undertow作为Web服务器)
- Lombok (用于简化代码)

### 主要配置类

1. **ResourceServerConfig**:
   - 配置OAuth 2.0资源服务器安全
   - 设置JWT令牌验证
   - 配置资源访问规则
   - 设置CORS策略

### 控制器

1. **MessagesController**:
   - 提供简单的REST API端点
   - 返回示例消息数据

## 配置详解

### application.yml

主要配置包括:
- 服务器端口(8090)
- OAuth2资源服务器JWT配置:
  - 令牌颁发者URI(http://192.168.56.1:9000)
- 日志级别设置

### 资源服务器安全配置

`ResourceServerConfig`配置了资源服务器的安全性:

```java
@Bean
SecurityFilterChain securityFilterChain(HttpSecurity http) throws Exception {
    http
        .authorizeHttpRequests(authorizeHttpRequests->authorizeHttpRequests.anyRequest().authenticated())
        .oauth2ResourceServer(oauth2ResourceServer->oauth2ResourceServer.jwt(Customizer.withDefaults()));
    return http.build();
}
```

这个配置:
- 要求所有请求都需要认证
- 配置OAuth2资源服务器使用JWT令牌
- 使用默认的JWT解码器，该解码器会从授权服务器的元数据端点获取公钥

### CORS配置

资源服务器配置了CORS支持，允许来自不同源的客户端访问API:

```java
@Bean
public CorsFilter corsFilter() {
    CorsConfiguration configuration = new CorsConfiguration();
    configuration.addAllowedOrigin("*");
    configuration.setAllowCredentials(true);
    configuration.addAllowedMethod("*");
    configuration.addAllowedHeader("*");
    UrlBasedCorsConfigurationSource configurationSource = new UrlBasedCorsConfigurationSource();
    configurationSource.registerCorsConfiguration("/**", configuration);
    return new CorsFilter(configurationSource);
}
```

## API端点

资源服务器提供了一个简单的消息API:

```java
@RestController
public class MessagesController {
    @GetMapping("/messages")
    public String[] getMessages() {
        return new String[] {"Message 1", "Message 2", "Message 3"};
    }
}
```

这个API:
- 位于`/messages`路径
- 返回一个包含三条消息的字符串数组
- 受OAuth2安全保护，需要有效的访问令牌才能访问

## JWT令牌验证

资源服务器通过以下步骤验证JWT令牌:

1. 从请求的Authorization头部提取Bearer令牌
2. 使用从授权服务器获取的公钥验证令牌签名
3. 验证令牌的颁发者、受众、过期时间等声明
4. 提取令牌中的权限和范围信息
5. 创建认证对象并设置安全上下文

这个过程由Spring Security OAuth2 Resource Server自动处理。

## 使用指南

1. **启动服务**:
   - 确保授权服务器(端口9000)已启动
   - 运行`MessagesResourceApplication`类
   - 资源服务器将在8090端口启动

2. **测试API访问**:
   - 尝试直接访问`http://localhost:8090/messages`将返回401未授权错误
   - 通过客户端模块获取有效令牌后访问API
   - 或使用Postman等工具，附加有效的Bearer令牌进行测试

3. **验证令牌**:
   - 可以通过调试日志观察令牌验证过程
   - 查看授权服务器颁发的令牌内容和声明

## 扩展点

1. **添加更多API资源**:
   - 实现更复杂的业务逻辑和API端点
   - 添加CRUD操作

2. **细粒度授权控制**:
   - 基于OAuth2范围实现不同级别的访问控制
   - 添加方法级安全注解

3. **增强安全性**:
   - 实现令牌内省(Token Introspection)
   - 添加令牌黑名单机制
   - 配置资源服务器与授权服务器的安全通信

4. **监控和审计**:
   - 添加API访问日志
   - 实现安全事件监控