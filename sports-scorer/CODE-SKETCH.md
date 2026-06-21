# 核心代码草图

以下是用 TypeScript 编写的核心代码示例，帮助理解项目的关键逻辑。

---

## 1. 状态管理 Store（Zustand）

```typescript
// src/shared/hooks/useMatchStore.ts
import { create } from 'zustand';
import { persist, createJSONStorage } from 'zustand/middleware';
import AsyncStorage from '@react-native-async-storage/async-storage';
import { Match, MatchStatus, SportId } from '../types';

interface MatchState {
  // 当前比赛
  currentMatch: Match | null;

  // 历史比赛
  history: Match[];

  // 操作方法
  createMatch: (sport: SportId, teamAName: string, teamBName: string) => void;
  addScore: (team: 'A' | 'B', value: number) => void;
  subtractScore: (team: 'A' | 'B') => void;
  undoLastScore: () => void;
  nextSet: () => void;
  finishMatch: () => void;
  deleteMatch: (id: string) => void;
}

export const useMatchStore = create<MatchState>()(
  persist(
    (set, get) => ({
      currentMatch: null,
      history: [],

      createMatch: (sport, teamAName, teamBName) => {
        const match: Match = {
          id: generateId(),
          sportId: sport,
          teamA: { name: teamAName || 'A队', setScores: [], currentScore: 0 },
          teamB: { name: teamBName || 'B队', setScores: [], currentScore: 0 },
          scoreLog: [],
          currentSet: 1,
          status: 'live',
          startedAt: Date.now(),
        };
        set({ currentMatch: match });
      },

      addScore: (team, value) => {
        const match = get().currentMatch;
        if (!match || match.status !== 'live') return;

        const updated = { ...match };
        if (team === 'A') {
          updated.teamA = { ...updated.teamA, currentScore: updated.teamA.currentScore + value };
        } else {
          updated.teamB = { ...updated.teamB, currentScore: updated.teamB.currentScore + value };
        }

        // 记录操作日志（用于撤销）
        updated.scoreLog = [...updated.scoreLog, {
          timestamp: Date.now(),
          team,
          value,
          setNumber: updated.currentSet,
        }];

        // 检查是否赢得本局
        const config = getSportConfig(updated.sportId);
        const teamScore = team === 'A' ? updated.teamA.currentScore : updated.teamB.currentScore;
        const opponentScore = team === 'A' ? updated.teamB.currentScore : updated.teamA.currentScore;

        if (config.maxPoints && teamScore >= config.maxPoints) {
          if (!config.mustWinByTwo || teamScore - opponentScore >= 2) {
            // 赢得本局
            updated.teamA.setScores = [...updated.teamA.setScores, updated.teamA.currentScore];
            updated.teamB.setScores = [...updated.teamB.setScores, updated.teamB.currentScore];
            updated.teamA.currentScore = 0;
            updated.teamB.currentScore = 0;
            updated.currentSet++;

            // 检查是否赢得比赛
            const setsWonA = updated.teamA.setScores.filter((s, i) => s > updated.teamB.setScores[i]).length;
            const setsWonB = updated.teamB.setScores.filter((s, i) => s > updated.teamA.setScores[i]).length;

            if (setsWonA >= config.setsToWin || setsWonB >= config.setsToWin) {
              updated.status = 'finished';
              updated.endedAt = Date.now();
            }
          }
        }

        set({ currentMatch: updated });
      },

      // ... 其他方法
    }),
    {
      name: 'match-storage',
      storage: createJSONStorage(() => AsyncStorage),
    }
  )
);
```

---

## 2. 计分规则配置

```typescript
// src/core/config/sports.ts
import { SportConfig, SportId } from '../../shared/types';

export const SPORT_CONFIGS: Record<SportId, SportConfig> = {
  volleyball: {
    id: 'volleyball',
    name: '排球',
    icon: '🏐',
    maxPoints: 25,
    setsToWin: 3,
    pointValues: [1],
    mustWinByTwo: true,
    unlimitedSets: false,
  },
  basketball: {
    id: 'basketball',
    name: '篮球',
    icon: '🏀',
    maxPoints: null,
    setsToWin: 1,
    pointValues: [1, 2, 3],
    mustWinByTwo: false,
    unlimitedSets: false,
  },
  football: {
    id: 'football',
    name: '足球',
    icon: '⚽',
    maxPoints: null,
    setsToWin: 1,
    pointValues: [1],
    mustWinByTwo: false,
    unlimitedSets: false,
  },
};

export function getSportConfig(sportId: SportId): SportConfig {
  return SPORT_CONFIGS[sportId];
}
```

---

## 3. 计分板组件

