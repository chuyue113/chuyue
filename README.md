# Chuyue（初悦）— AI 人生模拟器

> 版本：v1.0.0 | 日期：2026-02-20 | 作者：悠然若妃 | 原创作品，盗版必究

Chuyue（初悦）是一款以 AI 为核心驱动力的文字冒险人生模拟器。玩家通过创建角色、选择剧本世界观，与 AI 共同书写专属的命运故事。游戏采用「三选一 + 自由输入」的交互机制，每个回合 AI 自动生成三个差异化剧情选项，玩家也可以自由输入任意行动，由 AI 实时响应并推进故事。

---

## 📚 文档索引

本仓库包含以下三份完整文档：

| 文件 | 说明 | 页数 |
|------|------|------|
| [01_技术文档.pdf](./01_技术文档.pdf) | 完整技术文档，涵盖系统架构、数据库设计、API 接口、前端状态管理、AI 引擎、主题系统、部署说明等 | 21 页 |
| [02_核心代码文档.pdf](./02_核心代码文档.pdf) | 核心代码完整文档，收录所有核心文件的完整代码及详细注释，供开发者参考与二次开发使用 | 34 页 |
| [Chuyue_完整文档.pdf](./Chuyue_完整文档.pdf) | 技术文档与核心代码文档的合集，包含全部内容 | 67 页 |

---

## 🚀 项目概述

本项目的核心设计理念是**沉浸感优先**：精心设计的三套国风主题（少女元氣、橘子汽水、高贵黑金）、流畅的粒子动画、细腻的 AI 叙事，共同构建出一个让玩家愿意反复回来的故事世界。

### 核心技术栈

| 层级 | 技术 | 版本 |
|------|------|------|
| 前端框架 | React | 19.2.1 |
| 后端框架 | Express | 4.21.2 |
| 全栈类型安全 | tRPC | 11.6.0 |
| 数据库 ORM | Drizzle ORM | 0.44.5 |
| 数据库 | MySQL / TiDB | — |
| 样式框架 | Tailwind CSS | 4.x |
| UI 组件库 | shadcn/ui + Radix UI | — |
| 数据请求 | TanStack Query | 5.90.2 |

---

## ⚙️ 快速开始

### 环境变量

```bash
DATABASE_URL=          # MySQL/TiDB 连接字符串
JWT_SECRET=            # Session Cookie 签名密钥
BUILT_IN_FORGE_API_KEY= # Manus 内置 LLM API Key
BUILT_IN_FORGE_API_URL= # Manus 内置 LLM 端点
VITE_APP_ID=           # Manus OAuth 应用 ID
OAUTH_SERVER_URL=      # Manus OAuth 后端地址
VITE_OAUTH_PORTAL_URL= # Manus 登录门户地址
```

### 启动命令

```bash
# 安装依赖
pnpm install

# 推送数据库 schema
pnpm db:push

# 开发模式
pnpm dev

# 生产构建
pnpm build

# 运行测试
pnpm test
```

---

## 🗂️ 技术文档目录

以下为 `01_技术文档.pdf` 的内容索引：

1. **项目概述** — 产品定位与核心差异化优势
2. **技术栈与依赖** — 核心框架、AI 功能库、开发工具链
3. **系统架构** — 客户端/服务端整体架构图与数据流说明
4. **目录结构** — 前端（`client/`）与后端（`server/`）完整文件树
5. **数据库设计** — 四张核心表（`users` / `game_accounts` / `game_saves` / `game_scripts`）的 Schema 定义
6. **后端 API 接口** — 所有 tRPC procedure 的类型、权限与参数说明（`auth` / `account` / `game` / `scripts` 路由）
7. **前端状态管理** — `GameContext`（`useReducer`）架构、`OnboardingStep` 流程、`PlayerProfile` 数据结构
8. **AI 引擎设计** — 双模式 LLM 调用（内置代理/外部 API）、开场生成、剧情推进、选项类型系统
9. **主题系统** — 三套主题（少女元氣/橘子汽水/高贵黑金）、CSS 变量体系、字体系统
10. **用户流程** — 新用户完整操作路径（注册 → 配置 → 角色创建 → 主游戏）
11. **多剧本系统** — 剧本切换逻辑与 `ScriptPage` 三种视图
12. **云端存档系统** — 自动存档机制、数据序列化、存档隔离安全设计
13. **账号认证系统** — Manus OAuth 与游戏账号双认证体系、密码安全（bcrypt）
14. **部署说明** — 环境变量配置、启动命令、测试覆盖情况

---

## 🧪 测试覆盖

| 测试文件 | 测试数 | 覆盖范围 |
|----------|--------|----------|
| `server/scripts.test.ts` | 6 | 剧本模板/类型/路由结构 |
| `server/auth.logout.test.ts` | 1 | 登出 Cookie 清除 |
