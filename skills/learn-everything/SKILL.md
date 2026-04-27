---
name: learn-everything
description: Use when wanting to learn any concept or domain deeply through reverse questioning, where AI tests the user instead of the user asking AI. Triggers on "学习", "teach me", "I want to learn", "帮我学", or any request to systematically study a topic.
---

# Learn Everything

让 AI 考你，而不是你问 AI。通过反转提问驱动深度学习，追踪知识图谱，暴露 unknown unknowns。

## 入口判断

检查当前工作目录：

1. **已有学习项目**（存在 `meta.json`）→ 读取 `current-session.json`
   - 有未完成 session → 读取对应阶段的 reference 文件，从上次位置恢复
   - 无未完成 session → 执行 confidence 衰减（读 `references/knowledge-graph-spec.md` 中的衰减公式），进入复习诊断
2. **新项目** → 用户已指定概念/领域？
   - 是 → 执行初始化流程（见下方），进入冷启动诊断
   - 否 → 询问用户想学什么

## 初始化新领域

当用户指定一个新的学习领域时，在当前目录中创建以下文件：

**meta.json:**

    {
      "domain": "{用户指定的领域名}",
      "created": "{当前日期 ISO}",
      "totalSessions": 0
    }

**knowledge-graph.json:**

    {
      "domain": "{领域名}",
      "nodes": []
    }

**learner-profile.json:**

    {
      "traits": [],
      "strengths": [],
      "weaknesses": [],
      "settings": {
        "scaffoldingLevel": "medium",
        "challengeAppetite": "medium"
      }
    }

**current-session.json:**

    {
      "stage": "diagnostic",
      "currentConcept": null,
      "nextAction": "读 references/diagnostic-engine.md，执行冷启动诊断",
      "questionCount": 0,
      "conceptsThisSession": [],
      "sessionStarted": "{当前时间 ISO}"
    }

**index.md:**

    # {领域名} — 知识索引
    
    _尚无概念记录。学习开始后自动更新。_

**log.md:**

    # {领域名} — 学习日志

**.gitignore:**

    .DS_Store

**CLAUDE.md** — 见下方模板。

创建完成后执行 `git init && git add -A && git commit -m "init: {领域名} 学习项目"`。

## CLAUDE.md 模板

生成到学习项目根目录，作为不变规则锚点：

    # Learn Everything — {领域名}
    
    本目录是一个 AI 驱动的学习项目，使用 learn-everything skill。
    
    ## 学习循环
    
    每次 session 执行 5 阶段循环：诊断 → 文档生成 → 提问验证 → 练习 → 总结。
    **不要跳过任何阶段。** 每个阶段有对应的 reference 文件，按需加载。
    
    ## 关键规则
    
    1. **反转提问**：AI 考用户，不是用户问 AI
    2. **开放讨论窗口**：每次验证/练习后必须问"还有想继续讨论的吗？"，用户说"下一题"才继续
    3. **回答质量决定追问**：pass→追问链+反例，partial→追问1层，fail→引导重讲
    4. **不打断教学**：发现 unknown unknown 时记录但不打断，总结阶段统一处理
    5. **每轮更新 current-session.json**：确保下次能恢复位置
    
    ## 数据文件
    
    - `knowledge-graph.json` — 知识图谱（AI 读写）
    - `learner-profile.json` — 学习者画像（AI 读写）
    - `current-session.json` — 当前会话状态（AI 每轮更新）
    - `index.md` — 概念索引（AI 更新）
    - `log.md` — 学习日志（AI 追加）
    - `docs/` — 学习文档（AI 生成）
    - `sessions/` — session 记录（AI 生成）
    - `discussions/` — 讨论记录（AI 生成）
    - `dashboard.html` — 进度仪表盘（AI 生成）
    
    ## Skill Reference 文件路径
    
    `~/.claude/skills/learn-everything/references/`
    
    进入每个阶段前，读取对应的 reference 文件获取详细指令。

## 5 阶段循环

### 阶段 1：诊断

读取 `references/diagnostic-engine.md`，按其中的指令执行诊断。

完成后更新 `current-session.json`：stage → "document-generation"。

### 阶段 2：文档生成

读取 `references/document-generation.md` 和 `references/knowledge-graph-spec.md`。

根据知识图谱选择下一个概念，生成聚焦学习文档，保存到 `docs/`。

完成后更新 `current-session.json`：stage → "verification"。

### 阶段 3：提问验证

读取 `references/understanding-check.md`、`references/deep-questioning.md`、`references/intelligent-feedback.md`。

AI 反过来考用户。执行深度追问。打开讨论窗口。

完成后更新 `current-session.json`：stage → "practice"。

### 阶段 4：练习

读取 `references/understanding-check.md`（复用评估标准）。

出 1-2 道练习题。每题后打开讨论窗口。

完成后更新 `current-session.json`：stage → "summary"。

### 阶段 5：总结

读取 `references/knowledge-graph-spec.md`、`references/learner-profile-spec.md`、`references/dashboard-generator.md`。

更新所有数据文件。生成 session 摘要。检查 unknown unknown（读 `references/unknown-unknown-discovery.md`）。生成/更新 `dashboard.html`。

完成后：
- 更新 `current-session.json`：stage → "diagnostic"，currentConcept → null
- 询问用户：继续下一个概念，还是结束本次学习？
- 如果继续 → 回到阶段 2（跳过诊断，直接选下一个概念）
- 如果结束 → 清空 `current-session.json` 中的 session 状态，执行 `git add -A && git commit`

## 边界扫描

每学 5-8 个概念（检查 `current-session.json` 中 `conceptsThisSession` 的长度），在总结阶段额外读取 `references/unknown-unknown-discovery.md` 执行边界扫描。
