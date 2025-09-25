# Spring Authorization Server 示例项目

这个项目是基于Spring Authorization Server 1.1.1版本构建的OAuth 2.0和OpenID Connect 1.0授权服务器示例。项目由三个主要模块组成，分别实现了授权服务器、客户端应用和资源服务器的功能。

## 项目概述

项目基于Spring Boot 3.1.0构建，使用Java 17，采用Maven多模块结构。项目演示了完整的OAuth 2.0授权流程，包括：
- 授权码授权流程
- 客户端凭证授权流程
- 设备码授权流程
- 刷新令牌
- OpenID Connect集成
- 联合身份认证(GitHub和Gitee)

## 模块结构

### 1. demo-authorizationserver

**功能**: 实现OAuth 2.0授权服务器和OpenID Connect提供者

- 支持多种授权类型
- 自定义授权同意页面
- 联合身份认证
- JWT令牌签名与验证
- 设备激活流程

### 2. demo-client

**功能**: 实现OAuth 2.0客户端应用，演示如何与授权服务器和资源服务器交互

- 支持多种OAuth 2.0授权流程
- WebClient集成
- 用户友好的界面
- 安全会话管理

### 3. messages-resource

**功能**: 实现OAuth 2.0资源服务器，提供受保护的API资源

- JWT令牌验证
- 受保护的REST API
- 与授权服务器集成

## 系统整体架构

这三个模块共同构成了一个完整的OAuth 2.0授权系统:

1. **授权服务器(端口9000)**:
   - 处理用户认证
   - 管理客户端注册
   - 颁发访问令牌和刷新令牌
   - 提供OpenID Connect支持

2. **客户端应用(端口8080)**:
   - 请求用户授权
   - 获取访问令牌
   - 使用令牌访问资源服务器

3. **资源服务器(端口8090)**:
   - 验证访问令牌
   - 提供受保护的API资源

## 快速开始

1. 启动授权服务器:
   ```
   cd demo-authorizationserver
   mvn spring-boot:run
   ```

2. 启动资源服务器:
   ```
   cd messages-resource
   mvn spring-boot:run
   ```

3. 启动客户端应用:
   ```
   cd demo-client
   mvn spring-boot:run
   ```

4. 访问客户端应用:
   - 授权码流程: http://localhost:8080/authorize?grant_type=authorization_code
   - 客户端凭证流程: http://localhost:8080/authorize?grant_type=client_credentials
   - 设备码流程: http://localhost:8080/authorize?grant_type=device_code

## 默认用户

- 用户名: user1
- 密码: password

## 详细文档

每个模块都有自己的README文件，提供了更详细的信息:

- [授权服务器文档](demo-authorizationserver/README.md)
- [客户端应用文档](demo-client/README.md)
- [资源服务器文档](messages-resource/README.md)