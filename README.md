# RAGent AI - 智能文档问答与知识库系统


<p align="center">
  <a href="#-项目简介">项目简介</a> •
  <a href="#-核心特性">核心特性</a> •
  <a href="#-技术架构">技术架构</a> •
  <a href="#-快速开始">快速开始</a> •
  <a href="#-项目结构">项目结构</a> •
  <a href="#-文档">文档</a>
</p>

---

## 项目简介

**RAGent AI**（RAG综合智能体）是一个基于 **Spring Boot 3** + **React 18** 构建的企业级智能文档处理与检索问答系统。系统深度融合了 RAG（检索增强生成）技术，支持多通道检索、意图识别、会话记忆、深度思考等高级功能，为企业提供精准、可溯源的知识问答服务。

### 主要能力

- **智能问答**：基于向量检索 + LLM 生成，提供精准的知识问答
- **知识库管理**：支持多知识库、多文档的 ingestion 与 chunk 管理
- **意图识别**：通过意图树实现问题分类与定向检索
- **会话记忆**：支持多轮对话、会话摘要、历史消息回溯
- **多通道检索**：向量全局检索 + 意图定向检索 + 后置处理器链（去重、Rerank）
- **MCP 集成**：支持 Model Context Protocol，扩展外部工具能力
- **RAG 链路追踪**：完整的检索-生成链路可视化追踪

---

## 核心特性

### RAG 核心能力

| 特性 | 说明 |
|------|------|
| **多通道检索** | 向量全局检索 + 意图定向检索，条件触发兜底机制 |
| **后置处理器链** | 去重、Rerank 重排序，提升检索质量 |
| **问题重写** | 基于 LLM 的多问题改写，优化检索效果 |
| **意图识别** | 树形意图分类，支持定向检索与引导提示 |
| **会话记忆** | 自动摘要 + 历史轮次保留，支持长对话上下文 |
| **深度思考** | 支持推理过程展示（thinking content）|

### 工程特性

| 特性 | 说明 |
|------|------|
| **向量数据库双支持** | Milvus / PostgreSQL pgvector 可切换 |
| **文档解析** | 支持 PDF、DOC、DOCX、Markdown 等格式（Apache Tika）|
| **对象存储** | S3 兼容存储（AWS S3 / MinIO / RustFS）|
| **消息队列** | RocketMQ 异步处理文档 ingestion 任务 |
| **限流控制** | 基于 Redis 的分布式信号量限流 |
| **链路追踪** | 完整的 RAG 检索-生成链路记录与可视化 |

---

## 技术架构

### 系统架构图

```
┌─────────────────────────────────────────────────────────────┐
│                        前端层 (Frontend)                      │
│  React 18 + TypeScript + Tailwind CSS + Vite               │
│  ├─ 用户聊天界面                                           │
│  ├─ 管理后台（知识库 / 文档 / 意图树 / 设置 / 追踪）        │
│  └─ 可视化图表与数据面板                                    │
└─────────────────────────────────────────────────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────┐
│                      API 网关层 (Bootstrap)                   │
│  Spring Boot 3.5 + Java 17                                 │
│  ├─ RESTful API（聊天 / 知识库 / 管理 / 追踪）              │
│  ├─ SSE 流式响应                                           │
│  └─ MCP Server 接口                                        │
└─────────────────────────────────────────────────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────┐
│                      RAG 核心引擎                            │
│  ├─ 意图识别（Intent Tree）                                 │
│  ├─ 多通道检索引擎（VectorGlobal / IntentDirected）         │
│  ├─ 后置处理器链（Deduplication / Rerank）                  │
│  ├─ 会话记忆与摘要管理                                       │
│  └─ 提示词模板与上下文格式化                                 │
└─────────────────────────────────────────────────────────────┘
                              │
              ┌───────────────┼───────────────┐
              ▼               ▼               ▼
┌─────────────────┐ ┌─────────────────┐ ┌─────────────────┐
│   向量存储        │ │   关系型数据库    │ │   消息队列       │
│  Milvus /       │ │  PostgreSQL     │ │  RocketMQ       │
│  pgvector       │ │  (业务数据)      │ │  (异步任务)      │
└─────────────────┘ └─────────────────┘ └─────────────────┘
```

