# 苍穹外卖 (Sky Take-Out)

基于 Spring Boot 的全栈外卖点餐管理系统，后台管理 + 微信小程序用户端。

> 本项目基于[黑马程序员](https://www.itheima.com/)苍穹外卖课程开发，后续持续添砖加瓦 🚀

---

## 技术栈

| 技术 | 版本 | 说明 |
|------|------|------|
| Spring Boot | 2.7.3 | 核心框架 |
| MyBatis | 2.2.0 | ORM |
| PageHelper | 1.3.0 | 分页插件 |
| MySQL | 8.0 | 关系数据库 |
| Druid | 1.2.1 | 数据库连接池 |
| Redis | 6.x | 缓存 + 店铺状态 |
| JWT (jjwt) | 0.9.1 | 双端 Token 认证 |
| WebSocket | - | 实时消息推送 |
| Knife4j | 3.0.2 | API 文档 (Swagger) |
| Aliyun OSS | 3.10.2 | 文件上传存储 |
| 微信支付 SDK | 0.4.8 | 微信 JSAPI 支付 |
| Apache POI | 3.16 | Excel 报表导出 |
| Lombok | 1.18.20 | 简化代码 |
| AspectJ | 1.9.4 | AOP 自动填充 |

---

## 项目结构

```
sky-take-out/
├── sky-common/          # 公共模块
│   └── java/com/sky/
│       ├── constant/    # 常量定义
│       ├── context/     # ThreadLocal 上下文
│       ├── enumeration/ # 枚举
│       ├── exception/   # 自定义异常 (12个)
│       ├── json/        # Jackson 序列化配置
│       ├── properties/  # 配置属性类
│       ├── result/      # 统一响应结果
│       └── utils/       # JWT/OSS/微信支付/HTTP 工具
│
├── sky-pojo/            # 领域模型模块
│   └── java/com/sky/
│       ├── entity/      # 实体类 (11个)
│       ├── dto/         # 数据传输对象 (21个)
│       └── vo/          # 视图对象 (17个)
│
└── sky-server/          # 服务端主模块
    └── java/com/sky/
        ├── SkyApplication.java   # 启动类
        ├── annotation/  # 自定义注解 (@AutoFill)
        ├── aspect/      # AOP 切面 (自动填充时间/用户)
        ├── config/      # 配置类 (MVC/Redis/OSS/WebSocket)
        ├── controller/
        │   ├── admin/   # 管理端接口 (9个)
        │   ├── user/    # 用户端接口 (8个)
        │   └── notify/  # 支付回调
        ├── handler/     # 全局异常处理
        ├── interceptor/ # JWT 拦截器
        ├── mapper/      # MyBatis Mapper
        ├── service/     # 业务服务
        ├── task/        # 定时任务
        └── websocket/   # WebSocket 服务端
```

---

## 功能清单

### 🖥️ 管理端 (`/admin`)

| 模块 | 功能 |
|------|------|
| **员工管理** | 登录/登出、新增、分页查询、启用/禁用、编辑 |
| **分类管理** | 菜品分类和套餐分类的增删改查、启用/禁用 |
| **菜品管理** | 菜品 CRUD、口味管理、批量删除、上架/下架、文件上传 |
| **套餐管理** | 套餐 CRUD、套餐菜品关联、上架/下架 |
| **订单管理** | 条件搜索、订单统计、查看详情、接单/拒单/取消/派送/完成 |
| **店铺管理** | 营业/打烊状态切换 |
| **数据报表** | 营业额统计、用户统计、订单统计、销量 Top10、Excel 导出 |
| **工作台** | 今日数据概览、订单/菜品/套餐概览 |

### 📱 用户端 (`/user`)

| 模块 | 功能 |
|------|------|
| **用户登录** | 微信小程序授权登录 |
| **菜品浏览** | 按分类查看菜品（Redis 缓存） |
| **套餐浏览** | 按分类查看套餐（Spring Cache 缓存） |
| **购物车** | 添加/减少/清空、查看列表 |
| **地址管理** | 地址增删改查、设置默认地址 |
| **订单管理** | 提交订单、微信支付、历史订单、取消、再来一单、催单提醒 |
| **店铺查询** | 查看店铺营业状态（公开接口） |

### ⚡ 特色特性

- **JWT 双端认证**：管理端和用户端独立 Token 体系
- **Redis 缓存**：菜品列表缓存、店铺状态缓存
- **WebSocket 实时推送**：新订单实时提醒
- **AOP 自动填充**：创建/更新时间、创建/更新人自动设置
- **定时任务**：超时订单自动取消（15分钟未支付）、派送中订单自动完成
- **微信支付回调**：支付成功异步通知处理

---

## 数据模型

```
Employee      — 员工
Category      — 分类（菜品/套餐）
Dish          — 菜品
DishFlavor    — 菜品口味
Setmeal       — 套餐
SetmealDish   — 套餐-菜品关联
Orders        — 订单
OrderDetail   — 订单明细
User          — 微信用户
AddressBook   — 用户地址
ShoppingCart  — 购物车
```

---

## 快速开始

### 环境要求

- JDK 8+
- Maven 3.6+
- MySQL 8.0+
- Redis 6.0+

### 1. 克隆项目

```bash
git clone https://github.com/05zddd/sky_take_out_plus.git
cd sky_take_out_plus
```

### 2. 数据库初始化

创建数据库：

```sql
CREATE DATABASE sky_take_out DEFAULT CHARACTER SET utf8mb4;
```

建表语句请参考课程资料中的 SQL 脚本，或联系项目维护者获取。

### 3. 配置应用

复制配置模板并填入你的实际配置：

```bash
cp sky-server/src/main/resources/application-dev.yml.example \
   sky-server/src/main/resources/application-dev.yml
```

编辑 `application-dev.yml`，将占位值替换为你的真实配置：
- 数据库用户名和密码
- Redis 连接信息
- 阿里云 OSS AccessKey
- 微信支付商户密钥

### 4. 启动

```bash
mvn clean package -DskipTests
cd sky-server
mvn spring-boot:run
```

或直接在 IDE 中运行 `com.sky.SkyApplication`。

启动成功后：

| 资源 | 地址 |
|------|------|
| API 文档 (Knife4j) | http://localhost:8080/doc.html |
| 管理端默认账号 | admin / 123456 |

---

## API 文档

项目集成 Knife4j（Swagger 增强），启动后在浏览器打开：

```
http://localhost:8080/doc.html
```

可直接在线调试管理端和用户端的所有接口。

---

## 许可证

本项目仅用于个人学习目的。

---

## 致谢

- [黑马程序员](https://www.itheima.com/) — 苍穹外卖课程
- Spring Boot / MyBatis / Redis 开源社区
