# Claude Code 高效开发指南

> 从原理角度结合实践，全面掌握 Claude Code 的使用技巧

---

## 🚀 5 分钟快速入门

### 安装

```bash
npm install -g @anthropic-ai/claude-code
```

### 第一次使用

```bash
cd your-project
claude
```

### 最常用的 5 个操作

```bash
# 1. 理解项目
> 给我一个这个项目的概览

# 2. 修改代码
> 修复 @src/api.ts 中的类型错误

# 3. 运行命令
> 运行测试并修复失败的用例

# 4. 提交代码
> 创建一个 commit，描述刚才的改动

# 5. 查看成本
> /cost
```

### 记住这 3 个快捷键

| 快捷键 | 用途 |
|--------|------|
| `Esc+Esc` | 撤销代码改动 |
| `Ctrl+C` | 取消当前操作 |
| `Shift+Tab` | 切换权限模式 |

### 新手推荐配置

创建 `CLAUDE.md` 在项目根目录：

```markdown
# 项目简介
这是一个 [描述]，使用 [技术栈]

# 常用命令
- npm run dev
- npm run test
```

---

## 📍 场景索引：我想要...

| 我想要... | 查看章节 |
|----------|---------|
| **快速上手** | [5分钟快速入门](#-5-分钟快速入门) |
| **理解陌生代码库** | [迭代式探索](#73-迭代式探索) |
| **让 Claude 记住项目约定** | [五层内存体系](#21-五层内存体系) |
| **减少 Token 消耗** | [Token 优化策略](#22-token-优化策略)、[成本优化](#十五成本优化策略) |
| **自动格式化代码** | [Hooks 自动化](#三hooks-自动化) |
| **连接 GitHub/数据库** | [MCP Servers](#四mcp-servers-扩展能力) |
| **保护敏感文件** | [权限管理](#五权限管理) |
| **处理复杂任务** | [提示词技巧](#七提示词技巧)、[人机协作](#十八人机协作心智模型) |
| **团队一起用** | [团队协作模式](#十七团队协作模式) |
| **使用自动记忆** | [自动记忆系统](#23-自动记忆系统-auto-memory) |
| **使用 Skills** | [Skills 系统](#十一skills-系统) |
| **管理任务列表** | [任务管理系统](#103-任务管理系统-task-system) |
| **快速输出模式** | [Fast Mode](#64-fast-mode) |
| **多代理协作** | [Agent Teams](#227-agent-teams-实验性) |
| **转移会话到 Web** | [/teleport 会话转移](#126-teleport-会话转移) |
| **Claude 出错了** | [常见问题与疑难排解](#十四常见问题与疑难排解) |
| **控制成本** | [成本优化策略](#十五成本优化策略) |
| **与 IDE 配合** | [IDE 与工具链集成](#十六ide-与工具链集成) |

---

## 目录

**基础篇**
1. [核心工作原理](#一核心工作原理)
2. [上下文管理与 Token 优化](#二上下文管理与-token-优化)
3. [Hooks 自动化](#三hooks-自动化)
4. [MCP Servers 扩展能力](#四mcp-servers-扩展能力)
5. [权限管理](#五权限管理)

**实践篇**
6. [高效交互技巧](#六高效交互技巧)
7. [提示词技巧](#七提示词技巧)
8. [实战工作流](#八实战工作流)
9. [配置示例](#九配置示例)

**进阶篇**
10. [CLI 参数与斜杠命令速查](#十cli-参数与斜杠命令速查)
11. [Skills 系统](#十一skills-系统)
12. [会话管理](#十二会话管理)
13. [深入理解 Agent 架构](#十三深入理解-agent-架构)

**专题篇**
14. [常见问题与疑难排解](#十四常见问题与疑难排解)
15. [成本优化策略](#十五成本优化策略)
16. [IDE 与工具链集成](#十六ide-与工具链集成)
17. [团队协作模式](#十七团队协作模式)
18. [人机协作心智模型](#十八人机协作心智模型)
19. [反模式：避免这些错误](#十九反模式避免这些错误)
20. [角色专属指南](#二十角色专属指南)
21. [提示词模板库](#二十一提示词模板库)
22. [进阶主题](#二十二进阶主题)

---

## 一、核心工作原理

### 1.1 Agentic Coding 机制

Claude Code 是一个 **主动式代码助手**，区别于传统的"问答式"AI，它能够：

- **主动决策**：不仅给建议，还能直接执行编辑、运行命令、创建提交
- **工具调用**：通过 Tool Use API 操作文件系统、执行命令、搜索代码
- **迭代推理**：根据执行结果自动调整策略，直到任务完成

### 1.2 工作流程

```
用户提示 → Claude 分析意图 → 选择工具 → 执行操作 → 获取结果 → 迭代决策 → 完成任务
```

### 1.3 核心工具能力

| 工具 | 功能 | 典型场景 |
|------|------|---------|
| `Read` | 读取文件 | 查看代码、配置文件 |
| `Edit` | 编辑文件 | 修改代码、修复 bug |
| `Write` | 创建文件 | 新建组件、配置 |
| `Bash` | 执行命令 | 运行测试、git 操作、构建 |
| `Grep` | 内容搜索 | 查找函数定义、引用 |
| `Glob` | 文件搜索 | 查找特定类型文件 |
| `WebFetch` | 获取网页 | 查阅文档、API 参考 |
| `WebSearch` | 网络搜索 | 查找解决方案 |
| `TaskCreate` | 创建任务 | 规划多步骤工作 |
| `TaskUpdate` | 更新任务 | 标记进度、设置依赖 |
| `TaskGet` | 获取任务 | 查看任务详情 |
| `TaskList` | 列出任务 | 查看所有任务状态 |
| `Skill` | 执行技能 | 调用自定义 Skills |

---

## 二、上下文管理与 Token 优化

### 2.1 五层内存体系

Claude Code 使用分层的记忆系统，优先级从高到低：

```
┌─────────────────────────────────────────────────────────┐
│ 1. 项目内存: ./CLAUDE.md 或 ./.claude/CLAUDE.md        │  ← 团队共享，提交到 git
├─────────────────────────────────────────────────────────┤
│ 2. 项目规则: ./.claude/rules/*.md                      │  ← 模块化指令
├─────────────────────────────────────────────────────────┤
│ 3. 自动记忆: ~/.claude/projects/<project>/memory/      │  ← 自动保存，个人项目上下文
├─────────────────────────────────────────────────────────┤
│ 4. 用户内存: ~/.claude/CLAUDE.md                       │  ← 个人全局设置
├─────────────────────────────────────────────────────────┤
│ 5. 项目本地: ./CLAUDE.local.md                         │  ← 个人笔记，不提交
└─────────────────────────────────────────────────────────┘
```

**原理**：Claude 每次对话开始时自动加载这些文件，相当于"长期记忆"，避免重复解释项目背景。

### 2.2 Token 优化策略

#### 使用 `@` 直接引用文件

```bash
# 高效 ✓ — 直接定位，节省搜索 token
> 解释 @src/utils/auth.js 的逻辑

# 低效 ✗ — 需要搜索和探索
> 帮我找到 auth 相关的代码并解释
```

#### 思考模式控制

```bash
# 复杂问题启用深度思考
> ultrathink: 设计这个分布式缓存的最佳方案

# 环境变量控制思考预算
export MAX_THINKING_TOKENS=8000  # 默认 31,999

# 全局启用
/config  # 选择 "Enable extended thinking"
```

#### 查看 Token 消耗

```bash
/cost  # 显示当前会话的 token 统计
```

### 2.3 自动记忆系统 (Auto Memory)

Claude Code 会自动在 `~/.claude/projects/<project>/memory/MEMORY.md` 中保存项目上下文，跨会话持久化。

#### 与手动 CLAUDE.md 的区别

| 特性 | CLAUDE.md | Auto Memory |
|------|-----------|-------------|
| 管理方式 | 手动维护 | Claude 自动写入 |
| 作用范围 | 团队共享（提交到 git） | 个人（本地存储） |
| 内容类型 | 项目规范、命令、约定 | 工作模式、调试经验、偏好 |
| 生效方式 | 每次会话自动加载 | 每次会话自动加载到系统提示 |

#### 常见用法

```bash
# 主动让 Claude 记住偏好
> 记住：这个项目始终使用 pnpm，不要用 npm

# Claude 会自动保存到 MEMORY.md

# 查看自动记忆
> 看看你记住了什么关于这个项目的内容

# 让 Claude 忘记某个记忆
> 忘记关于使用 npm 的记忆
```

#### 最佳实践

- **团队共享的规范** → 写入 `CLAUDE.md`（提交到 git）
- **个人工作习惯** → 让 Auto Memory 自动记录
- **MEMORY.md 前 200 行** 会加入系统提示，保持简洁
- 可以创建子文件（如 `debugging.md`、`patterns.md`）存储详细笔记

---

## 三、Hooks 自动化

### 3.1 Hook 事件生命周期

```
Session开始 ──→ UserPromptSubmit ──→ PreToolUse ──→ 工具执行 ──→ PostToolUse ──→ Stop
     │                                    │              │              │
     ▼                                    ▼              ▼              ▼
 SessionStart                      可阻止操作    PostToolUseFailure  可触发后处理
                                                                       │
                                                                       ▼
                                                              PermissionRequest
                                                              Notification
                                                              TaskCompleted
                                                              PreCompact
                                                              SessionEnd
```

#### 完整事件列表（14 个）

| 事件 | 触发时机 | 用途 |
|------|---------|------|
| `SessionStart` | 会话开始 | 初始化环境 |
| `SessionEnd` | 会话结束 | 清理资源、保存状态 |
| `UserPromptSubmit` | 用户发送提示 | 预处理输入 |
| `PreToolUse` | 工具执行前 | 阻止或修改操作 |
| `PostToolUse` | 工具执行后 | 后处理（格式化等） |
| `PostToolUseFailure` | 工具执行失败后 | 错误处理、告警 |
| `PermissionRequest` | 权限请求时 | 自定义权限逻辑 |
| `Notification` | 通知事件 | 发送外部通知 |
| `SubagentStart` | 子代理启动 | 监控子代理 |
| `SubagentStop` | 子代理停止 | 清理子代理资源 |
| `TeammateIdle` | 队友代理空闲 | Agent Teams 协调 |
| `TaskCompleted` | 任务完成 | 触发后续流程 |
| `PreCompact` | 上下文压缩前 | 保存关键信息 |
| `Stop` | Claude 停止响应 | 最终处理 |

### 3.2 Hook 类型

#### command hook（默认）

执行 shell 命令，返回码控制流程：

- `0` = 允许继续
- `2` = 阻止操作

```json
{
  "type": "command",
  "command": "echo 'hello'"
}
```

#### prompt hook

将内容发送到模型进行评估，模型决定是否继续：

```json
{
  "type": "prompt",
  "prompt": "检查这个文件修改是否符合项目规范，如果不符合请阻止并说明原因"
}
```

#### agent hook

生成一个子代理来执行复杂逻辑：

```json
{
  "type": "agent",
  "prompt": "分析这个工具调用的结果，如果发现测试失败则自动修复"
}
```

#### async hook

异步非阻塞执行，不等待结果：

```json
{
  "type": "command",
  "command": "curl -X POST https://slack.webhook/... -d '{\"text\": \"Claude 完成了任务\"}'",
  "async": true
}
```

### 3.3 配置文件位置

- 项目级：`.claude/settings.json`
- 用户级：`~/.claude/settings.json`

### 3.4 实用 Hook 示例

#### 自动格式化（保存后自动 prettier）

```json
{
  "hooks": {
    "PostToolUse": [
      {
        "matcher": "Edit|Write",
        "hooks": [
          {
            "type": "command",
            "command": "file_path=$(jq -r '.tool_input.file_path'); if [[ $file_path == *.ts ]] || [[ $file_path == *.tsx ]]; then npx prettier --write \"$file_path\" 2>/dev/null; fi"
          }
        ]
      }
    ]
  }
}
```

#### 敏感文件保护（阻止修改 .env）

```json
{
  "hooks": {
    "PreToolUse": [
      {
        "matcher": "Edit|Write",
        "hooks": [
          {
            "type": "command",
            "command": "jq -r '.tool_input.file_path' | grep -qE '\\.(env|secret|key)' && exit 2 || exit 0"
          }
        ]
      }
    ]
  }
}
```

**返回码说明**：
- `0` = 允许继续
- `2` = 阻止操作

#### Bash 命令日志

```json
{
  "hooks": {
    "PreToolUse": [
      {
        "matcher": "Bash",
        "hooks": [
          {
            "type": "command",
            "command": "jq -r '.tool_input.command' >> ~/.claude/command-history.log"
          }
        ]
      }
    ]
  }
}
```

---

## 四、MCP Servers 扩展能力

### 4.1 什么是 MCP

MCP (Model Context Protocol) 允许 Claude Code 连接外部服务，扩展其能力边界。

### 4.2 常用集成

#### GitHub 集成

```bash
claude mcp add github -- npx -y @anthropic/mcp-server-github
```

使用：
```bash
> Review PR #456 and suggest improvements
> Create an issue for the bug we found
> Show all open PRs assigned to me
```

#### 数据库查询

```bash
claude mcp add db -- npx -y @bytebase/dbhub \
  --dsn "postgresql://readonly:pass@host:5432/analytics"
```

使用：
```bash
> What's our total revenue this month?
> Find customers inactive for 90 days
```

#### Sentry 错误监控

```bash
claude mcp add sentry --transport http https://mcp.sentry.dev/mcp
```

### 4.3 配置范围

```bash
# 本地范围（默认，个人使用）
claude mcp add stripe -- ...

# 项目范围（团队共享，提交到 git）
claude mcp add --scope project stripe -- ...

# 用户范围（所有项目可用）
claude mcp add --scope user stripe -- ...
```

### 4.4 MCP 资源引用

```bash
> Analyze @github:issue://123
> Compare @postgres:schema://users with @docs/models.md
```

### 4.5 Tool Search 懒加载

MCP 服务器可能提供大量工具，Claude Code 使用 **Tool Search** 机制进行懒加载，减少约 95% 的上下文占用：

- 启动时仅加载工具索引（名称 + 简要描述）
- 用户请求相关功能时，才加载完整工具定义
- 对于工具数量多的 MCP 服务器（如 GitHub），效果尤为显著

### 4.6 OAuth 支持

MCP 服务器现在支持 OAuth 认证流程：

```bash
# 添加支持 OAuth 的 MCP 服务器
claude mcp add my-service --transport http https://api.example.com/mcp

# 首次使用时会引导 OAuth 授权流程
```

### 4.7 list_changed 通知

MCP 服务器可以通过 `list_changed` 通知 Claude Code 工具列表已更新，无需重启即可获取新工具。

---

## 五、权限管理

### 5.1 权限配置

```json
{
  "permissions": {
    "defaultMode": "acceptEdits",
    "allow": [
      "Bash(npm run:*)",
      "Bash(git status)",
      "Bash(git diff)",
      "Bash(git add:*)",
      "Bash(git commit:*)",
      "Edit(./src/**)",
      "Read(./src/**)"
    ],
    "deny": [
      "Bash(rm -rf:*)",
      "Bash(curl:*)",
      "Read(./.env*)",
      "Read(./secrets/**)"
    ]
  }
}
```

### 5.2 五种权限模式

| 模式 | 说明 | 适用场景 |
|------|------|---------|
| **Normal** | 每次操作需确认 | 敏感操作、学习阶段 |
| **AcceptEdits** | 自动批准文件编辑 | 日常开发 |
| **DontAsk** | 自动批准大多数操作 | 信任环境下的高效开发 |
| **Plan** | 只规划不执行 | 架构设计、风险评估 |
| **BypassPermissions** | 跳过所有权限检查 | CI/CD、完全信任的自动化场景 |

**切换方式**：`Shift+Tab` 循环切换

### 5.3 通配符权限与 Skill 权限

```json
{
  "permissions": {
    "allow": [
      "Bash(npm *)",
      "Bash(pnpm *)",
      "Bash(git *)",
      "Skill(commit)",
      "Skill(review)"
    ]
  }
}
```

- `Bash(npm *)` — 允许所有以 `npm` 开头的命令
- `Skill(commit)` — 允许执行 `/commit` 技能

### 5.4 启动时指定模式

```bash
claude --permission-mode plan  # 规划模式
claude --permission-mode acceptEdits  # 自动接受
```

---

## 六、高效交互技巧

### 6.1 快捷键速查

| 快捷键 | 功能 |
|--------|------|
| `Ctrl+C` | 取消当前操作 |
| `Ctrl+D` | 退出 Claude Code |
| `Ctrl+L` | 清屏（保留历史）|
| `Ctrl+O` | 切换详细输出（显示思考过程）|
| `Ctrl+T` | 打开任务列表 |
| `Ctrl+B` | 查看后台任务 |
| `Ctrl+G` | 在外部编辑器中打开 |
| `Shift+Tab` | 切换权限模式 |
| `Shift+Enter` | 换行（多行输入）|
| `Esc+Esc` | 撤销最后的代码改动 |
| `Option+Enter` | 多行输入（macOS）|

### 6.2 管道模式

```bash
# 分析日志
cat error.log | claude -p "分析这个错误日志的根本原因"

# 代码审查
git diff | claude -p "审查这些改动，关注安全问题"

# 解释代码
cat complex.ts | claude -p "解释这段代码的逻辑"
```

### 6.3 快速命令

```bash
# 执行 Bash 命令
> ! npm test

# 添加到记忆
> # 记得先运行 lint 再提交

# 引用文件
> Explain @src/complex-algorithm.ts
```

### 6.4 Fast Mode

Fast Mode 使用同一个 Opus 4.6 模型，但以更快的速度生成输出。

```bash
# 在会话中切换
/fast

# 特点
# - 同模型 (Opus 4.6)，更快输出
# - 定价为标准模式的 6 倍
# - 适合需要快速迭代的场景
# - 仅 Opus 4.6 支持
```

### 6.5 快捷键配置

Claude Code 支持完全自定义快捷键，配置文件位于 `~/.claude/keybindings.json`：

```json
[
  {
    "key": "ctrl+s",
    "command": "submit"
  },
  {
    "key": "ctrl+shift+t",
    "command": "tasks"
  }
]
```

使用 `/keybindings` 命令可以快速查看和编辑快捷键配置。

---

## 七、提示词技巧

### 7.1 高效提示词结构

```
[上下文] → [具体任务] → [约束条件]
```

**示例**：

```bash
> 项目使用 TypeScript + React + Zustand
> 为 @src/components/UserProfile.tsx 添加编辑功能
> 要求：
>   - 使用现有的 Modal 组件
>   - 表单验证使用 zod
>   - 保持现有代码风格
```

### 7.2 何时使用深度思考

```bash
# 架构设计
> ultrathink: 我们应该用 Redux 还是 Zustand？

# 复杂调试
> ultrathink: 为什么这个内存泄漏只在生产环境出现？

# 重构规划
> ultrathink: 如何将这个单体应用拆分为微服务？
```

### 7.3 迭代式探索

```bash
# 第一步：理解全局
> give me an overview of this codebase

# 第二步：深入模块
> how does the authentication flow work?

# 第三步：制定方案
> what's the best way to add OAuth support?

# 第四步：执行实现
> implement Google OAuth login
```

---

## 八、实战工作流

### 8.1 Bug 修复流程

```bash
# 1. 描述问题
> 用户登录时偶发超时，错误信息：Connection timeout after 30s

# 2. 定位原因
> search for timeout configuration in the auth module

# 3. 分析代码
> explain the connection handling in @src/lib/auth.ts

# 4. 实施修复
> fix the timeout issue by adding retry logic

# 5. 验证修复
> run the auth tests

# 6. 创建 PR
> create a pr explaining the fix
```

### 8.2 新功能开发流程

```bash
# 1. 规划（Plan Mode）
claude --permission-mode plan
> I need to add a user notification system

# 2. 审查计划后切换到执行模式
# Shift+Tab 切换

# 3. 逐步实现
> create the notification service
> add the notification UI component
> integrate with the existing user system

# 4. 测试
> write tests for the notification feature
> run all tests

# 5. 收尾
> update the documentation
> create a pr
```

### 8.3 代码审查流程

```bash
# 审查 PR
> review PR #123, focus on:
>   - Security vulnerabilities
>   - Performance issues
>   - Code style consistency

# 审查本地改动
git diff main | claude -p "审查这些改动，给出改进建议"
```

---

## 九、配置示例

### 9.1 推荐的 `.claude/settings.json`

```json
{
  "permissions": {
    "defaultMode": "acceptEdits",
    "allow": [
      "Bash(npm run:*)",
      "Bash(pnpm:*)",
      "Bash(git:*)",
      "Bash(node:*)",
      "Edit(./src/**)",
      "Edit(./tests/**)",
      "Read(**)"
    ],
    "deny": [
      "Bash(rm -rf:*)",
      "Bash(curl:*)",
      "Bash(wget:*)",
      "Read(./.env*)",
      "Edit(./.env*)"
    ]
  },
  "hooks": {
    "PostToolUse": [
      {
        "matcher": "Edit|Write",
        "hooks": [
          {
            "type": "command",
            "command": "file=$(jq -r '.tool_input.file_path'); [[ $file == *.ts || $file == *.tsx || $file == *.js || $file == *.jsx ]] && npx prettier --write \"$file\" 2>/dev/null || true"
          }
        ]
      }
    ]
  }
}
```

### 9.2 推荐的 `CLAUDE.md` 模板

```markdown
# 项目名称

> 一句话描述项目用途

## 快速命令

- 开发: `pnpm dev`（Next.js 16 默认使用 Turbopack）
- 构建: `pnpm build`
- 测试: `pnpm test`
- 检查: `pnpm lint`

## 技术栈

- 语言: TypeScript 5.9
- 框架: React 19 / Next.js 16
- 样式: Tailwind CSS v4（CSS-first 配置，无需 tailwind.config.js）
- 状态管理: Zustand 5
- 测试: Vitest 4

## 项目结构

src/
├── app/           # 页面
├── components/    # 组件
├── hooks/         # Hooks
├── lib/           # 工具函数
├── types/         # 类型定义
└── proxy.ts       # 请求代理

## 代码规范

- 2 空格缩进
- 函数式组件 + Hooks
- 组件文件 PascalCase
- 工具函数 camelCase
- React 19: ref 直接作为 props，不需要 forwardRef
- React 19: 优先使用 Server Components
- Next.js 16: cookies()/headers()/params 必须 await
- Tailwind v4: @import "tailwindcss"

## 关键文件

- `src/lib/api.ts` - API 封装
- `src/lib/auth.ts` - 认证逻辑
- `src/types/index.ts` - 全局类型

## 注意事项

- 不修改 .env 文件
- 提交前运行 lint 和 test
- PR 需要 review
```

---

## 总结：高效使用的核心原则

| 原则 | 实践方法 |
|------|---------|
| **减少搜索开销** | 用 `@` 直接引用文件 |
| **建立长期记忆** | 维护好 `CLAUDE.md` + Auto Memory |
| **自动化重复操作** | 配置 Hooks |
| **扩展能力边界** | 集成 MCP Servers |
| **控制安全边界** | 配置权限白名单/黑名单 |
| **复杂问题深度思考** | 使用 `ultrathink:` 前缀 |
| **融入现有工具链** | 善用管道模式 |
| **管理复杂工作流** | 使用 Task System |
| **复用最佳实践** | 编写 Skills |

---

## 十、CLI 参数与斜杠命令速查

### 10.1 启动参数

```bash
# 基本启动
claude                          # 交互式 REPL
claude "你的问题"               # 带初始提示启动
claude -p "查询"                # 单次查询后退出（SDK 模式）
claude -c                       # 继续上次对话
claude -r "session"             # 恢复指定会话

# 模型选择
claude --model sonnet           # 使用 Sonnet
claude --model opus             # 使用 Opus

# 权限模式
claude --permission-mode plan   # 规划模式启动
claude --dangerously-skip-permissions  # 跳过权限（谨慎）

# 高级选项
claude --add-dir ../lib ../shared      # 添加额外目录到上下文
claude --append-system-prompt "..."    # 追加系统提示
claude --tools "Bash,Edit,Read"        # 限制可用工具
claude --verbose                       # 详细输出
claude --debug "api,mcp"               # 调试模式
```

### 10.2 斜杠命令速查表

#### 会话控制

| 命令 | 说明 |
|------|------|
| `/help` | 查看所有命令 |
| `/clear` | 清除对话历史 |
| `/exit` | 退出 Claude Code |
| `/compact` | 压缩对话（节省 tokens）|
| `/rewind` | 回退代码/对话 |

#### 会话管理

| 命令 | 说明 |
|------|------|
| `/resume [session]` | 恢复指定会话 |
| `/rename <name>` | 重命名当前会话 |
| `/export [file]` | 导出对话到文件 |

#### 配置与状态

| 命令 | 说明 |
|------|------|
| `/config` | 打开设置界面 |
| `/status` | 查看状态信息 |
| `/model` | 切换模型 |
| `/memory` | 编辑 CLAUDE.md |
| `/permissions` | 查看/更新权限 |
| `/cost` | Token 使用统计 |
| `/context` | 上下文可视化 |
| `/stats` | 使用统计 |

#### 开发工具

| 命令 | 说明 |
|------|------|
| `/init` | 初始化项目 CLAUDE.md |
| `/doctor` | 检查安装健康状况 |
| `/tasks` | 列出当前任务 |
| `/bashes` | 管理后台任务 |
| `/vim` | 进入 vim 模式 |
| `/fast` | 切换 Fast Mode（同模型快速输出）|
| `/teleport` | 将会话转移到 claude.ai/code Web 界面 |
| `/keybindings` | 查看/编辑快捷键配置 |

#### 集成管理

| 命令 | 说明 |
|------|------|
| `/mcp` | 管理 MCP 服务器 |
| `/hooks` | 管理 Hooks |
| `/agents` | 管理自定义 Agents |

### 10.3 任务管理系统 (Task System)

Claude Code 内置任务管理系统（替代旧的 Todo 系统），通过四个核心工具管理复杂工作流：

#### 核心工具

| 工具 | 功能 | 示例 |
|------|------|------|
| `TaskCreate` | 创建任务 | 定义待办事项、描述、活动状态 |
| `TaskUpdate` | 更新任务 | 修改状态、设置依赖关系 |
| `TaskGet` | 获取任务 | 查看任务详细信息 |
| `TaskList` | 列出任务 | 查看所有任务及状态 |

#### 任务依赖关系

任务支持 `blocks` 和 `blockedBy` 依赖，确保执行顺序：

```bash
# Claude 会自动创建和管理任务
> 我需要重构认证模块：
>   1. 先更新数据库 schema
>   2. 然后修改 API 端点
>   3. 最后更新前端组件

# Claude 会创建 3 个任务，并设置 2 blockedBy 1，3 blockedBy 2
```

#### 查看任务

```bash
# 使用斜杠命令
/tasks

# 快捷键
Ctrl+T
```

#### 特点

- **跨会话持久化**：任务在会话间保留
- **自动状态管理**：Claude 开始工作时标记 `in_progress`，完成后标记 `completed`
- **依赖追踪**：被阻塞的任务在依赖完成前不会开始

---

## 十一、Skills 系统

Skills 系统（v2.1.3+）统一了斜杠命令和自定义指令，提供更强大的可扩展能力。

### 11.1 Skills 概述

Skills 是 Claude Code 的扩展机制，将原来的自定义命令（`.claude/commands/`）升级为功能更完整的技能系统。

#### 存放位置

```
.claude/skills/          # 项目级（提交到 git，团队共享）
~/.claude/skills/        # 全局级（个人所有项目可用）
```

> **迁移路径**：`.claude/commands/` 仍然兼容，但建议迁移到 `.claude/skills/`

### 11.2 SKILL.md 文件格式

Skills 使用 Markdown 文件定义，支持 frontmatter 元数据：

#### 简单 Skill

`.claude/skills/review.md`:
```markdown
---
description: 代码审查
---

审查当前改动，关注：
- 安全漏洞
- 性能问题
- 代码风格
- 潜在 bug
```

使用：`/review`

#### 带参数的 Skill

`.claude/skills/fix-issue.md`:
```markdown
---
argument-hint: [issue-number]
description: 修复 GitHub Issue
---

查看 GitHub Issue #$1，分析问题并实现修复。
```

使用：`/fix-issue 123`

#### 完整 frontmatter 选项

```markdown
---
description: Skill 描述（显示在命令列表中）
argument-hint: [参数说明]
---

Skill 内容...
```

### 11.3 高级特性

#### 热重载

修改 SKILL.md 文件后，无需重启 Claude Code，下次调用时自动加载最新版本。

#### 分叉上下文

Skills 在独立的上下文分支中执行，不会污染主对话上下文。执行完成后，结果会合并回主对话。

#### Skill 权限

可以在权限配置中针对 Skill 设置权限：

```json
{
  "permissions": {
    "allow": [
      "Skill(review)",
      "Skill(commit)"
    ]
  }
}
```

### 11.4 实用 Skill 示例

#### 代码审查 Skill

```markdown
---
description: 安全审查
---

对当前分支的改动进行安全审查：

1. 检查 OWASP Top 10 漏洞
2. 检查敏感信息泄露
3. 检查输入验证
4. 检查权限控制
5. 生成安全报告
```

#### PR 创建 Skill

```markdown
---
argument-hint: [base-branch]
description: 创建 PR
---

基于 $1 分支创建 Pull Request：
1. 总结所有提交的改动
2. 生成清晰的 PR 描述
3. 列出测试清单
```

#### 测试生成 Skill

```markdown
---
argument-hint: [file-path]
description: 为文件生成测试
---

为 @$1 生成单元测试：
- 使用项目现有的测试框架
- 覆盖主要功能和边界情况
- 包含 mock 和 fixture
```

#### 组件创建 Skill

```markdown
---
argument-hint: [ComponentName] [type: page|ui|feature]
description: 创建 React 组件
---

在 src/components/$2/ 目录创建 $1 组件，包含：
- 组件文件 $1.tsx
- 样式文件 $1.module.css
- 测试文件 $1.test.tsx
- index.ts 导出
```

使用：`/create-component UserCard ui`

---

## 十二、会话管理

### 12.1 会话操作

```bash
# 继续最近会话
claude -c

# 继续并追加提示
claude -c "继续上次的工作"

# 恢复指定会话（按名称或 ID）
claude -r "feature-auth"
claude -r "abc123"

# 在 Claude Code 内恢复
> /resume feature-auth
```

### 12.2 会话命名

```bash
# 重命名当前会话
> /rename auth-implementation

# 之后可以用名称恢复
claude -r "auth-implementation"
```

### 12.3 会话导出

```bash
# 导出到剪贴板
> /export

# 导出到文件
> /export conversation.md
> /export ~/notes/debug-session.md
```

### 12.4 查看会话历史

```bash
# 查看统计信息（包含会话历史）
> /stats
```

### 12.5 会话最佳实践

| 场景 | 建议 |
|------|------|
| 长期功能开发 | 命名会话，便于恢复 |
| 调试问题 | 导出会话供团队参考 |
| 多任务切换 | 不同任务用不同会话 |
| 节省 token | 定期 `/compact` 压缩 |
| 重新开始 | `/clear` 清除历史 |

### 12.6 /teleport 会话转移

使用 `/teleport` 可以将当前终端中的 Claude Code 会话转移到 claude.ai/code Web 界面，保持完整上下文：

```bash
# 在终端中
/teleport

# Claude Code 会生成一个链接
# 在浏览器中打开即可继续对话
```

**适用场景**：
- 需要在移动设备上继续工作
- 想要更好的渲染效果（图片、表格等）
- 与团队成员共享会话上下文

---

## 十三、深入理解 Agent 架构

### 13.1 Claude Code 的本质

Claude Code 不是简单的"聊天机器人 + 代码执行"，而是一个 **自主代理系统 (Autonomous Agent System)**。理解这一点对于高效使用至关重要。

```
┌─────────────────────────────────────────────────────────────────┐
│                      Claude Code 架构                            │
├─────────────────────────────────────────────────────────────────┤
│                                                                  │
│  ┌──────────┐    ┌──────────┐    ┌──────────┐    ┌──────────┐  │
│  │  用户    │───→│  主Agent │───→│ Sub-Agent│───→│  工具层  │  │
│  │  输入    │    │(Opus 4.6)│    │(Son. 4.5)│    │ Bash/Edit│  │
│  └──────────┘    └──────────┘    └──────────┘    └──────────┘  │
│        ↑              │                               │         │
│        │              ▼                               ▼         │
│        │         ┌──────────┐                  ┌──────────┐     │
│        └─────────│ 反馈循环 │←─────────────────│ 执行结果 │     │
│                  └──────────┘                  └──────────┘     │
│                                                                  │
└─────────────────────────────────────────────────────────────────┘
```

### 13.2 Sub-Agent 系统

Claude Code 内部使用多个专门化的子代理：

| Agent 类型 | 用途 | 特点 |
|-----------|------|------|
| `Explore` | 代码库探索 | 快速搜索，低 token 消耗 |
| `Plan` | 架构规划 | 深度分析，高质量输出 |
| `general-purpose` | 复杂任务 | 全工具访问 |

**设计哲学**：将复杂任务分解给专门的子代理，主代理负责协调和整合结果。

```bash
# 当你问"这个项目的结构是什么"时，实际发生：
用户提问 → 主Agent判断 → 派发给Explore Agent → 并行搜索 → 汇总结果 → 返回用户
```

### 13.3 工具调用的决策机制

Claude Code 在选择工具时遵循以下优先级：

```
1. 专用工具优先于通用工具
   Read > cat | Bash
   Edit > sed | Bash
   Grep > grep | Bash

2. 并行执行优先于串行
   同时读取多个独立文件 > 逐个读取

3. 精确定位优先于广泛搜索
   @file 直接引用 > Grep 搜索 > Glob 模式匹配

4. 增量修改优先于全量重写
   Edit(局部替换) > Write(全文件重写)
```

### 13.4 上下文窗口的真相

**200K Token 的实际意义**：

```
200K tokens ≈ 150,000 单词 ≈ 500 页文档 ≈ 约 50-100 个中型源文件
```

**上下文构成**：

```
┌─────────────────────────────────────────────┐
│            200K Token 上下文窗口              │
├─────────────────────────────────────────────┤
│ 系统提示 (~5K)                               │
│ ├── Claude Code 核心指令                     │
│ └── 工具定义                                 │
├─────────────────────────────────────────────┤
│ 项目记忆 (~1-5K)                             │
│ ├── CLAUDE.md                               │
│ └── rules/*.md                              │
├─────────────────────────────────────────────┤
│ 对话历史 (动态，主要消耗)                     │
│ ├── 用户消息                                 │
│ ├── Claude 回复                              │
│ └── 工具调用结果 ← 最大消耗来源              │
├─────────────────────────────────────────────┤
│ 当前任务上下文                               │
│ └── 读取的文件、搜索结果等                    │
└─────────────────────────────────────────────┘
```

**关键洞察**：工具调用结果（尤其是读取大文件）是 token 消耗的主要来源。

### 13.5 自动摘要机制

当上下文接近极限时，Claude Code 会：

1. **自动压缩历史对话**：保留关键信息，丢弃冗余细节
2. **保留最新上下文**：确保当前任务不受影响
3. **维护任务连续性**：记住正在进行的工作

```bash
# 手动触发压缩
> /compact

# 查看当前上下文使用
> /context
```

---

## 十四、常见问题与疑难排解

### 14.1 Claude "幻觉"问题

**症状**：Claude 生成不存在的文件路径、API 或函数名

**原因**：
- 上下文中缺少必要信息
- 过于模糊的指令
- 项目特定的约定未说明

**解决方案**：

```bash
# ❌ 模糊请求
> 帮我调用用户服务的API

# ✅ 精确请求
> 查看 @src/services/user.ts 中的 getUserById 函数，然后调用它
```

**预防措施**：
1. 在 CLAUDE.md 中明确项目 API 和约定
2. 使用 `@` 引用具体文件
3. 要求 Claude 先搜索再行动

### 14.2 上下文丢失

**症状**：Claude 忘记了之前讨论的内容

**原因**：
- 对话过长，触发了自动压缩
- 跨会话工作

**解决方案**：

```bash
# 1. 使用 /compact 时添加保留指令
> /compact 保留关于认证模块重构的所有讨论

# 2. 重要决定写入记忆
> # 决定：使用 JWT 而非 Session，原因是...

# 3. 命名会话便于恢复
> /rename auth-refactor
```

### 14.3 代码改坏了怎么办

**即时回滚**：

```bash
# 方法1：双击 Esc
Esc + Esc

# 方法2：使用 rewind
> /rewind

# 方法3：Git 回滚（如果已提交）
> ! git checkout -- path/to/file
```

**预防措施**：

```bash
# 1. 重要修改前先 Plan Mode
claude --permission-mode plan

# 2. 启用自动 Git 检查点
# 在 hooks 中配置自动 stash
```

### 14.4 Claude 陷入循环

**症状**：反复尝试同样的失败方案

**解决方案**：

```bash
# 1. 中断并重新引导
Ctrl+C
> 停止。让我们换个方法。不要使用 X，尝试 Y

# 2. 提供更多上下文
> 之前的方法失败是因为 [原因]。这个项目的约定是 [说明]

# 3. 清除上下文重新开始
> /clear
```

### 14.5 大文件处理

**问题**：读取大文件消耗大量 token

**解决方案**：

```bash
# 1. 指定行范围
> 读取 @src/large-file.ts 的第 100-200 行

# 2. 使用 Grep 精确定位
> 在 @src/large-file.ts 中搜索 "handleAuth" 函数

# 3. 让 Claude 分段读取
> 这个文件很大，请只读取与用户认证相关的部分
```

### 14.6 命令执行超时

**解决方案**：

```bash
# 1. 后台运行
> 运行 npm run build（后台执行）

# 2. 增加超时
> 运行测试，超时设为 5 分钟

# 3. 分步执行
> 先只运行 auth 模块的测试
```

### 14.7 权限被拒绝

**诊断**：

```bash
> /permissions  # 查看当前权限配置
> /doctor       # 检查安装状态
```

**常见解决**：

```json
{
  "permissions": {
    "allow": [
      "Bash(npm:*)",
      "Edit(./src/**)"
    ]
  }
}
```

---

## 十五、成本优化策略

### 15.1 Token 成本构成

```
总成本 = 输入 tokens × 输入价格 + 输出 tokens × 输出价格 + 思考 tokens × 思考价格
```

**各模型价格对比**（每百万 token）：

| 模型 | 输入 | 输出 | 适用场景 |
|------|------|------|---------|
| Haiku 4.5 | $1 | $5 | 简单查询、搜索 |
| Sonnet 4.5 | $3 | $15 | 日常开发 |
| Opus 4.6 | $5 | $25 | 复杂架构、深度分析 |

### 15.2 成本优化技巧

#### 选择合适的模型

```bash
# 简单任务用 Haiku 4.5/Sonnet 4.5
claude --model sonnet "修复这个 typo"

# 复杂任务才用 Opus 4.6
claude --model opus "设计微服务架构"
```

#### 减少上下文膨胀

```bash
# ❌ 读取整个大文件
> 解释 @src/giant-module.ts

# ✅ 只读取需要的部分
> 解释 @src/giant-module.ts 中的 processPayment 函数
```

#### 及时压缩对话

```bash
# 完成一个子任务后压缩
> /compact

# 查看当前成本
> /cost
```

#### 使用管道模式处理批量任务

```bash
# 批量处理，一次请求
find src -name "*.ts" | head -5 | claude -p "检查这些文件的类型错误"
```

### 15.3 成本监控

```bash
# 查看当前会话成本
> /cost

# 查看历史统计
> /stats
```

**设置预算提醒**（通过环境变量）：

```bash
export CLAUDE_CODE_COST_WARNING_THRESHOLD=5.00  # 超过 $5 提醒
```

### 15.4 企业使用的成本控制

```json
{
  "permissions": {
    "defaultMode": "normal"  // 非 auto-accept，减少误操作
  },
  "env": {
    "MAX_THINKING_TOKENS": "10000"  // 限制思考 token
  }
}
```

---

## 十六、IDE 与工具链集成

### 16.1 VS Code 集成

#### 终端集成

```bash
# 在 VS Code 终端直接使用
code --new-window /path/to/project
# 然后在集成终端运行 claude
```

#### 配合 VS Code 扩展

```bash
# 让 Claude 与 VS Code 协作
> 我在 VS Code 中打开了 @src/App.tsx，帮我添加一个新的路由

# 利用 VS Code 的问题面板
> 查看 VS Code 报告的 TypeScript 错误并修复它们
```

### 16.2 JetBrains IDE 集成

```bash
# 在 JetBrains 终端工具窗口使用
# 或使用外部终端

# 配合 IDE 功能
> 检查 IDE 标记的代码警告，解释并修复它们
```

### 16.3 与 Git 工作流集成

#### Pre-commit Hook

```bash
# .git/hooks/pre-commit
#!/bin/bash
# 使用 Claude 检查提交
git diff --cached | claude -p "快速检查这些改动是否有明显问题" --model haiku
```

#### PR 自动审查

```yaml
# .github/workflows/claude-review.yml
name: Claude Code Review
on: [pull_request]
jobs:
  review:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - name: Review with Claude
        run: |
          gh pr diff ${{ github.event.pull_request.number }} | \
          claude -p "审查这个 PR 的代码改动"
```

### 16.4 与测试框架集成

```bash
# 测试失败时自动分析
npm test 2>&1 | claude -p "分析测试失败原因并建议修复方案"

# TDD 工作流
> 为 @src/utils/validator.ts 编写测试，然后运行测试
> 测试失败了，请修复实现代码
> 运行测试确认修复
```

### 16.5 与构建工具集成

```bash
# 构建错误分析
npm run build 2>&1 | claude -p "分析构建错误并修复"

# Webpack/Vite 配置调整
> 分析 @vite.config.ts，优化构建性能
```

### 16.6 Docker 环境

```bash
# 在 Docker 容器中使用
docker run -it -v $(pwd):/app -w /app node:18 bash
# 然后安装并运行 claude

# 或者创建专用开发容器
# .devcontainer/devcontainer.json
{
  "postCreateCommand": "npm install -g @anthropic-ai/claude-code"
}
```

---

## 十七、团队协作模式

### 17.1 共享配置管理

#### 项目级配置（提交到 Git）

```
.claude/
├── settings.json      # 团队共享的权限和 hooks
├── skills/            # 自定义团队 Skills
│   ├── review.md
│   ├── deploy.md
│   └── onboard.md
└── rules/             # 模块化编码规范
    ├── api.md
    ├── testing.md
    └── security.md
```

#### 个人配置（不提交）

```
.claude/settings.local.json   # 个人权限覆盖
CLAUDE.local.md               # 个人笔记
```

### 17.2 新成员入职

创建入职 Skill `.claude/skills/onboard.md`：

```markdown
---
description: 新成员入职引导
---

作为新成员入职助手，请：

1. 概述这个项目的目的和架构
2. 解释目录结构和关键文件
3. 说明开发工作流程（分支策略、PR 流程）
4. 介绍主要的技术栈和约定
5. 列出常用命令和快捷操作
```

使用：
```bash
> /onboard
```

### 17.3 代码审查标准化

创建审查 Skill `.claude/skills/team-review.md`：

```markdown
---
argument-hint: [pr-number]
description: 团队标准代码审查
---

对 PR #$1 进行团队标准审查，检查：

## 功能正确性
- 是否实现了预期功能
- 边界情况处理

## 代码质量（参考 @.claude/rules/code-style.md）
- 命名规范
- 函数长度
- 复杂度

## 安全性（参考 @.claude/rules/security.md）
- 输入验证
- 敏感数据处理

## 测试覆盖
- 单元测试
- 集成测试

## 文档
- 代码注释
- API 文档更新

输出格式：按优先级列出问题（P0 阻断/P1 重要/P2 建议）
```

### 17.4 知识沉淀

将 Claude 的回答沉淀为团队知识：

```bash
# 导出有价值的对话
> /export docs/decisions/auth-architecture.md

# 更新团队文档
> 将刚才关于认证架构的讨论总结，更新到 @docs/ARCHITECTURE.md
```

### 17.5 MCP 服务器团队共享

```bash
# 添加团队共享的 MCP 服务器
claude mcp add --scope project jira -- npx -y @anthropic/mcp-server-jira

# 这会创建 .claude/mcp.json，提交到 Git 后团队成员自动获得
```

---

## 十八、人机协作心智模型

### 18.1 正确理解 Claude Code 的角色

**Claude Code 是什么**：
- ✅ 强大的结对编程伙伴
- ✅ 知识渊博的技术顾问
- ✅ 高效的任务执行者
- ✅ 代码库的快速导航器

**Claude Code 不是什么**：
- ❌ 完美无误的神谕
- ❌ 替代你思考的工具
- ❌ 不需要审查的自动化
- ❌ 所有问题的万能解

### 18.2 最佳协作模式

#### 模式一：探索者与向导

```
你（探索者）：我想了解这个项目的认证机制
Claude（向导）：[搜索分析] 这个项目使用 JWT...架构是...

最佳实践：用于理解陌生代码库
```

#### 模式二：驾驶员与副驾驶

```
你（驾驶员）：明确知道要做什么，需要帮助执行
Claude（副驾驶）：帮你写代码、运行命令、检查结果

最佳实践：日常开发任务
```

#### 模式三：架构师与实现者

```
你（架构师）：设计高层方案，做关键决策
Claude（实现者）：将设计转化为代码

最佳实践：新功能开发
```

#### 模式四：审查者与被审查者

```
你（审查者）：检查 Claude 的输出
Claude（被审查者）：执行后等待你的反馈

最佳实践：关键代码修改
```

### 18.3 高效提问的艺术

#### 层次化提问

```bash
# 第一层：理解（不执行）
> explain the payment processing flow

# 第二层：规划（Plan Mode）
> how would you add refund functionality?

# 第三层：执行（有了理解和规划后）
> implement the refund feature as discussed
```

#### 约束驱动的提问

```bash
# 添加明确约束
> 添加日志功能，要求：
>   - 使用现有的 logger 实例
>   - 不要引入新依赖
>   - 性能影响最小化
>   - 日志格式与现有保持一致
```

#### 迭代式细化

```bash
# 从粗到细
> 概述这个模块的职责           # 获得全貌
> 详细解释 processOrder 函数   # 聚焦细节
> 这里的错误处理有什么问题？    # 深入问题
> 按照最佳实践修复它           # 执行改进
```

### 18.4 何时信任，何时验证

| 任务类型 | 信任程度 | 验证方式 |
|---------|---------|---------|
| 代码解释 | 高 | 抽查核心逻辑 |
| 简单修改 | 中高 | 快速审阅 diff |
| 复杂重构 | 中 | 仔细审查 + 测试 |
| 架构决策 | 低 | 深度评估 + 团队讨论 |
| 安全相关 | 极低 | 专业安全审查 |

### 18.5 培养直觉

**识别 Claude 的"不确定"信号**：
- "可能"、"也许"、"我认为" → 需要验证
- "通常"、"一般来说" → 可能有例外
- 过于冗长的解释 → 可能不太确定

**识别 Claude 的"确定"信号**：
- 直接给出代码或命令
- 引用具体的文件和行号
- 简洁明确的回答

### 18.6 错误恢复策略

```bash
# Claude 犯错时的应对

# 1. 不要生气，冷静描述问题
> 这个改动破坏了现有功能，错误信息是 [xxx]

# 2. 提供更多上下文
> 这个项目有个特殊约定：[说明]

# 3. 引导正确方向
> 不要用 approach A，试试 approach B

# 4. 必要时回滚重来
Esc + Esc  # 撤销改动
> /clear    # 清除错误的上下文
```

### 18.7 效率最大化的日常实践

```bash
# 早晨：快速上下文恢复
claude -c  # 继续昨天的会话
> 总结一下昨天的进度和今天的计划

# 工作中：善用记忆
> # 发现 bug：用户头像上传在 Safari 失败
> 先记录下来，等会处理

# 任务切换：命名会话
> /rename feature-payment
# 切换到另一个任务
> /clear
> /rename bugfix-safari

# 下班前：保存上下文
> 总结今天的改动和明天需要继续的工作
> /export ~/notes/$(date +%Y-%m-%d).md
```

---

## 十九、反模式：避免这些错误

### 19.1 提示词反模式

#### ❌ 模糊指令

```bash
# 反模式
> 帮我改进代码

# 正确做法
> 优化 @src/utils/parser.ts 的 parseJSON 函数，减少内存分配
```

#### ❌ 一次性塞太多任务

```bash
# 反模式
> 添加用户认证、购物车、支付功能、发送邮件通知

# 正确做法
> 先实现用户认证功能
# 完成后再继续下一个
```

#### ❌ 不提供上下文就期望完美结果

```bash
# 反模式
> 修复登录 bug

# 正确做法
> 用户反馈：登录时报错 "Invalid token"
> 复现步骤：1. 进入登录页 2. 输入正确密码 3. 点击登录
> 查看 @src/auth/login.ts 和相关日志
```

### 19.2 工作流反模式

#### ❌ 不看就接受所有改动

**风险**：可能引入 bug、安全漏洞或破坏现有功能

**正确做法**：
- 复杂改动使用 Plan Mode 先审查
- 关键代码改动后运行测试
- 定期使用 `Esc+Esc` 回滚检查

#### ❌ 会话过长不压缩

**问题**：
- Token 成本飙升
- 上下文被截断，丢失重要信息
- 响应变慢

**正确做法**：
```bash
# 完成一个子任务后压缩
> /compact

# 或开始新会话
> /clear
```

#### ❌ 所有任务都用 Opus 4.6

**问题**：成本过高

**正确做法**：
```bash
# 简单任务
claude --model haiku "这个函数是做什么的"

# 日常开发
claude --model sonnet

# 复杂架构决策
claude --model opus
```

### 19.3 配置反模式

#### ❌ 没有 CLAUDE.md

**问题**：
- 每次都要重复解释项目背景
- Claude 不了解项目约定
- 可能生成不符合风格的代码

#### ❌ 权限全开

```json
// 反模式
{
  "permissions": {
    "allow": ["*"]
  }
}

// 正确做法
{
  "permissions": {
    "allow": ["Bash(npm:*)", "Edit(./src/**)"],
    "deny": ["Read(./.env*)", "Bash(rm -rf:*)"]
  }
}
```

#### ❌ 敏感信息放在 CLAUDE.md

```markdown
<!-- 反模式 -->
# API Keys
- OpenAI: sk-xxxxx
- Database: postgres://user:pass@host

<!-- 正确做法 -->
# 环境变量
敏感信息存放在 .env.local（不要让 Claude 读取）
```

### 19.4 协作反模式

#### ❌ 完全依赖 Claude 做决策

**问题**：Claude 可能不了解业务背景、团队约定

**正确做法**：
- 架构决策：Claude 提供选项，你做决定
- 业务逻辑：你描述需求，Claude 实现

#### ❌ 不验证 Claude 的输出

**高风险场景**：
- 安全相关代码
- 数据库操作
- 金融计算
- 外部 API 调用

**验证清单**：
- [ ] 代码逻辑正确
- [ ] 边界情况处理
- [ ] 错误处理完善
- [ ] 测试通过

---

## 二十、角色专属指南

### 20.1 前端开发者

#### 常用场景

```bash
# 组件开发
> 创建一个 UserCard 组件，使用 Tailwind CSS，包含头像、名称、邮箱

# 状态管理
> 分析 @src/store/ 的状态管理，找出可能的性能问题

# 样式调试
> 这个 flexbox 布局在 Safari 上有问题，帮我修复

# 响应式设计
> 让 @src/components/Dashboard.tsx 适配移动端
```

#### 推荐 CLAUDE.md 配置

```markdown
## 前端规范
- 组件库：shadcn/ui
- 样式：Tailwind CSS，遵循移动优先
- 状态管理：Zustand
- 表单：React Hook Form + Zod

## 组件规范
- 使用函数式组件
- Props 用 interface 定义
- 复杂组件拆分到 components/ 子目录
```

#### 有用的自定义 Skill

```markdown
<!-- .claude/skills/component.md -->
---
argument-hint: [ComponentName]
description: 创建标准 React 组件
---

在 src/components/ 创建 $1 组件，包含：
- $1.tsx（使用 TypeScript）
- $1.test.tsx（使用 Vitest）
- index.ts 导出

遵循项目现有组件的风格。
```

### 20.2 后端开发者

#### 常用场景

```bash
# API 开发
> 创建 /api/users/:id 的 CRUD 端点，使用现有的数据库连接

# 数据库
> 分析 @prisma/schema.prisma，建议索引优化

# 性能分析
> 这个查询很慢，帮我优化
> [粘贴 SQL 或代码]

# 安全审查
> 检查 @src/api/ 目录的安全问题，特别是注入和认证
```

#### 推荐 CLAUDE.md 配置

```markdown
## 后端规范
- 框架：Express / Fastify / NestJS
- 数据库：PostgreSQL + Prisma
- 认证：JWT
- 日志：Pino

## API 规范
- RESTful 设计
- 统一错误响应格式
- 请求验证使用 Zod
- 所有端点需要单元测试
```

### 20.3 全栈开发者

#### 工作流建议

```bash
# 1. 从 API 开始
> 设计并实现用户注册 API

# 2. 然后前端
> 创建注册表单，调用刚才的 API

# 3. 集成测试
> 写一个 E2E 测试覆盖注册流程

# 4. 一起提交
> 创建 commit，包含用户注册功能的前后端代码
```

### 20.4 DevOps 工程师

#### 常用场景

```bash
# Docker
> 优化 @Dockerfile，减小镜像体积

# CI/CD
> 分析 @.github/workflows/，找出可以并行的步骤

# 基础设施
> 审查 @terraform/main.tf 的安全配置

# 监控
> 为这个服务添加 Prometheus 指标
```

#### 推荐 MCP 集成

```bash
# AWS
claude mcp add aws -- npx -y @anthropic/mcp-server-aws

# Kubernetes
claude mcp add k8s -- npx -y @anthropic/mcp-server-kubernetes
```

### 20.5 技术负责人 / 架构师

#### 使用策略

```bash
# 1. 代码审查
> 审查这个 PR 的架构影响，特别关注扩展性和维护性

# 2. 技术决策
> ultrathink: 分析使用 GraphQL vs REST 的利弊，考虑我们的团队规模和项目特点

# 3. 遗留代码分析
> 分析 @src/legacy/ 模块，制定重构计划

# 4. 文档生成
> 为 @src/core/ 生成架构文档，包含模块关系图
```

#### Plan Mode 的最佳使用

```bash
# 启动 Plan Mode
claude --permission-mode plan

# 让 Claude 分析但不执行
> 如何将这个单体应用拆分为微服务？给我一个详细的迁移计划

# 审查计划后再决定是否执行
```

---

## 二十一、提示词模板库

### 21.1 代码理解类

#### 项目概览

```
给我这个项目的全面概览：
1. 项目目的和主要功能
2. 技术栈和依赖
3. 目录结构和模块划分
4. 入口文件和核心流程
5. 开发和部署方式
```

#### 函数/模块分析

```
分析 @[文件路径]：
1. 主要功能和职责
2. 输入输出
3. 依赖关系
4. 潜在问题或改进点
```

#### 数据流追踪

```
追踪 [功能名称] 的数据流：
1. 从用户输入开始
2. 经过哪些函数/模块
3. 数据如何转换
4. 最终输出/存储
```

### 21.2 代码修改类

#### Bug 修复

```
修复这个 bug：
- 现象：[描述问题]
- 复现步骤：[步骤]
- 期望行为：[应该怎样]
- 相关文件：@[文件]

请：
1. 定位问题根因
2. 修复代码
3. 添加防止回归的测试
```

#### 功能添加

```
添加 [功能名称] 功能：
- 需求：[详细描述]
- 影响范围：[涉及的模块]
- 约束：
  - 使用现有的 [xxx] 组件/服务
  - 不引入新依赖
  - 保持与现有代码风格一致

请先说明实现方案，我确认后再开始编码。
```

#### 重构

```
重构 @[文件路径]：
- 目标：[提高可读性/性能/可测试性]
- 约束：
  - 不改变外部接口
  - 保持现有测试通过
  - 逐步进行，每步可验证
```

### 21.3 代码审查类

#### PR 审查

```
审查这个 PR，关注：

## 功能正确性
- 是否实现了需求
- 边界情况处理

## 代码质量
- 可读性和命名
- 复杂度
- 重复代码

## 安全性
- 输入验证
- 敏感数据处理
- 权限检查

## 性能
- 明显的性能问题
- N+1 查询
- 内存泄漏风险

## 测试
- 测试覆盖是否充分
- 测试质量

输出格式：按 P0（阻断）/ P1（重要）/ P2（建议）分类
```

#### 安全审查

```
对 @[目录/文件] 进行安全审查：

检查项：
1. SQL 注入
2. XSS
3. CSRF
4. 敏感数据泄露
5. 认证/授权问题
6. 依赖漏洞
7. 配置安全

对每个发现：
- 严重程度（高/中/低）
- 问题位置
- 风险说明
- 修复建议
```

### 21.4 测试类

#### 单元测试生成

```
为 @[文件路径] 生成单元测试：

要求：
- 使用 [测试框架]
- 覆盖主要功能路径
- 覆盖边界情况
- 覆盖错误处理
- Mock 外部依赖

遵循项目现有的测试风格。
```

#### E2E 测试场景

```
为 [功能] 设计 E2E 测试场景：

用户故事：[描述]

测试场景（按优先级）：
1. Happy path
2. 边界情况
3. 错误处理
4. 并发情况（如适用）

使用 [Playwright/Cypress] 框架。
```

### 21.5 文档类

#### API 文档

```
为 @[API 文件] 生成 API 文档：

包含：
- 端点描述
- 请求方法和 URL
- 请求参数（path/query/body）
- 请求示例
- 响应格式
- 响应示例
- 错误码说明

格式：OpenAPI 3.0 / Markdown
```

#### 架构文档

```
为 @[目录] 生成架构文档：

包含：
1. 模块概述
2. 核心组件及职责
3. 组件间关系（如可能，用 ASCII 图）
4. 数据流
5. 关键设计决策及原因
6. 扩展点
```

### 21.6 调试类

#### 错误分析

```
分析这个错误：

错误信息：
[粘贴错误]

上下文：
- 触发场景：[描述]
- 环境：[开发/生产/测试]
- 最近的改动：[如有]

请：
1. 解释错误含义
2. 可能的原因（按可能性排序）
3. 如何定位
4. 如何修复
```

#### 性能分析

```
分析 @[文件/函数] 的性能问题：

现象：[慢/内存高/CPU 高]
数据量：[描述]
当前耗时/资源：[如有数据]

请：
1. 识别性能瓶颈
2. 解释原因
3. 提供优化方案（按效果排序）
4. 每个方案的权衡
```

---

## 二十二、进阶主题

### 22.1 多模态能力

Claude Code 支持图片输入，可用于：

#### 截图分析

```bash
# 粘贴截图（Ctrl+V）后
> 分析这个 UI 截图，指出布局问题

> 根据这个设计稿实现组件
```

#### 错误截图调试

```bash
# 粘贴错误截图后
> 这是控制台的错误截图，帮我分析问题
```

#### 图表理解

```bash
# 粘贴架构图后
> 理解这个架构图，然后帮我实现对应的代码结构
```

### 22.2 Claude Agent SDK

使用 Claude Agent SDK 可以构建自定义的代码智能体。

#### 安装

```bash
npm install @anthropic-ai/claude-agent-sdk
```

> **注意**：包名已从 `@anthropic-ai/claude-code-sdk` 变更为 `@anthropic-ai/claude-agent-sdk`

#### 依赖要求

- zod: ^4.0.0

#### 基本使用

```typescript
import { Agent } from '@anthropic-ai/claude-agent-sdk';

const agent = new Agent({
  model: 'sonnet',
  tools: ['Read', 'Edit', 'Bash'],
});

const result = await agent.run({
  prompt: '分析 src/ 目录的代码结构',
});
```

#### 自定义工具

```typescript
const customTool = {
  name: 'deploy',
  description: 'Deploy to staging',
  execute: async (params) => {
    // 自定义部署逻辑
  }
};

const agent = new Agent({
  tools: [...defaultTools, customTool],
});
```

### 22.3 Headless 模式与自动化

#### CI/CD 集成

```bash
# 非交互式运行
claude -p "检查代码风格问题" --output-format json

# 在 CI 中使用
- name: Code Review
  run: |
    RESULT=$(claude -p "审查 src/ 的安全问题" --model haiku)
    echo "$RESULT" >> $GITHUB_STEP_SUMMARY
```

#### 批量处理

```bash
# 批量修复文件
for file in src/**/*.ts; do
  claude -p "修复 @$file 的 TypeScript 错误" --model haiku
done
```

### 22.4 扩展思考（Extended Thinking）

#### 何时使用

- 复杂架构设计
- 多步推理问题
- 需要权衡多个方案
- Debug 复杂问题

#### 触发方式

```bash
# 前缀触发
> think: 分析这个性能问题
> ultrathink: 设计微服务拆分方案

# 全局启用
/config  # 选择 "Enable extended thinking"
```

#### 思考预算控制

```bash
# 环境变量
export MAX_THINKING_TOKENS=16000

# 或在 settings.json
{
  "env": {
    "MAX_THINKING_TOKENS": "16000"
  }
}
```

### 22.5 远程开发环境

#### SSH 远程使用

```bash
# 在远程服务器安装
ssh user@server "npm install -g @anthropic-ai/claude-code"

# 连接后使用
ssh user@server
cd /path/to/project
claude
```

#### Dev Container 配置

```json
// .devcontainer/devcontainer.json
{
  "name": "Dev with Claude",
  "image": "mcr.microsoft.com/devcontainers/typescript-node:18",
  "postCreateCommand": "npm install -g @anthropic-ai/claude-code",
  "customizations": {
    "vscode": {
      "settings": {
        "terminal.integrated.defaultProfile.linux": "bash"
      }
    }
  }
}
```

### 22.6 企业级部署

#### 代理配置

```bash
export HTTPS_PROXY=http://proxy.company.com:8080
export HTTP_PROXY=http://proxy.company.com:8080
claude
```

#### 私有化部署

```bash
# 自定义 API 端点
export ANTHROPIC_API_URL=https://api.internal.company.com
claude
```

#### 使用限制与配额

```json
// 企业级 settings.json
{
  "permissions": {
    "defaultMode": "normal"
  },
  "env": {
    "MAX_THINKING_TOKENS": "10000",
    "CLAUDE_CODE_COST_WARNING_THRESHOLD": "10.00"
  }
}
```

### 22.7 Agent Teams (实验性)

Agent Teams 允许多个 Claude Code 会话协调工作，并行处理复杂任务。

#### 工作原理

```
┌─────────────────────────────────────────────────────┐
│                   Agent Teams                        │
├─────────────┬─────────────┬─────────────────────────┤
│  Agent A    │  Agent B    │  Agent C                │
│ (前端重构)  │ (后端 API)  │ (测试编写)              │
├─────────────┴─────────────┴─────────────────────────┤
│            共享任务列表 & 依赖管理                    │
└─────────────────────────────────────────────────────┘
```

#### 核心特性

- **多会话协调**：多个 Claude Code 实例并行工作
- **共享任务列表**：通过 Task System 共享任务状态和依赖
- **TeammateIdle 事件**：当队友代理空闲时触发 Hook，实现动态任务分配

#### 适用场景

| 场景 | 说明 |
|------|------|
| 大型重构 | 多个代理分别处理不同模块 |
| 前后端并行 | 一个代理写 API，另一个写前端 |
| 并行调试 | 多个代理同时调查不同可能的原因 |
| 测试生成 | 一个代理写代码，另一个同步写测试 |

#### 使用方式

```bash
# 在多个终端窗口中启动 Claude Code
# 它们会通过共享的任务列表协调工作

# 终端 1
claude
> 重构 auth 模块的数据层

# 终端 2
claude
> 重构 auth 模块的 API 层（等数据层完成后开始）
```

---

## 附录 A：快捷键完整列表

| 快捷键 | 功能 | 备注 |
|--------|------|------|
| `Ctrl+C` | 取消当前操作 | |
| `Ctrl+D` | 退出 Claude Code | |
| `Ctrl+L` | 清屏 | 保留对话历史 |
| `Ctrl+O` | 切换详细输出 | 显示工具调用 |
| `Ctrl+R` | 搜索命令历史 | |
| `Ctrl+T` | 打开任务列表 | |
| `Ctrl+B` | 查看后台任务 | |
| `Ctrl+G` | 在外部编辑器中打开 | |
| `Ctrl+V` / `Alt+V` | 粘贴图片 | macOS / Windows |
| `Shift+Tab` | 切换权限模式 | |
| `Shift+Enter` | 换行 | 多行输入 |
| `Option+Enter` | 多行输入 | macOS |
| `\` + `Enter` | 多行输入 | 通用 |
| `Esc + Esc` | 撤销代码改动 | |
| `Option+P` / `Alt+P` | 切换模型 | |
| `Up/Down` | 导航历史 | |

---

## 附录 B：常用斜杠命令速查

| 命令 | 用途 | 常用场景 |
|------|------|---------|
| `/help` | 查看帮助 | 随时 |
| `/cost` | 查看成本 | 每个任务后 |
| `/compact` | 压缩上下文 | 会话过长时 |
| `/clear` | 清除对话 | 切换任务时 |
| `/model` | 切换模型 | 调整成本/能力 |
| `/config` | 打开设置 | 配置偏好 |
| `/memory` | 编辑记忆 | 更新项目信息 |
| `/export` | 导出对话 | 保存重要讨论 |
| `/rename` | 命名会话 | 便于恢复 |
| `/resume` | 恢复会话 | 继续之前工作 |
| `/fast` | 切换快速模式 | 加速输出 |
| `/tasks` | 查看任务列表 | 管理工作进度 |
| `/teleport` | 转移到 Web | 切换到浏览器 |
| `/keybindings` | 快捷键配置 | 自定义按键 |
| `/hooks` | 管理 Hooks | 配置自动化 |

---

## 附录 C：一页速查表

```
╔══════════════════════════════════════════════════════════════════════╗
║                      CLAUDE CODE 速查表                               ║
╠══════════════════════════════════════════════════════════════════════╣
║ 启动方式                                                              ║
║   claude              # 交互式                                        ║
║   claude -c           # 继续上次                                      ║
║   claude -p "..."     # 单次查询                                      ║
║   cat x | claude -p   # 管道模式                                      ║
╠══════════════════════════════════════════════════════════════════════╣
║ 核心快捷键                                                            ║
║   Esc+Esc     撤销改动    Ctrl+C      取消操作                        ║
║   Shift+Tab   切权限      Ctrl+T      任务列表                        ║
║   Ctrl+B      后台任务    Ctrl+G      外部编辑器                      ║
╠══════════════════════════════════════════════════════════════════════╣
║ 快捷前缀                                                              ║
║   @file       引用文件    !cmd        执行命令                        ║
║   #note       写入记忆    /cmd        斜杠命令                        ║
╠══════════════════════════════════════════════════════════════════════╣
║ 必知命令                                                              ║
║   /cost       查成本      /compact    压缩上下文                      ║
║   /clear      清历史      /model      切模型                          ║
║   /memory     编辑记忆    /fast       快速模式                        ║
║   /tasks      任务列表    /teleport   转移会话                        ║
╠══════════════════════════════════════════════════════════════════════╣
║ 模型选择                                                              ║
║   Haiku 4.5  便宜快速 → 简单查询                                      ║
║   Sonnet 4.5 均衡    → 日常开发                                       ║
║   Opus 4.6   强大    → 复杂任务                                       ║
╠══════════════════════════════════════════════════════════════════════╣
║ 配置文件位置                                                          ║
║   ./CLAUDE.md              项目记忆（提交）                           ║
║   ./CLAUDE.local.md        个人笔记（不提交）                         ║
║   ~/.claude/projects/      自动记忆（Auto Memory）                    ║
║   ./.claude/settings.json  项目配置                                   ║
║   ~/.claude/settings.json  全局配置                                   ║
╚══════════════════════════════════════════════════════════════════════╝
```

---

*本指南基于 Claude Code 官方文档整理（2026年2月更新），更多信息请访问 [claude.com/claude-code](https://claude.com/claude-code)*
