# Changelog

## [1.0.0] - 2026-04-17

### 新增
- 交互式收集项目配置（groupId、artifactId、Java 版本、Spring Boot 版本、端口、context-path）
- 调用 Spring Initializr API 自动生成项目骨架
- 写入 10 个通用业务代码文件（BaseResponse、ResultUtils、PageRequest、DeleteRequest、ErrorCode、BusinessException、GlobalExceptionHandler、ThrowUtils、CorsConfig、HealthController）
- 支持可选组件：Redis、MyBatis-Plus、Sa-Token、RabbitMQ、Kafka
- Java 8 / 17 / 21 版本联动（Java 8 自动使用 Spring Boot 2.7.x）
- Spring Boot 版本选项：2.7.18 / 3.2.x / 3.3.x / 3.4.x / 3.5.x
- 生成完成后输出完整的测试地址（健康检查、Knife4j 文档、Swagger UI）

### 依赖版本
- MyBatis-Plus：3.5.8（Spring Boot 3.x）/ 3.5.7（Spring Boot 2.7.x）
- Sa-Token：1.39.0
- Knife4j：4.4.0（Spring Boot 3.x）/ 4.1.0（Spring Boot 2.7.x）
- Hutool：5.8.38
