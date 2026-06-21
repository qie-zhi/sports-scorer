# 数据模型设计

## 核心模型

### Match（比赛）

```typescript
/**
 * 一场比赛的完整记录
 */
interface Match {
  /** 唯一标识符，UUID v4 */
  id: string;

  /** 运动类型 */
  sportId: SportId;

  /** A 队信息 */
  teamA: Team;

  /** B 队信息 */
  teamB: Team;

  /** 所有得分记录，按时间排序 */
  scoreLog: ScoreRecord[];

  /** 当前进行到第几局（从 1 开始） */
  currentSet: number;

  /** 比赛状态 */
  status: MatchStatus;

  /** 比赛开始的 Unix 时间戳 */
  startedAt: number;

  /** 比赛结束的 Unix 时间戳 */
  endedAt?: number;

  /** 比赛地点（可选） */
  location?: string;

  /** 备注（可选） */
  notes?: string;
}
```

### Team（队伍）

```typescript
/**
 * 一支队伍
 */
interface Team {
  /** 队伍名称 */
  name: string;

  /** 各局得分数组，索引 0 代表第 1 局 */
  setScores: number[];

  /** 当前局的分数 */
  currentScore: number;
}
```

### ScoreRecord（得分记录）

```typescript
/**
 * 一次得分操作记录
 * 用于撤销功能、比赛回放和统计
 */
interface ScoreRecord {
  /** 得分时间戳 */
  timestamp: number;

  /** 哪支队伍得分 */
  team: 'A' | 'B';

  /** 得分值（排球=1，篮球=2或3） */
  value: number;

  /** 发生在第几局 */
  setNumber: number;

  /** 得分球员编号（后期扩展） */
  scorerId?: string;

  /** 得分类型（后期扩展：扣球/发球/罚球等） */
  scoreType?: string;
}
```

### MatchStatus（比赛状态）

```typescript
/** 比赛状态枚举 */
type MatchStatus = 
  | 'setup'      // 设置中（正在输入队名等）
  | 'live'       // 进行中
  | 'paused'     // 暂停
  | 'finished';  // 已结束
```

### SportConfig（运动计分规则）

```typescript
/**
 * 运动计分规则配置
 * 扩展新运动只需添加一个新的配置对象
 */
interface SportConfig {
  /** 运动 ID */
  id: SportId;

  /** 运动名称 */
  name: string;

  /** 运动图标 */
  icon: string;

  /** 每局最高分（可被追平规则覆盖） */
  maxPoints: number | null;

  /** 获胜需要赢几局 */
  setsToWin: number;

  /** 支持的加分值（默认 [1]） */
  pointValues: number[];

  /** 是否必须有 2 分领先优势 */
  mustWinByTwo: boolean;

  /** 是否不限局数上限 */
  unlimitedSets: boolean;
}

type SportId = 'volleyball' | 'basketball' | 'football';
```

---

## 预设运动配置

```typescript
const SPORT_CONFIGS: Record<SportId, SportConfig> = {
  volleyball: {
    id: 'volleyball',
    name: '排球',
    icon: '🏐',
    maxPoints: 25,          // 25 分制
    setsToWin: 3,           // 五局三胜
    pointValues: [1],       // 每次只能+1
    mustWinByTwo: true,     // 必须领先 2 分
    unlimitedSets: false,
  },
  basketball: {
    id: 'basketball',
    name: '篮球',
    icon: '🏀',
    maxPoints: null,        // 无上限
    setsToWin: 1,           // 单局决胜
    pointValues: [1, 2, 3], // 罚球/两分/三分
    mustWinByTwo: false,
    unlimitedSets: false,
  },
  football: {
    id: 'football',
    name: '足球',
    icon: '⚽',
    maxPoints: null,        // 无上限
    setsToWin: 1,           // 单局决胜
    pointValues: [1],       // 每次+1
    mustWinByTwo: false,
    unlimitedSets: false,
  },
};
```

---

## 胜负判定逻辑

```
每次加分后执行：
┌──────────────────────────┐
│ 1. 检查本局分数           │
│    A队分数 >= maxPoints?  │
│    B队分数 >= maxPoints?  │
│    且领先 >= 2 分?         │
│    ↓ YES                  │
│ 2. 该队赢得本局           │
│    setScores.push(score)  │
│    currentScore = 0       │
│    currentSet++           │
│    ↓                      │
│ 3. 检查比赛是否结束       │
│    某队赢得 set ≥ setsToWin?│
│    ↓ YES                  │
│ 4. 比赛结束               │
│    status = 'finished'    │
└──────────────────────────┘
```
