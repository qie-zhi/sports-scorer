# AI 编程实战案例

## 案例 1：创建一个 Node.js API 项目

### 步骤 1：初始化项目
```
帮我创建一个 Express + TypeScript 的 REST API 项目，
包含以下功能：
- 用户注册和登录（JWT 认证）
- 文章的 CRUD 操作
- 数据库使用 SQLite
请先规划项目结构，再逐步实现。
```

### 步骤 2：添加测试
```
为 src/services/articleService.ts 中的每个方法编写单元测试，
使用 Jest，mock 数据库调用。
```

### 步骤 3：添加错误处理
```
给所有 API 路由添加统一的错误处理中间件，
错误格式统一为 { error: string, code: number }。
```

## 案例 2：重构现有代码

### 重构前
```
下面的代码太长了，帮我重构它：

<a large code block>
要求：
1. 保持功能完全不变
2. 拆分成多个可测试的函数
3. 抽离出可复用的逻辑
</a large code block>
```

### 修改后的迭代
```
重构后的 createUser 函数中，邮箱验证逻辑太紧耦合了，
帮我把它抽成一个独立的 validateEmail 函数放到 src/utils/validators.ts 中。
```

## 案例 3：调试

```
我的应用在用户提交表单后会白屏，
控制台有 "Cannot read property 'name' of undefined" 错误。
请帮我查看 src/components/UserForm.tsx 找到问题并修复。
```

## 案例 4：生成配置文件

```
帮我创建一个 .github/workflows/ci.yml 文件，
用于 GitHub Actions CI：
- 在 push 到 main 和 PR 时触发
- 需要 Node.js 18、安装依赖、运行 lint 和 test
```

## 案例 5：数据库迁移

```
我有一个 `users` 表，当前结构是：
- id (INTEGER PRIMARY KEY)
- email (TEXT)
- name (TEXT)

我想添加一个 `created_at` 字段和一个 `role` 字段（默认 'user'）。
帮我写 SQLite 的迁移脚本。
```

## 常用提示模板

### 模板 1：新功能开发
```
我需要实现 [功能名称]。
当前项目使用 [技术栈]。
相关文件在 [文件路径]。

具体要求：
1. [要求1]
2. [要求2]
3. [要求3]

请先告诉我你的实现计划，等我确认后再开始写代码。
```

### 模板 2：Bug 修复
```
我遇到了一个 Bug：[描述现象]
复现步骤：
1. [步骤1]
2. [步骤2]
预期行为：[应该是怎样]
实际行为：[实际是怎样]

请帮我找到问题的根源并修复。
```

### 模板 3：代码审查
```
请审查 [文件路径] 的代码，关注以下几个方面：
- 潜在的 Bug
- 性能问题
- 安全问题
- 代码可读性
- 是否遵循 [项目] 的最佳实践
```

### 模板 4：学习/理解代码
```
请解释 [文件路径] 中 [函数/类] 的作用和工作原理。
我是一个 [技术水平] 的开发者，请用适合我水平的语言解释。
```
