# Spring Authorization Server - 客户端模块

## 模块概述

客户端模块(`demo-client`)是一个OAuth 2.0客户端应用程序，演示了如何与授权服务器进行交互以获取访问令牌，并使用这些令牌访问受保护的资源。该模块实现了多种OAuth 2.0授权流程，包括授权码流程、客户端凭证流程和设备码流程。

## 主要特性

1. **多种OAuth 2.0授权流程支持**:
   - 授权码授权流程(Authorization Code Grant)
   - 客户端凭证授权流程(Client Credentials Grant)
   - 设备码授权流程(Device Code Grant)
   - OpenID Connect认证

2. **WebClient集成**:
   - 使用OAuth 2.0访问令牌调用资源服务器API
   - 自动处理令牌刷新

3. **用户界面**:
   - 主页显示从资源服务器获取的消息
   - 设备激活页面
   - 登出功能

4. **安全特性**:
   - 安全会话管理
   - CSRF保护
   - 受保护的资源访问

## 技术架构

### 核心依赖

- Spring Boot OAuth2 Client
- Spring Boot Security
- Spring Boot Web (使用Undertow作为Web服务器)
- Spring Boot Thymeleaf (用于视图渲染)
- Spring WebFlux和Reactor Netty (用于WebClient)
- Bootstrap和jQuery (用于前端UI)

### 主要配置类

1. **SecurityConfig**:
   - 配置OAuth 2.0客户端安全
   - 设置受保护资源访问规则
   - 配置登出处理

2. **WebClientConfig**:
   - 配置WebClient以支持OAuth 2.0授权
   - 设置令牌交换过滤器

### 授权扩展

1. **设备码授权支持**:
   - `DeviceCodeOAuth2AuthorizedClientProvider.java`: 提供设备码授权支持
   - `OAuth2DeviceAccessTokenResponseClient.java`: 处理设备码令牌响应
   - `OAuth2DeviceGrantRequest.java`: 封装设备码授权请求

### 控制器

1. **AuthorizationController**:
   - 处理不同授权类型的请求
   - 使用获取的令牌访问资源服务器
   - 处理授权错误

2. **DeviceController**:
   - 处理设备码授权流程
   - 显示设备激活页面

3. **DefaultController**:
   - 处理主页和其他基本视图

## 配置详解

### application.yml

主要配置包括:
- 服务器端口(8080)
- OAuth2客户端注册信息:
  - messaging-client-oidc: 用于OpenID Connect认证
  - messaging-client-authorization-code: 用于授权码流程
  - messaging-client-client-credentials: 用于客户端凭证流程
  - messaging-client-device-code: 用于设备码流程
- 资源服务器基础URI(http://127.0.0.1:8090/messages)
- 日志级别设置

### OAuth2客户端配置

客户端模块配置了多个OAuth2客户端注册，每个用于不同的授权流程:

1. **messaging-client-oidc**:
   - 用于OpenID Connect认证
   - 请求openid和profile作用域
   - 使用授权码授权类型

2. **messaging-client-authorization-code**:
   - 用于标准OAuth2授权码流程
   - 请求message.read和message.write作用域
   - 重定向URI: http://127.0.0.1:8080/authorized

3. **messaging-client-client-credentials**:
   - 用于客户端凭证流程
   - 适用于服务器到服务器的通信

4. **messaging-client-device-code**:
   - 用于设备码流程
   - 无需客户端认证方法
   - 使用特殊的设备码授权类型

## 授权流程详解

### 授权码流程

1. 用户访问需要授权的资源
2. 客户端重定向用户到授权服务器
3. 用户在授权服务器进行身份验证
4. 用户同意授权请求的范围
5. 授权服务器重定向回客户端，带有授权码
6. 客户端使用授权码向授权服务器请求访问令牌
7. 客户端使用访问令牌调用资源服务器API

这个流程在`AuthorizationController`的`authorizationCodeGrant`方法中实现。

### 客户端凭证流程

1. 客户端直接向授权服务器请求访问令牌，使用自己的凭证
2. 授权服务器验证客户端凭证并颁发访问令牌
3. 客户端使用访问令牌调用资源服务器API

这个流程在`AuthorizationController`的`clientCredentialsGrant`方法中实现。

### 设备码流程

1. 客户端请求设备码和用户码
2. 客户端显示用户码和验证URL给用户
3. 用户在另一设备上访问验证URL并输入用户码
4. 用户在授权服务器进行身份验证并同意授权
5. 客户端轮询授权服务器获取访问令牌
6. 获取令牌后，客户端可以访问资源服务器

这个流程通过`DeviceController`和自定义的设备码授权组件实现。

## WebClient集成

`WebClientConfig`配置了一个支持OAuth 2.0的WebClient，可以:
- 自动附加OAuth2访问令牌到请求
- 处理令牌过期和刷新
- 支持不同的授权类型

## 使用指南

1. **启动服务**:
   - 确保授权服务器(端口9000)已启动
   - 确保资源服务器(端口8090)已启动
   - 运行`DemoClientApplication`类
   - 客户端将在8080端口启动

2. **测试授权码流程**:
   - 访问`http://localhost:8080/authorize?grant_type=authorization_code`
   - 系统会重定向到授权服务器进行认证
   - 认证后，将显示从资源服务器获取的消息

3. **测试客户端凭证流程**:
   - 访问`http://localhost:8080/authorize?grant_type=client_credentials`
   - 系统会直接使用客户端凭证获取令牌
   - 显示从资源服务器获取的消息

4. **测试设备码流程**:
   - 访问`http://localhost:8080/authorize?grant_type=device_code`
   - 系统会显示设备激活页面
   - 按照指示在另一设备上完成授权

## 扩展点

1. **添加更多授权类型**:
   - 实现其他OAuth 2.0授权类型，如隐式授权或密码授权

2. **增强用户体验**:
   - 改进UI设计
   - 添加更多用户友好的错误处理

3. **增加安全性**:
   - 实现PKCE(Proof Key for Code Exchange)增强授权码流程安全性
   - 添加令牌验证和撤销功能

4. **集成其他资源服务器**:
   - 配置多个资源服务器访问