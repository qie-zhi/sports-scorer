# 项目架构设计

## 整体架构

```
SportsScorer/
│
├── 📁 src/                          # 源代码
│   ├── 📁 app/                      # 应用入口 & 路由
│   │   ├── App.tsx                  # 根组件
│   │   └── navigation.tsx           # 导航配置
│   │
│   ├── 📁 features/                 # 功能模块（按运动类型划分）
│   │   ├── 📁 volleyball/           # 🏐 排球模块
│   │   │   ├── screens/
│   │   │   │   ├── VolleyballScorerScreen.tsx   # 计分主界面
│   │   │   │   └── VolleyballRulesScreen.tsx    # 规则说明
│   │   │   ├── components/
│   │   │   │   ├── VolleyballScoreBoard.tsx     # 排球计分板
│   │   │   │   └── SetIndicator.tsx             # 局分指示器
│   │   │   └── hooks/
│   │   │       └── useVolleyballRules.ts        # 排球计分规则逻辑
│   │   │
│   │   ├── 📁 basketball/           # 🏀 篮球模块（后期扩展）
│   │   └── 📁 football/             # ⚽ 足球模块（后期扩展）
│   │
│   ├── 📁 shared/                   # 共享组件
│   │   ├── components/
│   │   │   ├── ScoreButton.tsx      # 计分按钮（+1, +2, +3, -1）
│   │   │   ├── TeamCard.tsx         # 队伍信息卡片
│   │   │   ├── MatchTimer.tsx       # 比赛计时器
│   │   │   ├── UndoButton.tsx       # 撤销按钮
│   │   │   ├── SportSelector.tsx    # 运动类型选择器
│   │   │   └── ConfirmDialog.tsx    # 确认对话框
│   │   ├── hooks/
│   │   │   ├── useMatch.ts          # 比赛核心逻辑 Hook
│   │   │   ├── useScore.ts          # 计分逻辑 Hook
│   │   │   └── useUndo.ts           # 撤销功能 Hook
│   │   └── types/
│   │       ├── match.ts             # Match 类型定义
│   │       ├── sport.ts             # Sport 类型定义
│   │       └── score.ts             # Score 类型定义
│   │
│   ├── 📁 core/                     # 核心基础设施
│   │   ├── storage/
│   │   │   ├── matchStorage.ts      # 比赛数据持久化
│   │   │   └── configStorage.ts     # 用户设置持久化
│   │   ├── sharing/
│   │   │   ├── exportMatch.ts       # 导出比赛数据为 JSON
│   │   │   ├── importMatch.ts       # 导入 JSON 恢复比赛
│   │   │   └── shareFile.ts         # 系统分享面板
│   │   └── config/
│   │       ├── sports.ts            # 各运动计分规则配置
│   │       └── constants.ts         # 全局常量
│   │
│   └── 📁 assets/                   # 静态资源
│       └── icons/                   # 运动图标
│
├── 📁 __tests__/                     # 测试文件
│
├── 📄 CLAUDE.md                      # Claude Code 项目文档
├── 📄 package.json
├── 📄 tsconfig.json
├── 📄 babel.config.js
├── 📄 metro.config.js
└── 📄 app.json
```

---

## 架构设计原则

### 1. 模块化（Feature-based）

每个运动类型是独立的功能模块，包含自己的：
- **screens/** — 页面组件
- **components/** — 专用 UI 组件
- **hooks/** — 专用业务逻辑

新增运动 = 复制模板 → 修改计分规则 → 完成

### 2. 分层清晰

```
┌────────────────────────┐
│     UI Layer (screens) │  ← 用户看到的界面
├────────────────────────┤
│   Logic Layer (hooks)  │  ← 计分规则、撤销、计时
├────────────────────────┤
│    Core Layer (core)   │  ← 存储、分享、配置
├────────────────────────┤
│   Shared Layer (shared)│  ← 通用组件和类型
└────────────────────────┘
```

### 3. 依赖方向

```
features/ ──→ shared/ ──→ core/
  （可以依赖）  （可以依赖）  （不依赖任何人）
```

- `core/` 不依赖任何模块
- `shared/` 只依赖 `core/`
- `features/` 可以依赖 `shared/` 和 `core/`

### 4. 扩展预留

每个扩展点都已定义好接口：

```typescript
// 新增运动只需实现这个接口
interface SportConfig {
  name: string;
  maxPoints: number | null;    // null = 无上限（篮球）
  setsToWin: number;
  pointValue: number[];        // 支持的加分值
  icon: string;
}
```

---

## 数据流

```
用户点击 [+1]
    │
    ▼
ScoreButton 触发 onPress
    │
    ▼
useMatch hook → addScore(team, value)
    │
    ├── 更新 score 状态（即时 UI 响应）
    ├── 检查是否赢得本局（checkSetWin）
    ├── 检查是否赢得比赛（checkMatchWin）
    └── 写入 AsyncStorage（持久化）
        │
        ▼
    UI 自动刷新 ← Zustand 状态变更
```

---

## 为什么这样设计？

| 决策 | 原因 |
|------|------|
| Feature-based 而非 Layer-based | 每个运动独立，互不干扰，方便扩展 |
| Zustand 而非 Redux | 状态简单，不需要 Redux 的复杂度 |
| Hook 封装业务逻辑 | UI 和逻辑分离，测试更方便 |
| 计分规则配置化 | 改配置文件就能适配新运动 |
