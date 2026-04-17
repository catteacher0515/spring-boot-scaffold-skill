---
name: spring-boot-scaffold
description: 交互式 Spring Boot 后端脚手架生成器。收集项目配置后调用 Spring Initializr API 生成骨架，并叠加通用业务代码（BaseResponse、异常体系、CORS、健康检查等）。支持可选组件：Redis、MyBatis-Plus、Sa-Token、RabbitMQ/Kafka。
triggers:
  - 初始化后端项目
  - 新建 Spring Boot 项目
  - 搭建后端脚手架
  - 创建 Spring Boot 脚手架
  - 初始化 Spring Boot
---

# Spring Boot 脚手架生成器

## 第一步：收集基本信息

使用 AskUserQuestion 工具，依次收集以下信息（每次一个问题）：

1. **groupId**：Maven 组织标识，例如 `com.example`（默认：`com.example`）
2. **artifactId**：项目名称/模块名，例如 `my-project`（默认：`my-project`）
3. **Java 版本**：单选
   - `8`（注意：将自动使用 Spring Boot 2.7.x，不支持 3.x）
   - `17`
   - `21`（默认）
4. **Spring Boot 版本**（仅当 Java 版本为 17 或 21 时询问）：单选
   - `3.2.12`
   - `3.3.6`
   - `3.4.1`
   - `3.5.0`（默认）
   - 若用户选择了 Java 8，跳过此问题，自动使用 `2.7.18`，并告知用户："Java 8 不支持 Spring Boot 3.x，已自动选择 Spring Boot 2.7.18。"
5. **服务端口**：数字，默认 `8123`
6. **context-path**：接口前缀，默认 `/api`

## 第二步：收集可选组件

使用 AskUserQuestion 工具，依次询问：

7. **是否需要 Redis**：是 / 否（默认：否）
8. **是否需要 MyBatis-Plus**：是 / 否（默认：是）
9. **是否需要 Sa-Token**：是 / 否（默认：否）
10. **是否需要消息队列**：单选
    - 不需要（默认）
    - RabbitMQ
    - Kafka

## 第三步：调用 Spring Initializr API 生成骨架

根据收集到的信息，构造并执行以下命令。

**注意：**
- 将所有大写变量替换为用户实际输入的值
- `PACKAGE_NAME` = groupId + "." + artifactId（artifactId 中的连字符需去掉，例如 `my-project` → `myproject`，即 `com.example.myproject`）

```bash
# 示例（替换为用户实际输入）：
# GROUP_ID=com.example
# ARTIFACT_ID=my-project
# PACKAGE_NAME=com.example.myproject   ← 注意：连字符去掉
# JAVA_VERSION=21
# BOOT_VERSION=3.3.6

curl -f -s "https://start.spring.io/starter.zip" \
  -d "type=maven-project" \
  -d "language=java" \
  -d "bootVersion=BOOT_VERSION" \
  -d "groupId=GROUP_ID" \
  -d "artifactId=ARTIFACT_ID" \
  -d "name=ARTIFACT_ID" \
  -d "packageName=PACKAGE_NAME" \
  -d "javaVersion=JAVA_VERSION" \
  -d "dependencies=web,mysql,lombok,devtools" \
  -o starter.zip || { echo "❌ 下载失败，请检查网络连接或参数是否正确"; exit 1; }

unzip starter.zip -d ARTIFACT_ID && rm starter.zip
```

执行后验证目录已创建：

```bash
ls $ARTIFACT_ID/pom.xml
```

期望输出：文件路径，无报错。

## 第四步：追加依赖到 pom.xml

### 固定追加（所有项目）

在 `ARTIFACT_ID/pom.xml` 的 `<dependencies>` 块末尾（`</dependencies>` 之前）追加以下依赖：

**Hutool（所有版本）：**
```xml
<dependency>
    <groupId>cn.hutool</groupId>
    <artifactId>hutool-all</artifactId>
    <version>5.8.38</version>
</dependency>
```

**AOP（所有版本）：**
```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-aop</artifactId>
</dependency>
```