### 多通道检索架构

```
用户问题
    ↓
【问题重写】
    ↓
【多通道并行检索】
    ├─→ IntentDirectedSearchChannel（意图定向检索，始终执行）
    └─→ VectorGlobalSearchChannel（向量全局检索，条件触发）
    ↓
【后置处理器链】
    ├─→ DeduplicationPostProcessor（去重）
    └─→ RerankPostProcessor（重排序）
    ↓
【上下文格式化】→ LLM 生成答案
```

---

## 技术栈

### 后端

| 技术 | 版本 | 用途 |
|------|------|------|
| Spring Boot | 3.5.7 | 核心框架 |
| Java | 17 | 编程语言 |
| MyBatis-Plus | 3.5.14 | ORM 框架 |
| PostgreSQL | - | 关系型数据库 |
| pgvector | 0.1.6 | PostgreSQL 向量扩展 |
| Milvus SDK | 2.6.6 | 向量数据库（可选）|
| Redis | - | 缓存 / 限流 / 会话 |
| RocketMQ | 5.2.0 | 消息队列 |
| Apache Tika | 3.2.3 | 文档解析 |
| AWS S3 SDK | 2.40.2 | 对象存储 |
| SA-Token | 1.43.0 | 认证授权 |
| Redisson | 4.0.0 | Redis 客户端 |
| MCP SDK | 1.1.2 | Model Context Protocol |

### 前端

| 技术 | 版本 | 用途 |
|------|------|------|
| React | 18.3 | UI 框架 |
| TypeScript | 5.5 | 类型系统 |
| Vite | 5.4 | 构建工具 |
| Tailwind CSS | 3.4 | CSS 框架 |
| Radix UI | - | 无头 UI 组件 |
| Zustand | 4.5 | 状态管理 |
| React Router | 6.26 | 路由管理 |
| Axios | 1.7 | HTTP 客户端 |
| Recharts | 3.7 | 图表库 |

---

## 快速开始

### 环境要求

- JDK 17+
- Maven 3.8+
- Node.js 18+
- PostgreSQL 14+（需安装 pgvector 扩展）
- Redis 6+
- RocketMQ 5.2+
- Milvus 2.6+（可选，可用 pgvector 替代）

### 1. 克隆项目

```bash
git clone <repository-url>
cd ragent-ai
```

### 2. 启动基础设施

使用 Docker Compose 启动依赖服务：

```bash
# 启动 Milvus + RustFS（对象存储）
docker compose -f resources/docker/milvus-stack-2.6.6.compose.yaml up -d

# 启动 RocketMQ
docker compose -f resources/docker/rocketmq-stack-5.2.0.compose.yaml up -d

# 启动 PostgreSQL 和 Redis（请自行准备或使用现有实例）
```

### 3. 初始化数据库

```bash
# 连接 PostgreSQL 并执行初始化脚本
psql -U postgres -d ragent -f resources/database/schema_pg.sql
psql -U postgres -d ragent -f resources/database/init_data_pg.sql
```

### 4. 配置应用

编辑 `bootstrap/src/main/resources/application.yaml`：

```yaml
spring:
  datasource:
    url: jdbc:postgresql://127.0.0.1:5432/ragent
    username: postgres
    password: your_password

  data:
    redis:
      host: 127.0.0.1
      port: 6379
      password: your_password

rocketmq:
  name-server: 127.0.0.1:9876

milvus:
  uri: http://localhost:19530

rag:
  vector:
    type: pg  # 可选: milvus / pg
```

### 5. 启动后端

```bash
# 编译并启动主服务
./mvnw clean install -DskipTests
./mvnw spring-boot:run -pl bootstrap

# 启动 MCP Server（可选）
./mvnw spring-boot:run -pl mcp-server
```

后端服务默认运行在 `http://localhost:9090/api/ragent`

### 6. 启动前端

```bash
cd frontend
npm install
npm run dev
```

前端开发服务器运行在 `http://localhost:5173`

### 7. 访问系统

- **用户端**：http://localhost:5173
- **管理后台**：http://localhost:5173（登录后根据角色自动跳转）
- **默认账号**：`admin / admin`

