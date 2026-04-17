# spring-boot-scaffold-skill

> 一句话初始化 Spring Boot 后端脚手架的 Claude Code Skill。告别重复劳动，专注业务开发。

## 效果演示

在 Claude Code 中输入：

```
初始化后端项目
```

Skill 会依次询问你的项目配置，然后自动完成：
- 调用 Spring Initializr 生成项目骨架
- 追加 Hutool、Knife4j、AOP 等常用依赖
- 写入 10 个通用业务代码文件
- 生成完整的 application.yml（含注释）
- 按需写入可选组件配置类

## 安装

```bash
cp spring-boot-scaffold.md ~/.claude/commands/
```

## 触发方式

说以下任意一句话即可触发，无需记命令：

- `初始化后端项目`
- `新建 Spring Boot 项目`
- `搭建后端脚手架`
- `创建 Spring Boot 脚手架`
- `初始化 Spring Boot`

或者直接用命令：`/spring-boot-scaffold`

## 交互配置项

| # | 配置项 | 默认值 |
|---|--------|--------|
| 1 | groupId | `com.example` |
| 2 | artifactId | `my-project` |
| 3 | Java 版本 | `21` |
| 4 | Spring Boot 版本 | `3.5.0`（Java 17/21）/ `2.7.18`（Java 8 自动） |
| 5 | 服务端口 | `8123` |
| 6 | context-path | `/api` |
| 7 | Redis | 否 |
| 8 | MyBatis-Plus | 是 |
| 9 | Sa-Token | 否 |
| 10 | 消息队列 | 不需要 |

## 支持版本

| Java | Spring Boot |
|------|-------------|
| 8 | 2.7.18（自动锁定） |
| 17 / 21 | 3.2.x / 3.3.x / 3.4.x / 3.5.x |

## 生成的项目结构

```
{artifactId}/
├── pom.xml                          # 含 Hutool、Knife4j、AOP 及可选依赖
├── src/main/resources/
│   └── application.yml              # 分业务变量区和架构常量区，带 #【改】注释
└── src/main/java/{groupPath}/{artifactId}/
    ├── common/
    │   ├── BaseResponse.java        # 统一响应体
    │   ├── ResultUtils.java         # 响应构造工具
    │   ├── PageRequest.java         # 分页请求基类
    │   └── DeleteRequest.java       # 删除请求基类
    ├── exception/
    │   ├── ErrorCode.java           # 错误码枚举
    │   ├── BusinessException.java   # 业务异常
    │   ├── GlobalExceptionHandler.java  # 全局异常处理器
    │   └── ThrowUtils.java          # 断言工具
    ├── config/
    │   ├── CorsConfig.java          # CORS 跨域配置
    │   ├── MybatisPlusConfig.java   # 可选：分页插件
    │   └── SaTokenConfig.java       # 可选：路由鉴权
    └── controller/
        └── HealthController.java    # 健康检查 GET /health/
```

## 启动后验证

| 功能 | 地址（默认配置） |
|------|----------------|
| 健康检查 | `http://localhost:8123/api/health/` |
| Knife4j 文档 | `http://localhost:8123/api/doc.html` |
| Swagger UI | `http://localhost:8123/api/swagger-ui/index.html` |
| OpenAPI JSON | `http://localhost:8123/api/v3/api-docs` |

## 常见问题

**Q：触发后没有调用这个 Skill，而是走了 brainstorming 流程？**

A：确认文件已放到 `~/.claude/commands/` 目录（不是 `~/.claude/skills/`），重新打开 Claude Code 会话后再试。

**Q：生成项目时报 `PaginationInnerInterceptor` 找不到符号？**

A：MyBatis-Plus 3.5.9+ 移除了该类。Skill 已固定使用 3.5.8，如果你手动修改了版本号，请改回 `3.5.8`。

**Q：选了 Sa-Token 后，`doc.html` 打不开，报 `NotLoginException`？**

A：已在 `SaTokenConfig` 中放行 `/doc.html`、`/webjars/**` 等 Knife4j 所需路径，确保使用最新版 Skill 文件。

**Q：Java 8 能用吗？**

A：可以，选 Java 8 后 Skill 会自动使用 Spring Boot 2.7.18，并提示你不支持 3.x。

**Q：Spring Boot 版本选项里没有最新版怎么办？**

A：Skill 提供的是经过验证的稳定版本。如需最新版，可以在生成后手动修改 `pom.xml` 中的 `spring-boot-starter-parent` 版本号。

## 依赖版本说明

| 依赖 | Spring Boot 3.x | Spring Boot 2.7.x |
|------|----------------|-------------------|
| MyBatis-Plus | 3.5.8 | 3.5.7 |
| Sa-Token | 1.39.0 | 1.39.0 |
| Knife4j | 4.4.0 | 4.1.0 |
| Hutool | 5.8.38 | 5.8.38 |

## License

[MIT](LICENSE)
