# 项目名称

> 简要描述：一句话说明项目用途

## 快速命令

```bash
# 开发
pnpm dev             # 启动开发服务器（Next.js 16 默认使用 Turbopack）
pnpm build           # 构建生产版本
pnpm test            # 运行测试
pnpm lint            # 代码检查（ESLint 直接配置，Next.js 16 已移除 next lint）
pnpm format          # 格式化代码

# Git 工作流
pnpm commit          # 提交代码（如使用 commitizen）
```

## 技术栈

- **语言**: TypeScript 5.9
- **框架**: React 19 / Next.js 16
- **状态管理**: Zustand 5
- **样式**: Tailwind CSS v4
- **测试**: Vitest 4 + React Testing Library
- **包管理**: pnpm 10

## 项目结构

```
src/
├── app/           # Next.js App Router 页面
├── components/    # React 组件
│   ├── ui/        # 基础 UI 组件
│   └── features/  # 业务功能组件
├── hooks/         # 自定义 Hooks
├── lib/           # 工具函数和配置
├── types/         # TypeScript 类型定义
├── styles/        # 全局样式（Tailwind v4: CSS-first 配置，使用 @import "tailwindcss"，无需 tailwind.config.js）
└── proxy.ts       # 请求代理（Next.js 16 替代 middleware.ts 处理代理逻辑）
```

## 代码规范

- 2 空格缩进
- 使用函数式组件 + Hooks
- 组件文件使用 PascalCase: `UserProfile.tsx`
- 工具函数使用 camelCase: `formatDate.ts`
- 类型定义使用 `interface` 优先于 `type`
- 导入顺序：外部库 → 内部模块 → 类型 → 样式
- React 19: ref 直接作为 props 传递，不再需要 forwardRef
- React 19: 优先使用 Server Components，仅在需要交互时使用 `"use client"`
- Next.js 16: `cookies()`、`headers()`、`params` 等动态 API 必须 await
- Tailwind v4: 使用 CSS-first 配置，通过 `@import "tailwindcss"` 引入，无需 `tailwind.config.js`

## 关键文件

- `src/lib/api.ts` - API 请求封装
- `src/lib/auth.ts` - 认证逻辑
- `src/types/index.ts` - 全局类型定义

## 环境变量

```bash
# .env.local (不要提交)
DATABASE_URL=          # 数据库连接
NEXT_PUBLIC_API_URL=   # API 地址
AUTH_SECRET=           # 认证密钥
```

## 常见任务指引

### 添加新页面
1. 在 `src/app/` 下创建目录
2. 添加 `page.tsx` 和 `layout.tsx`（如需要）

### 添加新组件
1. 在 `src/components/` 下创建文件
2. 导出到 `index.ts`
3. 编写对应测试文件 `*.test.tsx`

### 添加 API 端点
1. 在 `src/app/api/` 下创建 `route.ts`
2. 使用 `src/lib/api.ts` 中的工具函数

## 注意事项

- 不要直接修改 `node_modules` 或 `dist` 目录
- 敏感信息放在 `.env.local`，不要硬编码
- 提交前运行 `pnpm lint && pnpm test`
- PR 需要通过 CI 检查才能合并

## 团队约定

- 分支命名: `feature/xxx`, `fix/xxx`, `refactor/xxx`
- Commit 格式: `type(scope): message`
- PR 需要至少 1 人 review