---

## 项目结构

```
ragent-ai/
├── bootstrap/                    # 主应用模块（Spring Boot）
│   ├── src/main/java/
│   │   └── com/nageoffer/ai/ragent/
│   │       ├── RagentApplication.java
│   │       ├── admin/            # 管理后台服务
│   │       ├── ingestion/        # 文档摄入管道
│   │       ├── knowledge/        # 知识库管理
│   │       ├── rag/              # RAG 核心引擎
│   │       │   ├── controller/   # API 接口
│   │       │   ├── core/         # 核心逻辑
│   │       │   │   ├── intent/       # 意图识别
│   │       │   │   ├── retrieve/     # 检索引擎
│   │       │   │   ├── memory/       # 会话记忆
│   │       │   │   ├── prompt/       # 提示词模板
│   │       │   │   └── mcp/          # MCP 集成
│   │       │   └── service/      # 业务服务
│   │       └── user/             # 用户认证
│   └── src/main/resources/
│       ├── application.yaml
│       ├── prompt/               # 提示词模板文件
│       └── lua/                  # Redis Lua 脚本
│
├── framework/                    # 公共框架模块
│   └── src/main/resources/lua/   # 雪花算法 Lua 脚本
│
├── infra-ai/                     # AI 基础设施模块
│
├── mcp-server/                   # MCP 服务模块
│   └── src/main/java/
│       └── McpServerApplication.java
│
├── frontend/                     # 前端应用（React + Vite）
│   ├── src/
│   │   ├── components/           # UI 组件
│   │   ├── pages/                # 页面
│   │   │   ├── admin/            # 管理后台页面
│   │   │   ├── ChatPage.tsx      # 聊天页面
│   │   │   └── LoginPage.tsx     # 登录页面
│   │   ├── services/             # API 服务
│   │   ├── stores/               # 状态管理
│   │   └── hooks/                # 自定义 Hooks
│   ├── package.json
│   └── vite.config.ts
│
├── resources/                    # 项目资源
│   ├── database/                 # 数据库脚本
│   │   ├── schema_pg.sql         # PostgreSQL 建表脚本
│   │   └── init_data_pg.sql      # 初始化数据
│   ├── docker/                   # Docker Compose 配置
│   │   ├── milvus-stack-2.6.6.compose.yaml
│   │   └── rocketmq-stack-5.2.0.compose.yaml
│   └── docs/                     # 示例知识库文档
│
├── docs/                         # 项目文档
│   ├── quick-start.md            # 快速开始
│   ├── multi-channel-retrieval.md # 多通道检索架构
│   └── refactoring-summary.md    # 重构总结
│
├── assets/                       # 文档图片资源
├── pom.xml                       # Maven 父 POM
└── README.md                     # 本文件
```

---

## 核心模块说明

### RAG 引擎 (`rag/core`)

| 模块 | 说明 |
|------|------|
| `intent` | 意图分类器、意图解析器、意图树管理 |
| `retrieve` | 多通道检索引擎、Milvus/Pg 检索服务 |
| `memory` | 会话记忆存储、自动摘要生成 |
| `prompt` | 提示词模板加载、上下文格式化 |
| `rewrite` | 查询重写、术语映射 |
| `mcp` | MCP 参数提取、工具调用 |

### 管理后台 (`admin`)

| 功能 | 说明 |
|------|------|
| 仪表盘 | 系统概览、统计图表 |
| 知识库 | 知识库 CRUD、Chunk 管理、文档管理 |
| 意图树 | 意图分类树编辑、示例问题管理 |
| 摄入管道 | 文档上传、解析任务监控 |
| 链路追踪 | RAG 检索-生成链路可视化 |
| 系统设置 | 模型配置、主题设置、查询术语映射 |

---

## 文档

- [快速开始指南](docs/quick-start.md)
- [多通道检索架构](docs/multi-channel-retrieval.md)
- [重构总结](docs/refactoring-summary.md)
- [PDF 摄入示例](docs/examples/pdf-ingestion-example.md)

---

## 许可证

本项目基于 [Apache License 2.0](LICENSE) 开源协议。

---

<p align="center">
  Made with ❤️ by Nageoffer AI Team
</p>
