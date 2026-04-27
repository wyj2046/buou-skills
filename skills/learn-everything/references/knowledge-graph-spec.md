# 知识图谱规格

## JSON Schema

`knowledge-graph.json` 存储在学习项目根目录。

```json
{
  "domain": "领域名称",
  "nodes": [
    {
      "id": "concept-id-kebab-case",
      "name": "概念中文名",
      "status": "learning",
      "confidence": 0.5,
      "lastReviewed": "2026-04-24",
      "edges": [
        { "target": "other-concept-id", "type": "requires" }
      ],
      "sessionHistory": [
        { "date": "2026-04-24", "result": "partial", "notes": "简短记录" }
      ]
    }
  ]
}
```

### 字段说明

| 字段 | 类型 | 说明 |
|------|------|------|
| id | string | kebab-case，英文，全局唯一 |
| name | string | 概念的中文名称 |
| status | enum | undiscovered, unknown, learning, weak, mastered |
| confidence | float | 0.0 - 1.0，掌握度量化值 |
| lastReviewed | string | ISO 日期，上次复习日期 |
| edges | array | 与其他概念的关系 |
| sessionHistory | array | 该概念的学习记录 |

## 节点状态机

```
undiscovered → unknown → learning → mastered
                  ↑          ↑         |
                  |          |         v
                  +----------+------ weak
```

### 状态转换规则

| 从 | 到 | 触发条件 |
|----|-----|---------|
| undiscovered | unknown | 边界扫描或 unknown unknown 发现后，用户选择探索 |
| unknown | learning | 开始学习该概念（进入文档生成阶段） |
| learning | mastered | confidence ≥ 0.7 且至少有 2 次 pass 记录 |
| learning | weak | confidence 衰减到 < 0.4 |
| mastered | weak | confidence 衰减到 < 0.4 |
| weak | learning | 用户重新开始学习该概念 |

### Confidence 更新规则

**验证/练习后更新：**
- pass → confidence = min(confidence + 0.2, 1.0)
- partial → confidence = min(confidence + 0.1, 1.0)
- fail → confidence = max(confidence - 0.1, 0.0)

**时间衰减（每次 session 开始时执行）：**

对所有 status 不是 unknown 或 undiscovered 的节点：
1. 计算距 lastReviewed 的天数 d
2. 计算衰减次数 n = floor(d / 7)
3. 如果 n > 0：confidence = confidence × (0.8 ^ n)
4. 如果 confidence < 0.4：status → weak

## 关系类型

| type | 含义 | 影响 |
|------|------|------|
| requires | A requires B = 学 A 之前必须先学 B | 概念选择时检查前置是否满足 |
| relates-to | 相关但无硬依赖 | 用于边界扫描发现关联 |
| extends | A extends B = A 是 B 的深化 | 建议在 B mastered 后学 A |

## 概念选择优先级

当需要选择下一个要学的概念时，按以下优先级排序：

1. **weak 且被其他概念 requires**（地基薄弱，影响上层建筑）
2. **weak**（学过但没学牢）
3. **unknown 且所有 requires 前置已满足**（可以开始学）
4. **unknown**（即使前置未满足，标记后跳过）
5. **undiscovered**（边界扫描发现的新概念）

如果同一优先级有多个候选，选择 confidence 最低的。

## 新增概念

学习过程中 LLM 可以自行向图谱中添加新概念节点。添加时：
- id 使用 kebab-case 英文
- status 设为 unknown
- confidence 设为 0
- 根据领域知识添加合理的 edges
- 添加后同步更新 index.md
