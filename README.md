# DataHub 多数据源管理系统

**中文** | [English](README.en.md)

DataHub 是面向研发、数据、集成与运维团队的一体化数据接入与数据服务平台。它将多类型数据源管理、数据集建模、开放 API 发布、凭证治理、数据迁移、配置导入导出和桌面端交付整合到同一个工作台中，适合建设内部数据服务平台、数据中台工具、项目交付客户端和单机数据管理工具。

## 界面预览

> 以下截图来自本地运行环境，展示数据已做匿名化处理。

| 数据源管理 | 数据集管理 |
| --- | --- |
| ![数据源管理](docs/images/datahub-datasource.png) | ![数据集管理](docs/images/datahub-dataset.png) |

| 开放 API 管理 | 工具箱 |
| --- | --- |
| ![开放 API 管理](docs/images/datahub-openapi.png) | ![工具箱](docs/images/datahub-toolbox.png) |

## 产品定位

DataHub 不是单一的数据库客户端，而是一套围绕“接入、编排、发布、治理、交付”设计的数据服务产品：

- **统一接入**：用一致的模型管理关系型数据库、NoSQL、搜索引擎、对象存储、文件传输、HTTP API、消息中间件等数据源。
- **统一编排**：将底层数据源封装为可复用数据集，支持 SQL、ES DSL、Mongo Pipeline、变量、字段映射、缓存和查询限制。
- **统一发布**：将数据集发布为开放 API，并提供版本、文档、AK/SK、白名单、限流和调用日志能力。
- **统一交付**：同时支持前后端分离部署和 Tauri 桌面端打包，可覆盖研发、内网、离线和单机交付场景。

## 核心能力

| 模块 | 能力 |
| --- | --- |
| 数据源管理 | 分组、连接测试、类型化配置、专用工作台、导入导出 |
| 数据浏览 | 表结构浏览、SQL 执行、分页查询、基础 DDL/DML 操作 |
| 文件与 HTTP 导入 | Excel、CSV、HTTP API 预解析、镜像入库、重导入、进度跟踪 |
| 数据集管理 | SQL / ES DSL / Mongo Pipeline、字段映射、变量、缓存 TTL |
| 开放 API | API 发布、版本管理、分页策略、OpenAPI / Markdown / Postman / curl 导出 |
| 凭证治理 | AK/SK、凭证分组、有效期、IP 白名单、限流覆盖、调用统计 |
| 数据迁移 | 迁移任务、表映射、字段映射、过滤条件、建表策略、执行日志 |
| 工具箱 | HTTP 调试、JSON、XML、Base64、二维码、Cron、URL、文本 Diff |
| 桌面交付 | Tauri 桌面壳、内置 JRE、本地 H2、单机运行、下载中心 |

## 支持的数据源

| 类型 | 数据源 |
| --- | --- |
| 关系型数据库 | MySQL、PostgreSQL、Oracle、达梦、人大金仓、GBase8s、Doris |
| NoSQL / 缓存 | Redis、MongoDB |
| 搜索引擎 | Elasticsearch、OpenSearch |
| 对象存储 | MinIO、阿里云 OSS、华为云 OBS、AWS S3 |
| 文件传输与协同 | FTP、SFTP、ZooKeeper |
| 消息中间件 | Kafka、RocketMQ |
| 导入型数据源 | Excel、CSV、HTTP API |

文件型和 HTTP 型数据源会先导入本地镜像库，再统一参与查询、数据集建模和 API 发布。

## 典型流程

```text
接入数据源
    ↓
创建数据集
    ↓
配置字段、变量、缓存和查询规则
    ↓
发布开放 API
    ↓
发放 AK/SK、导出文档、观察调用日志
```

在同一工作台内，还可以继续完成跨源迁移、配置导入导出、HTTP 调试和桌面端离线交付。

## 系统架构

```text
┌─────────────────────────────────────────────────────────────┐
│                         管理端 UI                            │
│        Vue 3 + TypeScript + Vite + Element Plus             │
│                    Web / Tauri Desktop                      │
└────────────────────────────┬────────────────────────────────┘
                             │ REST / SSE / WebSocket
┌────────────────────────────▼────────────────────────────────┐
│                     Spring Boot 服务层                       │
│  数据源接入 | 数据集编排 | API 发布 | 迁移引擎 | 系统配置       │
└───────────────┬──────────────┬──────────────┬───────────────┘
                │              │              │
        元数据存储        文件 / HTTP 镜像       外部业务数据源
      PostgreSQL / H2       backend-data/       DB / ES / MQ / OSS
```




## 注意事项

- 部分商业数据库驱动可能依赖企业制品库或本地 Maven 缓存，首次构建前需要确认依赖来源。
- 服务端模式使用 PostgreSQL，桌面模式使用 H2 文件库。
- `backend/src/main/resources/static/` 是前端构建产物目录，不建议手工维护。
- 下载中心等能力依赖 Tauri 环境，浏览器模式下只提供基础 Web 能力。
