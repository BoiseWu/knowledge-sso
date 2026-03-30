# Knowledge SSO Service

一个基于Spring Boot和JWT的单点登录(Single Sign-On)服务。

## 功能特性

- ✅ 用户注册和登录
- ✅ JWT访问令牌认证
- ✅ 刷新令牌机制
- ✅ 令牌验证
- ✅ 用户登出
- ✅ 基于角色的访问控制(RBAC)
- ✅ 密码加密(BCrypt)
- ✅ H2内存数据库(开发环境)
- ✅ MySQL支持(生产环境)
- ✅ RESTful API设计
- ✅ 全局异常处理
- ✅ CORS配置
- ✅ Docker支持

## 技术栈

- **Java 8**
- **Spring Boot 2.7.18**
- **Spring Security**
- **JWT (JSON Web Token)**
- **JPA/Hibernate**
- **H2 Database** (开发)
- **MySQL** (生产)
- **Maven**
- **Lombok**

## 项目结构

```
knowledge-sso/
├── src/
│   ├── main/
│   │   ├── java/com/knowledge/sso/
│   │   │   ├── config/           # 配置类
│   │   │   ├── controller/       # 控制器
│   │   │   ├── dto/              # 数据传输对象
│   │   │   ├── entity/           # 实体类
│   │   │   ├── exception/        # 异常处理
│   │   │   ├── repository/       # 数据访问层
│   │   │   ├── security/         # 安全相关
│   │   │   ├── service/          # 业务逻辑层
│   │   │   ├── util/             # 工具类
│   │   │   └── SsoApplication.java
│   │   └── resources/
│   │       ├── application.yml           # 开发环境配置
│   │       └── application-prod.yml      # 生产环境配置
│   └── test/                     # 测试代码
├── pom.xml                       # Maven配置
├── Dockerfile                    # Docker镜像构建
├── docker-compose.yml            # Docker Compose配置
└── API.md                        # API文档
```

## 快速开始

### 前置要求

- Java 8 或更高版本
- Maven 3.6 或更高版本

### 本地运行

#### 方式1: 使用Maven

```bash
# 克隆项目
cd knowledge-sso

# 编译项目
mvn clean package -DskipTests

# 运行应用
java -jar target/knowledge-sso-1.0.0.jar
```

#### 方式2: 使用Maven运行

```bash
mvn spring-boot:run
```

#### 方式3: 使用启动脚本

**Linux/Mac:**
```bash
chmod +x run.sh
./run.sh
```

**Windows:**
```cmd
run.bat
```

### 使用Docker

#### 仅运行SSO服务(使用H2数据库)

```bash
# 构建镜像
docker build -t knowledge-sso .

# 运行容器
docker run -p 8080:8080 knowledge-sso
```

#### 使用Docker Compose(包含MySQL数据库)

```bash
# 启动所有服务
docker-compose up -d

# 查看日志
docker-compose logs -f

# 停止服务
docker-compose down
```

## 访问应用

应用启动后,可以通过以下地址访问:

- **API Base URL**: http://localhost:8080/api
- **H2 Console** (仅开发环境): http://localhost:8080/api/h2-console
  - JDBC URL: `jdbc:h2:mem:sso_db`
  - Username: `sa`
  - Password: (留空)

## 默认用户

系统启动时会自动创建以下测试用户:

| 用户名 | 密码 | 角色 |
|--------|------|------|
| admin | admin123 | USER, ADMIN |
| demo | demo123 | USER |

## API端点

### 认证相关

| 方法 | 端点 | 描述 | 认证 |
|------|------|------|------|
| POST | `/auth/register` | 注册新用户 | ❌ |
| POST | `/auth/login` | 用户登录 | ❌ |
| POST | `/auth/refresh` | 刷新令牌 | ❌ |
| POST | `/auth/logout` | 用户登出 | ❌ |
| GET | `/auth/validate` | 验证令牌 | ✅ |

### 用户相关

| 方法 | 端点 | 描述 | 认证 |
|------|------|------|------|
| GET | `/user/profile` | 获取用户信息 | ✅ |
| GET | `/user/health` | 健康检查 | ❌ |

详细的API文档请查看 [API.md](API.md)

## 测试API

### 使用cURL

```bash
# 登录
curl -X POST http://localhost:8080/api/auth/login \
  -H "Content-Type: application/json" \
  -d '{"username":"admin","password":"admin123"}'

# 获取用户信息(需要替换<TOKEN>为实际的访问令牌)
curl -X GET http://localhost:8080/api/user/profile \
  -H "Authorization: Bearer <TOKEN>"
```

### 使用HTTP文件

项目包含了 `test-api.http` 文件,可以使用VS Code的REST Client扩展或IntelliJ IDEA的HTTP Client直接测试。

## 配置说明

### JWT配置

在 `application.yml` 中配置:

```yaml
jwt:
  secret: YourSuperSecretKey  # JWT签名密钥(生产环境必须修改)
  expiration: 86400000        # 访问令牌过期时间(24小时)
  refresh-expiration: 604800000  # 刷新令牌过期时间(7天)
```

### 数据库配置

**开发环境(H2):**
```yaml
spring:
  datasource:
    url: jdbc:h2:mem:sso_db
    driver-class-name: org.h2.Driver
```

**生产环境(MySQL):**
```yaml
spring:
  datasource:
    url: jdbc:mysql://localhost:3306/sso_db
    username: root
    password: password
```

## 安全建议

在生产环境中,请注意以下安全事项:

1. ⚠️ **修改JWT密钥**: 使用环境变量设置强密钥
   ```bash
   export JWT_SECRET="YourStrongSecretKey"
   ```

2. ⚠️ **使用HTTPS**: 在生产环境中启用SSL/TLS

3. ⚠️ **修改默认用户**: 删除或修改默认的admin/demo用户

4. ⚠️ **配置CORS**: 根据实际需求配置允许的来源

5. ⚠️ **数据库安全**: 使用强密码并限制数据库访问

## 开发

### 运行测试

```bash
mvn test
```

### 构建项目

```bash
mvn clean package
```

### 查看日志

应用日志位于:
- 开发环境: 控制台输出
- 生产环境: `logs/sso-service.log`

## 许可证

MIT License

## 联系方式

如有问题或建议,请联系开发团队。

---

**Happy Coding! 🚀**
# knowledge-sso
# knowledge-api
