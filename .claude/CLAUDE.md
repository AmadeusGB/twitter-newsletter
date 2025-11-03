# Twitter Newsletter Project - Claude Code 配置

## 项目概述

这是一个基于 Next.js 的 Twitter Newsletter 自动化服务，用于定时发送个性化的 Twitter 内容摘要邮件给用户。

**核心功能：**
- 用户通过前端配置 Newsletter 订阅（Twitter 用户、关键词、行业话题）
- 后端定时任务抓取 Twitter 内容
- 使用 Grok AI 进行内容摘要和分析
- 按用户本地时间发送定制化邮件

## 技术栈

### 前端
- **Next.js 14+** - App Router 架构
- **TypeScript** - 类型安全
- **Tailwind CSS** - 样式系统
- **shadcn/ui** - 组件库

### 后端
- **Next.js API Routes** - Serverless API
- **Prisma** - 数据库 ORM
- **PostgreSQL** - 主数据库
- **Node-cron** - 定时任务调度

### 外部服务
- **Twitter API v2** - 推文获取
- **Grok API** - AI 内容摘要
- **Resend/SendGrid** - 邮件发送

## 项目结构

```
twitter-newsletter/
├── app/
│   ├── api/              # API routes
│   │   ├── auth/         # 认证端点
│   │   ├── newsletters/  # Newsletter CRUD
│   │   ├── cron/         # 定时任务触发
│   │   └── webhooks/     # Webhook 处理
│   ├── dashboard/        # 用户仪表盘
│   └── (auth)/           # 认证页面
├── components/           # React 组件
├── lib/
│   ├── db/              # 数据库工具
│   ├── twitter/         # Twitter API 集成
│   ├── grok/            # Grok API 集成
│   ├── email/           # 邮件服务
│   └── scheduler/       # Cron 管理
├── prisma/
│   └── schema.prisma    # 数据库 Schema
└── types/               # TypeScript 类型定义
```

## 开发规范

### 代码组织

**API Routes (app/api/)**
- 使用 Next.js 14+ App Router 约定
- 每个路由文件导出 GET, POST, PUT, DELETE 等命名函数
- 使用 NextRequest 和 NextResponse

**组件 (components/)**
- 使用 shadcn/ui 组件作为基础
- 所有组件使用 TypeScript
- Props 使用 interface 定义

**工具库 (lib/)**
- 按功能模块组织（twitter/, grok/, email/, scheduler/）
- 每个模块导出清晰的 API
- 使用 async/await 处理异步操作

### 时间处理规范

**关键原则：内部使用 Unix 时间戳**

- Newsletter 的 `scheduleTime` 字段存储 **Unix 时间戳**（整数）
- 用户时区信息存储在 `User.timezone` 和 `Newsletter.timezone` 字段
- 前端接收用户本地时间，后端转换为 Unix 时间戳存储
- 定时任务触发时，根据时间戳和时区计算执行时间

**示例：**
```typescript
// 前端：用户选择本地时间 "每天 09:00"
const userLocalTime = "09:00";
const userTimezone = "Asia/Shanghai";

// 后端：转换为 Unix 时间戳（今天的 09:00）
const scheduleTime = convertToUnixTimestamp(userLocalTime, userTimezone);

// 存储到数据库
await prisma.newsletter.create({
  data: {
    scheduleTime,      // Unix 时间戳
    timezone: userTimezone,
    frequency: "daily"
  }
});
```

### 数据库规范

**Prisma Schema 设计：**
- 使用 `cuid()` 作为主键默认值
- 时间字段使用 `DateTime` 类型（Prisma 自动处理）
- 定时任务相关字段：
  - `scheduleTime: Int` - Unix 时间戳
  - `timezone: String` - 用户时区（如 "Asia/Shanghai"）
  - `frequency: String` - 频率（"daily", "weekly", "custom"）

### API 设计规范

