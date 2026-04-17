# spring-boot-scaffold-skill

一个 Claude Code Skill，用自然语言一句话初始化 Spring Boot 后端脚手架。

## 使用方式

将 `spring-boot-scaffold.md` 放到 `~/.claude/commands/` 目录下，然后在任意 Claude Code 会话中说：

```
初始化后端项目
```

或者直接用命令：

```
/spring-boot-scaffold
```

## 功能

- 交互式收集项目配置（groupId、artifactId、Java 版本、Spring Boot 版本等）
- 调用 Spring Initializr API 自动生成项目骨架
- 写入完整的通用业务代码：
  - `BaseResponse` / `ResultUtils` / `PageRequest` / `DeleteRequest`
  - `ErrorCode` / `BusinessException` / `GlobalExceptionHandler` / `ThrowUtils`
  - `CorsConfig` / `HealthController`
- 支持可选组件：Redis、MyBatis-Plus、Sa-Token、RabbitMQ、Kafka
- Java 版本联动：选 Java 8 自动使用 Spring Boot 2.7.x，选 Java 17/21 可选 3.x

## 支持版本

| Java | Spring Boot |
|------|-------------|
| 8 | 2.7.18（自动） |
| 17 / 21 | 3.2.x / 3.3.x / 3.4.x / 3.5.x |

## 安装

```bash
cp spring-boot-scaffold.md ~/.claude/commands/
```

## 生成的项目结构

```
{artifactId}/
├── pom.xml
├── src/main/resources/
│   └── application.yml
└── src/main/java/{groupPath}/{artifactId}/
    ├── common/
    │   ├── BaseResponse.java
    │   ├── ResultUtils.java
    │   ├── PageRequest.java
    │   └── DeleteRequest.java
    ├── exception/
    │   ├── ErrorCode.java
    │   ├── BusinessException.java
    │   ├── GlobalExceptionHandler.java
    │   └── ThrowUtils.java
    ├── config/
    │   ├── CorsConfig.java
    │   ├── MybatisPlusConfig.java  # 可选
    │   └── SaTokenConfig.java      # 可选
    └── controller/
        └── HealthController.java
```

## 启动后验证

| 功能 | 地址（默认配置） |
|------|----------------|
| 健康检查 | `http://localhost:8123/api/health/` |
| 接口文档 | `http://localhost:8123/api/doc.html` |
| Swagger UI | `http://localhost:8123/api/swagger-ui/index.html` |
