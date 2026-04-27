# 学习者画像规格

## JSON Schema

`learner-profile.json` 存储在学习项目根目录。

```json
{
  "traits": [
    {
      "id": "trait-id-kebab-case",
      "description": "观察到的学习特征描述",
      "confidence": 0.3,
      "firstObserved": "2026-04-10",
      "lastConfirmed": "2026-04-22"
    }
  ],
  "strengths": ["强项描述1", "强项描述2"],
  "weaknesses": ["弱项描述1"],
  "settings": {
    "scaffoldingLevel": "medium",
    "challengeAppetite": "medium"
  }
}
```

## Trait 观察与衰减

### 新增 trait

首次观察到一个学习特征时，confidence 设为 0.3。只有持续确认才增加。

### Confidence 更新

- 本次 session 观察到该 trait → confidence = min(confidence + 0.15, 1.0)，更新 lastConfirmed
- 本次 session 未观察到 → 不变（衰减在 session 间发生）

### Session 间衰减

每次 session 开始时，对所有 traits 检查 lastConfirmed：
- 距今超过 3 次 session 未确认 → confidence × 0.7
- confidence < 0.1 → 删除该 trait

这避免过早给学习者贴标签。

## Settings 含义

| 设置 | 值 | 对系统行为的影响 |
|------|-----|----------------|
| scaffoldingLevel | low / medium / high | low: 文档紧凑，减少引导；high: 更多类比、示例、分步解释 |
| challengeAppetite | low / medium / high | low: 更多基础巩固；high: 更多反例挑战，追问链用满 3 层 |

### Settings 调整

settings 基于 session 表现自动微调：
- 连续 3 次 pass → challengeAppetite 上调一级
- 连续 2 次 fail → scaffoldingLevel 上调一级，challengeAppetite 下调一级
- 用户主动要求更难/更简单 → 直接调整

## 画像如何影响各阶段

| 画像内容 | 影响的阶段 | 具体行为 |
|---------|----------|---------|
| trait: 偏好类比 | 文档生成 | 文档中增加高质量类比 |
| trait: 容易混淆相近概念 | 提问验证 | 增加对比题 |
| scaffoldingLevel: high | 文档生成 | 更多引导、分步解释 |
| challengeAppetite: high | 提问验证 | 更多反例挑战，追问层数增加 |
| strength: 横向关联能力 | 练习 | 出跨概念关联题 |
| weakness: 容易过度泛化 | 提问验证 | 增加边界测试题 |

## 更新时机

在阶段 5（总结）中更新。回顾本次 session 的表现，观察：
- 用户回答的模式（是否频繁使用类比？是否善于举反例？）
- 理解方式偏好（是否从整体到局部？还是从细节到整体？）
- 常见错误模式（混淆、过度泛化、表面理解）

更新 traits、strengths、weaknesses 和 settings。
