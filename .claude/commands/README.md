# Slash Commands

Slash commands 是 Claude Code 中的快捷指令，用于触发特定的工作流程。

## 可用命令

### /dev-docs

**用途**: 创建结构化的开发计划和任务文档

**使用场景**:
- 规划大型功能开发
- 需要持久化任务跟踪
- 避免 context 重置丢失信息

**示例**:
```
/dev-docs 实现 Twitter API 集成
/dev-docs 重构 Newsletter 调度系统
/dev-docs 添加 Grok AI 内容摘要功能
```

**生成的文件**:
```
dev/active/[task-name]/
├── [task-name]-plan.md      # 综合计划
├── [task-name]-context.md   # 关键文件和决策
└── [task-name]-tasks.md     # 任务清单（checkbox 格式）
```

**优点**:
- ✅ 结构化的任务管理
- ✅ Context 重置后仍可恢复
- ✅ 清晰的进度跟踪
- ✅ 风险评估和时间估算

---

### /dev-docs-update

**用途**: 在接近 context 限制前更新开发文档

**使用场景**:
- Token 使用接近上限
- 长时间开发会话
- 需要保存当前进度和决策

**示例**:
```
/dev-docs-update
/dev-docs-update 重点更新 Twitter 集成部分
```

**更新内容**:
- 当前实现状态
- 关键决策和修改的文件
- 已完成和待完成的任务
- 发现的问题和解决方案
- 下一步行动

**优点**:
- ✅ 无缝切换 context
- ✅ 保留重要决策
- ✅ 记录复杂问题的解决方案
- ✅ 交接给其他开发者

---

## 如何使用

在 Claude Code 中直接输入命令：

```
/dev-docs 你的任务描述
```

命令会自动展开并执行相应的工作流程。

## 自定义命令

你可以在 `.claude/commands/` 目录下创建自己的命令：

**文件格式**: `command-name.md`

**基本结构**:
```markdown
---
description: 命令的简短描述
argument-hint: 参数提示（可选）
---

命令的具体指令和工作流程...
```

**示例 - 创建测试命令**:

`.claude/commands/test-api.md`:
```markdown
---
description: 测试 API endpoints
argument-hint: endpoint path (e.g., /api/newsletters)
---

测试 API endpoint: $ARGUMENTS

1. 检查路由文件是否存在
2. 运行相关测试
3. 报告测试结果
```

使用: `/test-api /api/newsletters`

---

## Twitter Newsletter 项目建议的自定义命令

### 可以创建的命令

1. **/test-twitter-api** - 测试 Twitter API 集成
2. **/test-grok** - 测试 Grok AI 集成
3. **/test-cron** - 测试定时任务
4. **/test-email** - 测试邮件发送
5. **/create-migration** - 创建数据库迁移
6. **/deploy-check** - 部署前检查清单

需要我帮你创建这些自定义命令吗？

---

## 最佳实践

1. **使用 /dev-docs 规划复杂功能**
   - 在开始编码前创建计划
   - 分解成可管理的任务
   - 定期更新进度

2. **定期使用 /dev-docs-update**
   - 每当完成重要功能
   - Token 使用超过 50%
   - 准备结束会话时

3. **创建项目特定命令**
   - 重复性的工作流程
   - 多步骤的检查流程
   - 常用的测试场景

---

**提示**: Commands 是提高开发效率的利器，善用它们可以让你的工作流程更加流畅！