```typescript
// src/shared/components/TeamCard.tsx
import React from 'react';
import { View, Text, StyleSheet } from 'react-native';

interface TeamCardProps {
  name: string;
  score: number;
  setsWon: number;
  isLeading: boolean;
  color: 'red' | 'blue';
}

export const TeamCard: React.FC<TeamCardProps> = ({
  name,
  score,
  setsWon,
  isLeading,
  color,
}) => {
  return (
    <View style={[styles.card, isLeading && styles.leading]}>
      <Text style={styles.name}>{name}</Text>
      <Text style={[styles.score, { color }]}>{score}</Text>
      <Text style={styles.sets}>已赢 {setsWon} 局</Text>
      {/* 局分圆点 */}
      <View style={styles.dots}>
        {[...Array(setsWon)].map((_, i) => (
          <View key={i} style={[styles.dot, { backgroundColor: color }]} />
        ))}
      </View>
    </View>
  );
};

const styles = StyleSheet.create({
  card: {
    flex: 1,
    alignItems: 'center',
    padding: 20,
  },
  leading: {
    transform: [{ scale: 1.05 }],
  },
  score: {
    fontSize: 64,
    fontWeight: 'bold',
  },
  // ... 更多样式
});
```

---

## 4. 共享数据格式（导出/导入）

```typescript
// src/core/sharing/exportMatch.ts

/**
 * 导出的 JSON 文件格式
 * 可以分享给任何人，他们导入即可查看比赛结果
 */
interface ExportedMatch {
  // 导出元数据
  version: '1.0';
  exportedAt: number;
  appName: 'SportsScorer';

  // 比赛数据
  match: {
    sport: string;
    teamA: string;
    teamB: string;
    status: string;
    startedAt: number;
    endedAt?: number;

    // 各局比分
    sets: Array<{
      setNumber: number;
      scoreA: number;
      scoreB: number;
    }>;
  };
}

// 导出示例输出：
// {
//   "version": "1.0",
//   "exportedAt": 1719000000000,
//   "appName": "SportsScorer",
//   "match": {
//     "sport": "排球",
//     "teamA": "战狼队",
//     "teamB": "飞虎队",
//     "status": "finished",
//     "startedAt": 1718990000000,
//     "endedAt": 1718995000000,
//     "sets": [
//       { "setNumber": 1, "scoreA": 25, "scoreB": 18 },
//       { "setNumber": 2, "scoreA": 23, "scoreB": 25 },
//       { "setNumber": 3, "scoreA": 25, "scoreB": 20 },
//       { "setNumber": 4, "scoreA": 25, "scoreB": 22 }
//     ]
//   }
// }
```

---

## 5. 关键 Hook 用法示例

```typescript
// 在计分页面中使用

import { useMatchStore } from '../shared/hooks/useMatchStore';
import { getSportConfig } from '../core/config/sports';

function ScorerScreen() {
  const { currentMatch, addScore, undoLastScore, finishMatch } = useMatchStore();

  if (!currentMatch) return null;

  const config = getSportConfig(currentMatch.sportId);

  return (
    <View>
      {/* 运动标题 */}
      <Text style={styles.sportTitle}>
        {config.icon} {config.name}
      </Text>

      {/* 两队分数 */}
      <View style={styles.scoreRow}>
        <TeamCard
          name={currentMatch.teamA.name}
          score={currentMatch.teamA.currentScore}
          setsWon={countSetsWon(currentMatch, 'A')}
          isLeading={currentMatch.teamA.currentScore > currentMatch.teamB.currentScore}
          color="#FF4444"
        />
        <TeamCard
          name={currentMatch.teamB.name}
          score={currentMatch.teamB.currentScore}
          setsWon={countSetsWon(currentMatch, 'B')}
          isLeading={currentMatch.teamB.currentScore > currentMatch.teamA.currentScore}
          color="#4488FF"
        />
      </View>

      {/* 计分按钮（根据运动类型显示不同分值） */}
      <View style={styles.buttonRow}>
        {config.pointValues.map(val => (
          <ScoreButton
            key={val}
            label={`A队 +${val}`}
            onPress={() => addScore('A', val)}
            color="#FF4444"
          />
        ))}
      </View>

      {/* 底部操作 */}
      <View style={styles.actionRow}>
        <UndoButton onPress={undoLastScore} />
        <FinishButton onPress={finishMatch} />
      </View>
    </View>
  );
}
```

---

## 把这些代码用 Claude Code 变成真实项目

在 `C:\claude-code-learning\SportsScorer\` 目录下，你可以这样和 Claude 对话：

> "帮我初始化一个 React Native 项目，按照 CLAUDE.md 中的架构创建目录结构"

> "帮我实现 useMatchStore，参考 CODE-SKETCH.md 中的代码"

> "帮我创建 TeamCard 组件，要求支持红色和蓝色主题"

Claude 会直接创建真实的代码文件，而不仅仅是草图！