**Knife4j（Spring Boot 3.x）：**
```xml
<dependency>
    <groupId>com.github.xiaoymin</groupId>
    <artifactId>knife4j-openapi3-jakarta-spring-boot-starter</artifactId>
    <version>4.4.0</version>
</dependency>
```

**Knife4j（Spring Boot 2.7.x，仅当用户选择 Java 8 时使用）：**
```xml
<dependency>
    <groupId>com.github.xiaoymin</groupId>
    <artifactId>knife4j-openapi3-spring-boot-starter</artifactId>
    <version>4.1.0</version>
</dependency>
```

### 可选追加

**Redis（用户选择时追加）：**
```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-redis</artifactId>
</dependency>
<dependency>
    <groupId>org.apache.commons</groupId>
    <artifactId>commons-pool2</artifactId>
</dependency>
```

**MyBatis-Plus（Spring Boot 3.x，用户选择时追加）：**
```xml
<dependency>
    <groupId>com.baomidou</groupId>
    <artifactId>mybatis-plus-spring-boot3-starter</artifactId>
    <version>3.5.8</version>
</dependency>
```

**MyBatis-Plus（Spring Boot 2.7.x，用户选择时追加）：**
```xml
<dependency>
    <groupId>com.baomidou</groupId>
    <artifactId>mybatis-plus-boot-starter</artifactId>
    <version>3.5.7</version>
</dependency>
```

**Sa-Token（Spring Boot 3.x，用户选择时追加）：**
```xml
<dependency>
    <groupId>cn.dev33</groupId>
    <artifactId>sa-token-spring-boot3-starter</artifactId>
    <version>1.39.0</version>
</dependency>
```

**Sa-Token（Spring Boot 2.7.x，用户选择时追加）：**
```xml
<dependency>
    <groupId>cn.dev33</groupId>
    <artifactId>sa-token-spring-boot-starter</artifactId>
    <version>1.39.0</version>
</dependency>
```

**RabbitMQ（用户选择时追加）：**
```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-amqp</artifactId>
</dependency>
```

**Kafka（用户选择时追加）：**
```xml
<dependency>
    <groupId>org.springframework.kafka</groupId>
    <artifactId>spring-kafka</artifactId>
</dependency>
```

## 第五步：覆写 application.yml

将 `ARTIFACT_ID/src/main/resources/application.yml` 替换为以下内容（根据用户选项决定包含哪些块）。

### 基础块（所有项目必含）

```yaml
# ===================================================================
# 第一部分：【业务变量区】（初始化项目时，请重点关注带 #【改】 标签的行）
# ===================================================================

server:
  port: 8123          # 【改】服务端口（用户输入的端口）
  servlet:
    context-path: /api  # 【改】接口前缀（用户输入的 context-path）

spring:
  application:
    name: my-project  # 【改】项目名称（用户输入的 artifactId）

  # 【MySQL 数据库连接】
  datasource:
    driver-class-name: com.mysql.cj.jdbc.Driver
    url: jdbc:mysql://localhost:3306/my_project?serverTimezone=Asia/Shanghai&useUnicode=true&characterEncoding=utf-8  # 【改】库名
    username: root
    password: your_password  # 【改】数据库密码
```

### MyBatis-Plus 块（用户选择 MyBatis-Plus 时追加）

```yaml
  # 【MyBatis-Plus 配置】
mybatis-plus:
  mapper-locations: classpath*:/mapper/**/*.xml
  type-aliases-package: GROUP_ID.ARTIFACT_ID_CLEAN.model.entity  # 【改】实体类包路径
  configuration:
    map-underscore-to-camel-case: true
    log-impl: org.apache.ibatis.logging.stdout.StdOutImpl
```

### Redis 块（用户选择 Redis 时追加）

```yaml
  # 【Redis 缓存连接】
  data:
    redis:
      host: localhost
      port: 6379
      password:
      database: 0
      lettuce:
        pool:
          max-active: 8
          max-idle: 8
          min-idle: 0
```

### RabbitMQ 块（用户选择 RabbitMQ 时追加）

