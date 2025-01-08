# MetaTalk 多语言翻译应用

MetaTalk是一个基于Spring Boot + uni-app的多语言翻译应用,支持多端运行(H5/Android/iOS等)。

## 系统架构

### 整体架构
```
                                    【负载均衡】
                                         ↓
【移动端/网页】 → 【网关】 → 【服务发现(Nacos)】 → 【微服务集群】
                                         ↓
                                  【监控&告警系统】
```

### 微服务架构
```
                    【API网关】
                        ↓
     ┌─────────────────┼─────────────────┐
     ↓                 ↓                 ↓
【用户服务】      【翻译服务】        【系统服务】
     ↓                 ↓                 ↓
【用户数据库】    【翻译缓存】      【系统配置库】
```

### 数据流转图

1. 翻译请求流程
```
用户请求 → API网关 → 翻译服务 → 调用翻译API → 结果缓存 → 返回结果
```

2. 用户注册流程
```
注册请求 → 验证信息 → 发送验证码 → 信息入库 → 生成Token → 返回结果
```

## 技术栈

### 后端技术栈
- Spring Boot 2.1.1
- Dubbo + Nacos (微服务架构)
- Redis (缓存)
- RabbitMQ (消息队列)
- MyBatis-Plus (ORM框架)
- MySQL (数据库)
- JWT (认证)
- 阿里云服务 (OSS对象存储、短信服务等)
- Hutool、OkHttp等工具包

### 前端技术栈
- uni-app (跨平台开发框架)
- Vue.js
- uView UI组件库
- vue-i18n (国际化)

## 核心功能与实现

### 1. 用户系统
- 基于JWT的用户认证
- 手机号/邮箱双重验证
- 用户信息加密存储
- Token自动续期机制

### 2. 翻译功能
- 多语言实时翻译
- 翻译结果缓存
- 批量翻译处理
- 自定义词库支持

### 3. 消息队列
- 异步处理翻译请求
- 削峰填谷
- 任务重试机制
- 死信队列处理

## 代码结构

### 后端代码结构
```
src/main/java/com/wanlun/
├── common/              // 公共模块
│   ├── config/         // 配置类
│   ├── constant/       // 常量定义
│   └── utils/          // 工具类
├── user/               // 用户模块
│   ├── controller/     // 控制器
│   ├── service/        // 服务层
│   ├── dao/           // 数据访问层
│   └── entity/        // 实体类
├── translate/          // 翻译模块
│   ├── controller/     
│   ├── service/       
│   ├── dao/          
│   └── entity/       
└── system/             // 系统模块
    ├── controller/    
    ├── service/      
    ├── dao/         
    └── entity/      
```

### 前端代码结构
```
├── components/          // 公共组件
│   ├── common/         // 基础组件
│   └── business/       // 业务组件
├── pages/              // 页面文件
│   ├── user/          // 用户相关页面
│   ├── translate/     // 翻译相关页面
│   └── system/        // 系统设置页面
├── store/              // 状态管理
├── api/                // 接口定义
└── utils/              // 工具函数
```

## 数据库设计

### 用户表(user)
| 字段名 | 类型 | 说明 |
|--------|------|------|
| id | bigint | 主键 |
| username | varchar | 用户名 |
| password | varchar | 密码(加密) |
| phone | varchar | 手机号 |
| email | varchar | 邮箱 |
| status | tinyint | 状态 |
| create_time | datetime | 创建时间 |

### 翻译记录表(translate_record)
| 字段名 | 类型 | 说明 |
|--------|------|------|
| id | bigint | 主键 |
| user_id | bigint | 用户ID |
| source_text | text | 原文 |
| target_text | text | 译文 |
| source_lang | varchar | 源语言 |
| target_lang | varchar | 目标语言 |
| create_time | datetime | 创建时间 |

## API文档

### 用户接口
```
POST /api/user/register  // 用户注册
POST /api/user/login     // 用户登录
GET  /api/user/info      // 获取用户信息
PUT  /api/user/info      // 更新用户信息
```

### 翻译接口
```
POST /api/translate          // 翻译接口
GET  /api/translate/history  // 获取翻译历史
POST /api/translate/batch    // 批量翻译
```

## 开发环境要求

- JDK 8+
- MySQL 5.7+
- Redis 5.0+
- RabbitMQ 3.8+
- Node.js 12+

## 部署说明

### 1. 后端部署
- 导入sql目录下的数据库脚本
- 修改application.yml中的数据库、Redis等配置
- 使用Maven打包: `mvn clean package`
- 运行jar包: `java -jar metatalk.jar`

### 2. 前端部署
- 安装依赖: `npm install`
- 开发运行: `npm run dev`
- 打包: `npm run build`

### 3. 环境配置
```yaml
# 数据库配置
spring:
  datasource:
    url: jdbc:mysql://localhost:3306/metatalk
    username: root
    password: xxxxxx

# Redis配置
spring:
  redis:
    host: localhost
    port: 6379
    
# RabbitMQ配置
spring:
  rabbitmq:
    host: localhost
    port: 5672
    username: guest
    password: guest
```

## 性能优化

1. 数据库优化
- 合理使用索引
- 分库分表策略
- SQL语句优化

2. 缓存优化
- 多级缓存架构
- 缓存预热
- 缓存更新策略

3. 并发处理
- 线程池管理
- 限流策略
- 熔断降级

## 开发规范

1. 代码规范
- 遵循阿里巴巴Java开发手册
- 使用统一的代码格式化工具
- 必要的注释和文档

2. Git规范
- 分支管理策略
- Commit信息规范
- Code Review流程

## 测试说明

1. 单元测试
- Controller层测试
- Service层测试
- DAO层测试

2. 接口测试
- 接口功能测试
- 性能测试
- 安全测试

## 常见问题(FAQ)

1. Q: 如何处理翻译API限流?
   A: 使用令牌桶算法实现限流,同时配合消息队列实现请求削峰

2. Q: 如何确保用户数据安全?
   A: 使用加密存储、HTTPS传输、定期备份等措施

## 更新日志

### v1.0.0 (2023-12-01)
- 初始版本发布
- 实现基础翻译功能
- 用户系统上线

### v1.1.0 (2023-12-15)
- 添加批量翻译功能
- 优化翻译性能
- 修复已知问题

## 贡献指南

1. Fork 项目
2. 创建新分支: `git checkout -b feature/xxx`
3. 提交更改: `git commit -m 'Add xxx'`
4. 推送分支: `git push origin feature/xxx`
5. 提交Pull Request
# inlaw