**RESTful API 约定：**
- `POST /api/newsletters` - 创建 Newsletter
- `GET /api/newsletters` - 列表查询
- `GET /api/newsletters/[id]` - 详情查询
- `PUT /api/newsletters/[id]` - 更新
- `DELETE /api/newsletters/[id]` - 删除

**认证：**
- 使用 NextAuth.js 进行认证
- API routes 使用 middleware 或 getServerSession 验证

**Cron Jobs：**
- 通过 `/api/cron/process-newsletters` 触发
- 使用 `CRON_SECRET` 环境变量保护端点
- Header 验证：`Authorization: Bearer ${CRON_SECRET}`

### 外部 API 集成

**Twitter API:**
- 使用 Twitter API v2
- Bearer Token 认证
- 搜索推文、获取用户时间线
- 遵守 rate limit

**Grok AI:**
- 用于内容摘要和分析
- 提取关键见解
- 生成 Newsletter 内容

**邮件服务 (Resend/SendGrid):**
- 响应式邮件模板
- 个性化内容
- 退订管理

## 环境变量

```env
# Database
DATABASE_URL="postgresql://..."

# Authentication
NEXTAUTH_SECRET="..."
NEXTAUTH_URL="http://localhost:3000"

# Twitter API
TWITTER_API_KEY="..."
TWITTER_API_SECRET="..."
TWITTER_BEARER_TOKEN="..."

# Grok AI
GROK_API_KEY="..."
GROK_API_URL="..."

# Email Service
RESEND_API_KEY="..."
FROM_EMAIL="noreply@yourdomain.com"

# Cron Job Protection
CRON_SECRET="..."
```

## Claude Code 集成

### Skills
- **skill-developer** - 用于创建和管理自定义 skills

### Agents
- **code-architecture-reviewer** - 代码架构审查
- **documentation-architect** - 生成文档
- **auto-error-resolver** - 自动修复错误
- **code-refactor-master** - 重构助手

### Hooks
- **UserPromptSubmit** - 自动激活相关 skills
- **PostToolUse** - 跟踪已使用的 skills

## 开发工作流

### 本地开发
```bash
# 安装依赖
npm install

# 设置环境变量
cp .env.example .env.local

# 初始化数据库
npx prisma migrate dev

# 启动开发服务器
npm run dev
```

### 数据库迁移
```bash
# 创建迁移
npx prisma migrate dev --name description

# 应用迁移（生产）
npx prisma migrate deploy

# 生成 Prisma Client
npx prisma generate
```

### 测试 Cron Jobs
```bash
# 手动触发 cron job
curl -X POST http://localhost:3000/api/cron/process-newsletters \
  -H "Authorization: Bearer ${CRON_SECRET}"
```

## 安全考虑

- ✅ 使用环境变量保护敏感信息
- ✅ API routes 需要认证
- ✅ Cron endpoints 使用 secret 保护
- ✅ 输入验证和清理
- ✅ Rate limiting（防止滥用）
- ✅ CORS 配置
- ✅ SQL 注入防护（Prisma 自动处理）
- ✅ XSS 防护（React 自动转义）

## 部署

**推荐平台：Vercel**

```bash
# 安装 Vercel CLI
npm i -g vercel

# 部署
vercel --prod
```

**Cron Jobs 配置（vercel.json）：**
```json
{
  "crons": [{
    "path": "/api/cron/process-newsletters",
    "schedule": "*/15 * * * *"
  }]
}
```

## 常见问题

### 时区处理
Q: 如何确保用户收到邮件的时间是他们本地时间？
A: 后端在处理定时任务时，会根据存储的 Unix 时间戳和用户时区计算准确的发送时间。

### Grok API 限流
Q: 如何处理 Grok API 的 rate limit？
A: 实现队列系统，批量处理请求，使用指数退避重试。

### 邮件发送失败
Q: 邮件发送失败如何处理？
A: 记录到 Delivery 表，标记为 "failed"，实现重试机制。

---

**注意：** 这是项目初期配置，随着开发进展会持续更新。