```yaml
  # 【RabbitMQ 消息队列】
  rabbitmq:
    host: localhost
    port: 5672
    username: guest
    password: guest  # 【改】密码
    virtual-host: /
```

### Kafka 块（用户选择 Kafka 时追加）

```yaml
  # 【Kafka 消息队列】
  kafka:
    bootstrap-servers: localhost:9092  # 【改】Kafka 地址
    producer:
      key-serializer: org.apache.kafka.common.serialization.StringSerializer
      value-serializer: org.apache.kafka.common.serialization.StringSerializer
    consumer:
      group-id: my-project-group  # 【改】消费者组 ID（用户输入的 artifactId + "-group"）
      key-deserializer: org.apache.kafka.common.serialization.StringDeserializer
      value-deserializer: org.apache.kafka.common.serialization.StringDeserializer
```

### Sa-Token 块（用户选择 Sa-Token 时追加）

```yaml
  # 【Sa-Token 配置】
sa-token:
  token-name: Authorization
  timeout: 2592000       # token 有效期（秒），默认 30 天
  is-concurrent: true    # 是否允许同一账号多地同时登录
  is-share: true         # 多次登录是否共用一个 token
  token-style: uuid
```

### 架构常量块（所有项目必含，追加在最后）

```yaml
# ===================================================================
# 第二部分：【架构常量区】（除扫描路径外，其余通常无需改动）
# ===================================================================

# 【SpringDoc & Knife4j 接口文档配置】
springdoc:
  group-configs:
    - group: 'api'
      paths-to-match: '/**'
      packages-to-scan: com.example.myproject.controller  # 【改】Controller 包路径（用户输入的 packageName + ".controller"）
  swagger-ui:
    path: /swagger-ui.html
    tags-sorter: alpha
    operations-sorter: alpha
  api-docs:
    path: /v3/api-docs

knife4j:
  enable: true
  setting:
    language: zh_cn
    enable-swagger-models: true
    swagger-model-name: "数据模型"
    enable-after-script: true
```

## 第六步：写入固定通用代码

所有文件写入路径：`ARTIFACT_ID/src/main/java/GROUP_PATH/ARTIFACT_ID_CLEAN/`
（GROUP_PATH = groupId 中的 `.` 替换为 `/`，例如 `com.example` → `com/example`；ARTIFACT_ID_CLEAN = artifactId 去掉连字符，例如 `my-project` → `myproject`）

### common/BaseResponse.java

```java
package GROUP_ID.ARTIFACT_ID_CLEAN.common;

import GROUP_ID.ARTIFACT_ID_CLEAN.exception.ErrorCode;
import lombok.Data;
import java.io.Serializable;

@Data
public class BaseResponse<T> implements Serializable {

    private int code;
    private T data;
    private String message;

    public BaseResponse(int code, T data, String message) {
        this.code = code;
        this.data = data;
        this.message = message;
    }

    public BaseResponse(int code, T data) {
        this(code, data, "");
    }

    public BaseResponse(ErrorCode errorCode) {
        this(errorCode.getCode(), null, errorCode.getMessage());
    }
}
```

### common/ResultUtils.java

```java
package GROUP_ID.ARTIFACT_ID_CLEAN.common;

import GROUP_ID.ARTIFACT_ID_CLEAN.exception.ErrorCode;

public class ResultUtils {

    public static <T> BaseResponse<T> success(T data) {
        return new BaseResponse<>(0, data, "ok");
    }

    public static BaseResponse<?> error(ErrorCode errorCode) {
        return new BaseResponse<>(errorCode);
    }

    public static BaseResponse<?> error(int code, String message) {
        return new BaseResponse<>(code, null, message);
    }

    public static BaseResponse<?> error(ErrorCode errorCode, String message) {
        return new BaseResponse<>(errorCode.getCode(), null, message);
    }
}
```

### common/PageRequest.java

```java
package GROUP_ID.ARTIFACT_ID_CLEAN.common;

import lombok.Data;

@Data
public class PageRequest {

    private int current = 1;
    private int pageSize = 10;
    private String sortField;
    private String sortOrder;
}
```

