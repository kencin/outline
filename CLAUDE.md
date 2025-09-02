# CLAUDE.md

此文件为 Claude Code (claude.ai/code) 在此代码仓库中工作时提供指导。

## Claude code/Ai Agent 生成代码前约束
1. 此仓库为一个 fork 仓库，主要目的是解决原始仓库不支持Cloudflare R2存储的问题
2. 上述解决存储问题的分支为：feat-r2
3. 存储问题已经在上述分支完成了，除非我要求你修复或者更改，你不要动任何代码

## 开发命令

### 基本命令
- `yarn dev:watch` - 启动开发模式，同时监听后端和前端的文件变化
- `yarn dev` - 仅启动后端（运行多个服务：定时任务、协作、WebSocket、管理、Web、Worker）
- `yarn dev:backend` - 启动后端并监听文件变化（排除测试和某些目录）
- `yarn vite:dev` - 仅启动前端开发服务器
- `yarn build` - 构建整个应用程序（清理、Vite、国际化、服务器）
- `yarn start` - 启动生产服务器

### 测试
- `yarn test` - 运行所有测试
- `yarn test:app` - 仅运行前端测试
- `yarn test:server` - 仅运行后端测试
- `yarn test:shared` - 仅运行共享代码测试
- `make test` - 运行所有测试并设置数据库（删除、创建、迁移测试数据库）
- `make watch` - 在监听模式下运行测试，并设置数据库

### 代码检查和格式化
- `yarn lint` - 使用 oxlint 检查代码
- `yarn lint:changed` - 仅检查更改的文件
- `yarn format` - 使用 Prettier 格式化代码
- `yarn format:check` - 检查代码格式

### 数据库
- `yarn db:create` - 创建数据库
- `yarn db:migrate` - 运行数据库迁移
- `yarn db:rollback` - 回滚最后一次迁移
- `yarn db:reset` - 重置数据库（删除、创建、迁移）
- `yarn db:create-migration` - 创建新的迁移文件

### Docker（开发环境）
- `make up` - 使用 Docker 启动开发环境（Redis、PostgreSQL）
- `make destroy` - 停止并移除 Docker 容器

## 架构概览

Outline 是一个使用 React 和 Node.js 构建的协作式知识库。代码库是一个单体仓库，包含前端（React）、后端（Koa/Node.js）和共享代码。

### 前端结构 (`app/`)
- **React 17** 配合 **MobX** 进行状态管理
- **Styled Components** 用于样式
- **Vite** 用于构建和开发
- **React Router** 用于路由
- **ProseMirror** 用于富文本编辑器（来自 `shared/editor/`）

主要目录：
- `actions/` - 可重用的操作（导航、创建等）
- `components/` - 可重用的 React 组件
- `scenes/` - 完整页面视图
- `models/` - MobX 状态模型
- `stores/` - 模型集合及其获取逻辑
- `hooks/` - 自定义 React 钩子
- `routes/` - 路由定义，支持异步加载

### 后端结构 (`server/`)
- **Koa** Web 框架
- **Sequelize** ORM 配合 PostgreSQL
- **Redis** 配合 **Bull** 处理队列
- **Socket.io** 用于实时功能
- **Passport.js** 用于身份验证

主要目录：
- `routes/` - API 和身份验证路由
- `models/` - Sequelize 数据库模型
- `policies/` - 授权逻辑（cancan 风格）
- `presenters/` - 数据库模型的 JSON 序列化器（后端到前端的接口）
- `commands/` - 复杂的多模型操作
- `queues/processors/` - 后台任务处理器
- `services/` - 应用程序服务启动器

### 共享代码 (`shared/`)
- `editor/` - 基于 ProseMirror 的富文本编辑器
- `utils/` - 前端和后端都使用的工具函数
- `i18n/` - 国际化和翻译文件
- `styles/` - 共享样式和主题

## 关键技术和模式

### 身份验证
- 多种提供商：Google、Slack、Azure AD、OIDC
- 基于 JWT 的会话
- 基于团队的访问控制

### 编辑器
- 使用 Y.js 进行实时协作编辑
- ProseMirror 处理文档结构
- 支持 Markdown、代码块、嵌入内容、表格

### 数据库
- PostgreSQL 配合 Sequelize ORM
- 基于迁移的架构变更
- 大多数模型使用软删除

### 测试
- Jest 多项目配置（app、server、shared）
- jsdom 环境用于前端测试
- Node 环境用于后端测试
- 集成测试需要数据库设置/清理

## 环境配置

环境变量从 `.env` 文件加载，按优先级顺序：
1. `.env.test`（当 NODE_ENV=test 时）
2. `.env.development`（当 NODE_ENV=development 时）
3. `.env.local`（在本地开发时）
4. `.env`（基础配置）

主要环境变量包括：
- 数据库连接（PGHOST、PGUSER、PGPASSWORD 等）
- Redis 连接（REDIS_URL、REDIS_TLS）
- 外部服务密钥（AWS、Google、Slack 等）
- 应用程序配置（URL、PORT、FORCE_HTTPS）

## 开发提示

- 使用 `yarn dev:watch` 进行全栈开发和热重载
- 后端运行多个服务 - 检查日志以了解特定服务的错误
- 测试需要数据库设置 - 使用 `make test` 进行干净的测试运行
- 服务器启动前必须运行迁移
- 编辑器很复杂 - 添加功能时查看现有的扩展
- 以后构建docker镜像时，都跳过环境依赖检查