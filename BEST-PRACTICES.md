# AI 编程最佳实践

## 1. 项目结构

保持清晰的项目结构能让 AI 更好地理解你的代码：

```
my-project/
├── src/
│   ├── components/     # UI 组件
│   ├── services/       # 业务逻辑
│   ├── utils/          # 工具函数
│   ├── types/          # TypeScript 类型定义
│   └── index.ts        # 入口文件
├── tests/              # 测试文件
├── docs/               # 文档
├── package.json
├── tsconfig.json
└── README.md
```

## 2. 用好 CLAUDE.md

每个项目都应该有一个 `CLAUDE.md` 文件，告诉 Claude：

- 项目是什么、用什么技术栈
- 构建/测试/运行命令
- 代码规范和约定
- 项目特定的注意事项

**示例 CLAUDE.md：**
```markdown
# My App

## 技术栈
- Frontend: React 18 + TypeScript + Tailwind CSS
- Backend: Express + Prisma + PostgreSQL
- Testing: Jest + Playwright

## 常用命令
- 开发: `npm run dev`
- 构建: `npm run build`
- 测试: `npm test`
- Lint: `npm run lint`

## 规范
- 文件命名用 kebab-case
- 组件用 PascalCase
- 使用 async/await 而非 Promise.then
- 每个函数添加 JSDoc 注释
```

## 3. 与 AI 协作的黄金法则

### ✅ DO（应该做）

- **小步快跑**：每次只改一小部分，验证后再继续
- **明确验收标准**：告诉 Claude "完成的标准是 npm test 全部通过"
- **提供反馈**：遇到问题时描述具体的错误信息和现象
- **用 Git 保驾护航**：每次 AI 修改前先 commit，方便回滚
- **读懂了再用**：确保理解 AI 生成的代码再合入主分支

### ❌ DON'T（不应该做）

- **不要盲目信任**：AI 生成的代码可能有 Bug，需要审查
- **不要一次性要求太多**：复杂需求拆成小步骤
- **不要跳过测试**：AI 生成的代码更需要测试覆盖
- **不要在上下文太脏时继续**：用 `/clear` 或 `/compact` 刷新上下文
- **不要硬记状态**：让 AI 自己读代码了解当前状态

## 4. 版本控制策略

```bash
# 在让 AI 修改代码前，先提交当前状态
git add -A && git commit -m "checkpoint: before AI refactoring"

# AI 修改后检查 diff
git diff

# 如果不满意，可以回滚
git reset --hard HEAD

# 如果满意，提交
git add -A && git commit -m "feat: AI-assisted refactoring of user module"
```

## 5. 安全注意事项

- 不要将 API 密钥、密码放到对话中
- 用 `.env` 文件管理敏感信息，并在 `.gitignore` 中添加
- AI 生成的代码上线前应经过安全审查
- 注意 AI 可能引入的依赖安全漏洞

## 6. 高效使用模式

| 场景 | 建议 |
|------|------|
| **学习新代码库** | 先用 Claude 生成概要，再逐模块深入 |
| **快速原型** | 直接描述功能，让 Claude 生成初版代码 |
| **Bug 修复** | 描述症状 → Claude 定位 → 修复 → 测试 |
| **代码审查** | 让 Claude 检查代码质量和潜在问题 |
| **技术调研** | 让 Claude 搜索和总结最新技术方案 |
| **文档编写** | 生成 API 文档、README、注释 |