### common/DeleteRequest.java

```java
package GROUP_ID.ARTIFACT_ID_CLEAN.common;

import lombok.Data;
import java.io.Serializable;

@Data
public class DeleteRequest implements Serializable {

    private Long id;
}
```

### exception/ErrorCode.java

```java
package GROUP_ID.ARTIFACT_ID_CLEAN.exception;

import lombok.Getter;

@Getter
public enum ErrorCode {

    SUCCESS(0, "ok"),
    PARAMS_ERROR(40000, "请求参数错误"),
    NOT_LOGIN_ERROR(40100, "未登录"),
    NO_AUTH_ERROR(40101, "无权限"),
    NOT_FOUND_ERROR(40400, "请求数据不存在"),
    FORBIDDEN_ERROR(40300, "禁止访问"),
    SYSTEM_ERROR(50000, "系统内部异常"),
    OPERATION_ERROR(50001, "操作失败");

    private final int code;
    private final String message;

    ErrorCode(int code, String message) {
        this.code = code;
        this.message = message;
    }
}
```

### exception/BusinessException.java

```java
package GROUP_ID.ARTIFACT_ID_CLEAN.exception;

import lombok.Getter;

@Getter
public class BusinessException extends RuntimeException {

    private final int code;

    public BusinessException(int code, String message) {
        super(message);
        this.code = code;
    }

    public BusinessException(ErrorCode errorCode) {
        super(errorCode.getMessage());
        this.code = errorCode.getCode();
    }

    public BusinessException(ErrorCode errorCode, String message) {
        super(message);
        this.code = errorCode.getCode();
    }
}
```

### exception/GlobalExceptionHandler.java

```java
package GROUP_ID.ARTIFACT_ID_CLEAN.exception;

import GROUP_ID.ARTIFACT_ID_CLEAN.common.BaseResponse;
import GROUP_ID.ARTIFACT_ID_CLEAN.common.ResultUtils;
import io.swagger.v3.oas.annotations.Hidden;
import lombok.extern.slf4j.Slf4j;
import org.springframework.web.bind.annotation.ExceptionHandler;
import org.springframework.web.bind.annotation.RestControllerAdvice;

@Hidden
@RestControllerAdvice
@Slf4j
public class GlobalExceptionHandler {

    @ExceptionHandler(BusinessException.class)
    public BaseResponse<?> businessExceptionHandler(BusinessException e) {
        log.error("BusinessException", e);
        return ResultUtils.error(e.getCode(), e.getMessage());
    }

    @ExceptionHandler(RuntimeException.class)
    public BaseResponse<?> runtimeExceptionHandler(RuntimeException e) {
        log.error("RuntimeException", e);
        return ResultUtils.error(ErrorCode.SYSTEM_ERROR, "系统错误");
    }
}
```

### exception/ThrowUtils.java

```java
package GROUP_ID.ARTIFACT_ID_CLEAN.exception;

public class ThrowUtils {

    public static void throwIf(boolean condition, RuntimeException runtimeException) {
        if (condition) {
            throw runtimeException;
        }
    }

    public static void throwIf(boolean condition, ErrorCode errorCode) {
        throwIf(condition, new BusinessException(errorCode));
    }

    public static void throwIf(boolean condition, ErrorCode errorCode, String message) {
        throwIf(condition, new BusinessException(errorCode, message));
    }
}
```

### config/CorsConfig.java

```java
package GROUP_ID.ARTIFACT_ID_CLEAN.config;

import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.web.cors.CorsConfiguration;
import org.springframework.web.cors.UrlBasedCorsConfigurationSource;
import org.springframework.web.filter.CorsFilter;

@Configuration
public class CorsConfig {

    @Bean
    public CorsFilter corsFilter() {
        CorsConfiguration config = new CorsConfiguration();
        config.addAllowedOriginPattern("*");
        config.addAllowedHeader("*");
        config.addAllowedMethod("*");
        config.setAllowCredentials(true);
        UrlBasedCorsConfigurationSource source = new UrlBasedCorsConfigurationSource();
        source.registerCorsConfiguration("/**", config);
        return new CorsFilter(source);
    }
}
```

