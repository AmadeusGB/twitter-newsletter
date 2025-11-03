# Development Documentation

这个目录用于存放开发过程中的计划、上下文和任务跟踪文档。

## 目录结构

```
dev/
├── active/              # 当前活跃的任务
│   └── [task-name]/     # 具体任务目录
│       ├── [task-name]-plan.md      # 综合计划
│       ├── [task-name]-context.md   # 关键上下文
│       └── [task-name]-tasks.md     # 任务清单
└── archive/             # 已完成的任务（归档）
```

## 使用方式

### 创建新任务

使用 `/dev-docs` 命令：

```
/dev-docs 实现 Twitter API 集成
```

这会自动创建一个任务目录和三个文档文件。

### 更新任务进度

在开发过程中：
1. 在 `[task-name]-tasks.md` 中标记完成的任务 ✅
2. 在 `[task-name]-context.md` 中记录关键决策
3. 使用 `/dev-docs-update` 保存会话上下文

### 归档完成的任务

任务完成后，将目录移动到 `archive/`：

```bash
mv dev/active/twitter-api-integration dev/archive/
```

## 文件说明

### [task-name]-plan.md

**内容**:
- 执行摘要
- 当前状态分析
- 未来状态设计
- 实施阶段
- 详细任务
- 风险评估
- 成功指标
- 时间估算

**用途**: 高层次的战略规划和架构设计

### [task-name]-context.md

**内容**:
- 关键文件列表
- 重要决策和理由
- 依赖关系
- 已知问题和解决方案
- 配置变更
- 环境变量

**用途**: 快速恢复上下文，理解为什么这样做

### [task-name]-tasks.md

**内容**:
- Checkbox 格式的任务清单
- 按优先级和阶段组织
- 每个任务的验收标准
- 依赖关系

**用途**: 日常进度跟踪，清晰的待办事项

## 最佳实践

### 1. 何时创建任务文档

✅ **应该创建**:
- 开发周期 > 1 天的功能
- 涉及多个文件/模块的修改
- 需要架构设计的功能
- 有复杂依赖关系的任务

❌ **不需要创建**:
- 简单的 bug 修复
- 单文件小改动
- 临时性的实验

### 2. 保持文档更新

- **每日结束前**: 更新任务清单和进度
- **重大决策后**: 记录到 context.md
- **Context 警告时**: 运行 `/dev-docs-update`
- **功能完成时**: 最终更新并归档

### 3. 写清晰的任务描述

**好的任务描述** ✅:
```markdown
- [ ] 实现 Twitter API 认证流程
  - 使用 Bearer Token 认证
  - 处理 rate limit 错误
  - 添加 token 刷新逻辑
  - 验收：成功获取 tweets
```

**不好的任务描述** ❌:
```markdown
- [ ] 做 Twitter 的东西
```

### 4. 利用文档恢复工作

Context 重置后：
1. 阅读 plan.md 了解整体目标
2. 查看 context.md 理解当前状态
3. 检查 tasks.md 找到下一步行动
4. 继续开发

## Twitter Newsletter 项目的典型任务

示例任务目录：

```
dev/active/
├── twitter-api-integration/      # Twitter API 集成
├── grok-ai-summarization/        # Grok AI 摘要功能
├── email-template-system/        # 邮件模板系统
├── cron-scheduler/               # 定时任务调度器
└── user-dashboard/               # 用户仪表盘
```

每个任务都包含完整的计划、上下文和任务清单。

## 团队协作

如果多人开发：
1. 任务目录提交到 Git
2. 每次 push 前更新文档
3. Pull 代码后先读文档
4. 关键决策在 context.md 中沟通

## 提示

- 📝 **文档是代码的一部分** - 认真维护
- 🔄 **定期更新** - 不要等到忘记了才写
- 🎯 **聚焦行动** - 任务要可执行，不要太抽象
- 💡 **记录"为什么"** - 代码说"是什么"，文档说"为什么"

---

**使用 `/dev-docs` 命令开始你的第一个任务吧！**
