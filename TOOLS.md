# Claude Code 工具与命令速查表

## 核心工具（Tools）

Claude Code 提供以下核心工具来帮助你编程：

### 📖 文件读取工具

| 工具 | 用途 | 示例 |
|------|------|------|
| **Read** | 读取文件内容 | 读取代码、配置、文档 |
| **Glob** | 按模式匹配文件 | `**/*.ts` 查找所有 TS 文件 |
| **Grep** | 搜索文件内容 | 搜索函数名、变量、字符串 |

### ✏️ 文件编辑工具

| 工具 | 用途 | 示例 |
|------|------|------|
| **Write** | 创建/覆盖文件 | 创建新文件 |
| **Edit** | 精确字符串替换 | 修改文件中的某一段代码 |

### 💻 命令执行工具

| 工具 | 用途 | 示例 |
|------|------|------|
| **Bash** | 运行 Shell 命令 | `npm test`、`git status` |

### 🔧 高级工具

| 工具 | 用途 |
|------|------|
| **Agent** | 启动子代理处理复杂多步骤任务 |
| **TaskCreate** | 创建任务列表跟踪进度 |
| **AskUserQuestion** | 向用户提问澄清需求 |
| **EnterPlanMode** | 进入计划模式，先设计再实现 |
| **WebFetch** | 获取网页内容 |
| **WebSearch** | 搜索互联网信息 |

## 常用 / 命令（Slash Commands）

| 命令 | 功能 |
|------|------|
| `/help` | 查看帮助信息 |
| `/config` | 查看/修改配置 |
| `/memory` | 管理持久化记忆 |
| `/clear` | 清除对话上下文 |
| `/compact` | 压缩对话上下文 |
| `/code-review` | 代码审查 |
| `/simplify` | 代码简化/重构 |
| `/init` | 为项目创建 CLAUDE.md |
| `/loop` | 定时重复执行命令 |
| `/review` | 审查 PR |
| `/security-review` | 安全审查 |
| `/run` | 运行应用 |
| `/verify` | 验证代码更改是否正确 |

## 常用 Bash 操作

```bash
# 安装依赖
npm install <package>

# 运行测试
npm test

# 代码格式化
npx prettier --write "src/**/*.ts"

# 代码检查
npx eslint "src/**/*.ts"

# 查看 Git 状态
git status

# 查看最近提交
git log --oneline -10

# 创建新分支
git checkout -b feature/my-feature
```

## 提示技巧

- **直接说你要什么**，Claude Code 会选择合适的工具
- 可以一次让 Claude 做多件事，它会并行处理
- 对于复杂任务，可以要求"先计划再动手"
- 如果输出太长，说"继续"让 Claude 接着输出