### controller/HealthController.java

```java
package GROUP_ID.ARTIFACT_ID_CLEAN.controller;

import GROUP_ID.ARTIFACT_ID_CLEAN.common.BaseResponse;
import GROUP_ID.ARTIFACT_ID_CLEAN.common.ResultUtils;
import io.swagger.v3.oas.annotations.tags.Tag;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

@Tag(name = "系统服务", description = "包含健康检查等基础接口")
@RestController
@RequestMapping("/health")
public class HealthController {

    @GetMapping("/")
    public BaseResponse<String> healthCheck() {
        return ResultUtils.success("ok");
    }
}
```

## 第七步：按选项写入额外文件

### MyBatis-Plus（用户选择时写入）

**config/MybatisPlusConfig.java：**

```java
package GROUP_ID.ARTIFACT_ID_CLEAN.config;

import com.baomidou.mybatisplus.annotation.DbType;
import com.baomidou.mybatisplus.extension.plugins.MybatisPlusInterceptor;
import com.baomidou.mybatisplus.extension.plugins.inner.PaginationInnerInterceptor;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

@Configuration
public class MybatisPlusConfig {

    @Bean
    public MybatisPlusInterceptor mybatisPlusInterceptor() {
        MybatisPlusInterceptor interceptor = new MybatisPlusInterceptor();
        interceptor.addInnerInterceptor(new PaginationInnerInterceptor(DbType.MYSQL));
        return interceptor;
    }
}
```

### Sa-Token（用户选择时写入）

**config/SaTokenConfig.java：**

```java
package GROUP_ID.ARTIFACT_ID_CLEAN.config;

import cn.dev33.satoken.interceptor.SaInterceptor;
import cn.dev33.satoken.router.SaRouter;
import cn.dev33.satoken.stp.StpUtil;
import org.springframework.context.annotation.Configuration;
import org.springframework.web.servlet.config.annotation.InterceptorRegistry;
import org.springframework.web.servlet.config.annotation.WebMvcConfigurer;

@Configuration
public class SaTokenConfig implements WebMvcConfigurer {

    @Override
    public void addInterceptors(InterceptorRegistry registry) {
        registry.addInterceptor(new SaInterceptor(handle -> {
            // 放行健康检查和文档接口
            SaRouter.match("/**")
                    .notMatch(
                        "/health/**",
                        "/swagger-ui/**",
                        "/swagger-ui.html",
                        "/v3/api-docs/**",
                        "/doc.html",
                        "/webjars/**",
                        "/favicon.ico"
                    )
                    .check(r -> StpUtil.checkLogin());
        })).addPathPatterns("/**");
    }
}
```


## 第八步：生成完成提示

所有文件写入完毕后，输出以下提示：

---

✅ 项目 `ARTIFACT_ID` 已生成完毕！

**启动前准备：**
1. 在 MySQL 中创建数据库：`CREATE DATABASE ARTIFACT_ID_CLEAN;`
2. 修改 `src/main/resources/application.yml` 中标注 `#【改】` 的配置项（数据库密码等）

**启动项目：**
```bash
cd ARTIFACT_ID
mvn spring-boot:run
```

**验证测试（启动后访问以下地址）：**

| 功能 | 地址 | 说明 |
|------|------|------|
| 健康检查 | `http://localhost:PORT/CONTEXT_PATH/health/` | 期望返回 `{"code":0,"data":"ok","message":"ok"}` |
| Knife4j 文档 | `http://localhost:PORT/CONTEXT_PATH/doc.html` | 可视化接口调试页面 |
| Swagger UI | `http://localhost:PORT/CONTEXT_PATH/swagger-ui/index.html` | 原生 Swagger 页面 |
| OpenAPI JSON | `http://localhost:PORT/CONTEXT_PATH/v3/api-docs` | 原始接口描述 JSON |

**示例（默认配置 port=8123, context-path=/api）：**
- 健康检查：`http://localhost:8123/api/health/`
- 接口文档：`http://localhost:8123/api/doc.html`

---
