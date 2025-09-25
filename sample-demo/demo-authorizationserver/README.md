# Spring Authorization Server - 授权服务器模块

## 模块概述

授权服务器模块(`demo-authorizationserver`)是整个系统的核心，负责实现OAuth 2.0和OpenID Connect 1.0规范，处理客户端注册、用户认证、授权请求、令牌颁发和验证等功能。该模块基于Spring Authorization Server框架构建，提供了完整的授权服务器功能。

## 主要特性

1. **多种授权类型支持**:
   - 授权码授权流程(Authorization Code Grant)
   - 客户端凭证授权流程(Client Credentials Grant)
   - 设备码授权流程(Device Code Grant)
   - 刷新令牌(Refresh Token)

2. **OpenID Connect支持**:
   - ID令牌颁发
   - 用户信息端点
   - 标准声明支持

3. **联合身份认证**:
   - 支持GitHub OAuth登录
   - 支持Gitee OAuth登录
   - 自定义身份提供者集成

4. **自定义授权同意页面**:
   - 用户可查看并选择授权范围
   - 支持已授权范围记忆

5. **设备激活流程**:
   - 支持设备码验证
   - 用户设备激活界面

6. **安全特性**:
   - JWT令牌签名与验证
   - 客户端认证
   - CSRF保护
   - 跨域资源共享(CORS)配置

## 技术架构

### 核心依赖

- Spring Security OAuth2 Authorization Server 1.1.1
- Spring Boot OAuth2 Client
- Spring Boot Security
- Spring Boot Web (使用Undertow作为Web服务器)
- Spring Boot Thymeleaf (用于视图渲染)
- Spring Boot JDBC (用于持久化)
- H2数据库 (用于示例存储)

### 主要配置类

1. **AuthorizationServerConfig**:
   - 配置OAuth 2.0授权服务器核心功能
   - 设置端点、客户端认证方法
   - 配置JWT令牌源
   - 注册客户端信息
   - 配置授权服务和授权同意服务

2. **DefaultSecurityConfig**:
   - 配置Web安全
   - 设置表单登录和OAuth2登录
   - 配置安全过滤器链
   - 设置用户详情服务
   - 配置CORS

### 关键组件

1. **JWK (JSON Web Key)处理**:
   - `Jwks.java`: 生成RSA密钥对
   - `KeyGeneratorUtils.java`: 密钥生成工具

2. **联合身份认证**:
   - `FederatedIdentityAuthenticationSuccessHandler.java`: 处理第三方登录成功
   - `FederatedIdentityIdTokenCustomizer.java`: 自定义ID令牌
   - `UserRepositoryOAuth2UserHandler.java`: 处理OAuth2用户信息

3. **设备认证**:
   - `DeviceClientAuthenticationProvider.java`: 设备客户端认证提供者
   - `DeviceClientAuthenticationToken.java`: 设备客户端认证令牌
   - `DeviceClientAuthenticationConverter.java`: 设备客户端认证转换器

4. **控制器**:
   - `AuthorizationConsentController.java`: 处理授权同意页面
   - `DeviceController.java`: 处理设备激活流程
   - `LoginController.java`: 处理登录页面
   - `UserInfoController.java`: 提供用户信息
   - `DefaultErrorController.java`: 处理错误页面

## 数据存储

授权服务器使用以下表结构存储OAuth 2.0相关数据:

1. **oauth2_registered_client**: 存储注册的客户端信息
2. **oauth2_authorization**: 存储授权信息
3. **oauth2_authorization_consent**: 存储用户授权同意记录

这些表由Spring Authorization Server框架自动创建和管理。在示例中使用H2内存数据库，但可以轻松切换到其他关系型数据库。

## 配置详解

### application.yml

主要配置包括:
- 服务器端口(9000)
- OAuth2客户端配置(GitHub和Gitee)
- 日志级别设置

### 客户端注册

在`AuthorizationServerConfig`中预配置了两个客户端:
1. **messaging-client**: 用于常规OAuth2流程
   - 支持授权码、刷新令牌和客户端凭证授权类型
   - 配置了重定向URI和作用域

2. **device-messaging-client**: 用于设备码流程
   - 支持设备码和刷新令牌授权类型
   - 无需客户端认证方法

### 自定义授权同意页面

`AuthorizationConsentController`提供了自定义授权同意页面的功能:
- 显示客户端请求的权限范围
- 区分新请求的范围和已授权的范围
- 提供范围的详细描述
- 支持设备验证流程

### 安全配置

`DefaultSecurityConfig`配置了Web安全:
- 静态资源访问控制
- 登录页面配置
- OAuth2登录集成
- 会话管理
- CORS配置

## 使用指南

1. **启动服务**:
   - 运行`DemoAuthorizationServerApplication`类
   - 服务将在9000端口启动

2. **访问登录页面**:
   - 访问`http://localhost:9000/login`
   - 可以使用内存中预配置的用户(user1/password)登录
   - 或使用GitHub/Gitee账号登录

3. **测试OAuth2流程**:
   - 配合客户端模块测试完整的OAuth2授权流程
   - 可以观察授权请求、令牌颁发和资源访问过程

4. **查看H2控制台**:
   - 如需查看数据库内容，可配置H2控制台

## 扩展点

1. **自定义用户存储**:
   - 替换`InMemoryUserDetailsManager`实现自定义用户管理

2. **添加更多身份提供者**:
   - 在`application.yml`中配置其他OAuth2提供者

3. **自定义令牌格式**:
   - 实现`OAuth2TokenCustomizer`自定义令牌内容

4. **持久化存储**:
   - 替换H2数据库为生产环境数据库

5. **增强安全性**:
   - 配置TLS/SSL
   - 实现更复杂的密码策略
   - 添加多因素认证