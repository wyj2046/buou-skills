# HTML 仪表盘生成

## 触发时机

每次阶段 5（总结）末尾执行。全量重新生成 `dashboard.html`。

## 数据提取

从以下文件读取数据：

| 数据源 | 提取内容 |
|-------|---------|
| knowledge-graph.json | 所有节点的 name、status、confidence |
| meta.json | domain、created、totalSessions |
| log.md | 最近 10 条 session 记录 |
| sessions/ 目录 | 每天的 session 数量（用于热力图） |

## 统计计算

```
totalConcepts = nodes.length（排除 undiscovered）
masteredCount = nodes.filter(status === "mastered").length
weakCount = nodes.filter(status === "weak").length
discoveredCount = nodes.filter(status === "undiscovered").length
learningDays = 从 meta.created 到今天的天数
```

## HTML 模板

生成以下完整 HTML 写入 `dashboard.html`。用实际数据替换所有 `{{占位符}}`。

```html
<!DOCTYPE html>
<html lang="zh-CN">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>{{domain}} — 学习仪表盘</title>
<style>
* { margin: 0; padding: 0; box-sizing: border-box; }
body {
  font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", sans-serif;
  background: #0d1117; color: #e6edf3; padding: 32px;
}
.dashboard { max-width: 900px; margin: 0 auto; }
.header { margin-bottom: 32px; }
.header h1 { font-size: 24px; font-weight: 600; margin-bottom: 4px; }
.header .meta { font-size: 13px; color: #8b949e; }

.stats { display: grid; grid-template-columns: repeat(4, 1fr); gap: 16px; margin-bottom: 32px; }
.stat-card { background: #161b22; border: 1px solid #30363d; border-radius: 8px; padding: 16px; }
.stat-card .value { font-size: 28px; font-weight: 700; }
.stat-card .label { font-size: 12px; color: #8b949e; margin-top: 4px; }
.v-blue { color: #58a6ff; }
.v-green { color: #39d353; }
.v-yellow { color: #d29922; }
.v-purple { color: #a371f7; }

.section { margin-bottom: 32px; }
.section-title {
  font-size: 14px; font-weight: 600; margin-bottom: 12px;
  color: #8b949e; text-transform: uppercase; letter-spacing: 0.5px;
}

.heatmap { display: flex; gap: 3px; flex-wrap: wrap; }
.hm { width: 14px; height: 14px; border-radius: 2px; }
.h0 { background: #161b22; }
.h1 { background: #0e4429; }
.h2 { background: #006d32; }
.h3 { background: #26a641; }
.h4 { background: #39d353; }
.heatmap-legend { display: flex; align-items: center; gap: 4px; margin-top: 8px; font-size: 11px; color: #8b949e; }

.knowledge-map { background: #161b22; border: 1px solid #30363d; border-radius: 8px; padding: 24px; }
.concept-grid { display: grid; grid-template-columns: repeat(auto-fill, minmax(180px, 1fr)); gap: 12px; }
.concept-node { padding: 12px; border-radius: 6px; border-left: 3px solid; }
.concept-node .name { font-size: 13px; font-weight: 600; margin-bottom: 4px; }
.concept-node .conf { font-size: 11px; color: #8b949e; }
.concept-node .bar { height: 3px; border-radius: 2px; margin-top: 8px; }
.mastered { border-color: #39d353; background: rgba(57,211,83,0.08); }
.mastered .bar { background: #39d353; }
.learning { border-color: #58a6ff; background: rgba(88,166,255,0.08); }
.learning .bar { background: #58a6ff; }
.weak { border-color: #d29922; background: rgba(210,153,34,0.08); }
.weak .bar { background: #d29922; }
.unknown { border-color: #8b949e; background: rgba(139,148,158,0.05); }
.unknown .bar { background: #8b949e; }
.legend { display: flex; gap: 20px; margin-top: 12px; }
.legend-item { display: flex; align-items: center; gap: 6px; font-size: 11px; color: #8b949e; }
.legend-dot { width: 10px; height: 10px; border-radius: 2px; }

.timeline { background: #161b22; border: 1px solid #30363d; border-radius: 8px; padding: 24px; }
.tl-entry { display: flex; gap: 16px; padding: 12px 0; border-bottom: 1px solid #21262d; }
.tl-entry:last-child { border-bottom: none; }
.tl-date { font-size: 12px; color: #8b949e; min-width: 80px; padding-top: 2px; }
.tl-content { font-size: 13px; }
.tl-content .topic { font-weight: 600; }
.tl-content .detail { color: #8b949e; margin-top: 2px; }
.badge { display: inline-block; font-size: 10px; padding: 2px 6px; border-radius: 10px; margin-left: 6px; }
.b-pass { background: rgba(57,211,83,0.15); color: #39d353; }
.b-partial { background: rgba(210,153,34,0.15); color: #d29922; }
.b-fail { background: rgba(248,81,73,0.15); color: #f85149; }
.b-disc { background: rgba(137,87,229,0.15); color: #a371f7; }
</style>
</head>
<body>
<div class="dashboard">
  <div class="header">
    <h1>{{domain}}</h1>
    <div class="meta">已学习 {{learningDays}} 天 · {{totalSessions}} 次 session · 上次学习: {{lastSessionDate}}</div>
  </div>

  <div class="stats">
    <div class="stat-card"><div class="value v-blue">{{totalConcepts}}</div><div class="label">已发现概念</div></div>
    <div class="stat-card"><div class="value v-green">{{masteredCount}}</div><div class="label">已掌握</div></div>
    <div class="stat-card"><div class="value v-yellow">{{weakCount}}</div><div class="label">需巩固</div></div>
    <div class="stat-card"><div class="value v-purple">{{discoveredCount}}</div><div class="label">新发现</div></div>
  </div>

  <div class="section">
    <div class="section-title">学习热力图</div>
    <div class="heatmap">
      {{heatmapCells}}
    </div>
    <div class="heatmap-legend">
      <span>少</span>
      <div class="hm h0" style="display:inline-block;width:10px;height:10px;"></div>
      <div class="hm h1" style="display:inline-block;width:10px;height:10px;"></div>
      <div class="hm h2" style="display:inline-block;width:10px;height:10px;"></div>
      <div class="hm h3" style="display:inline-block;width:10px;height:10px;"></div>
      <div class="hm h4" style="display:inline-block;width:10px;height:10px;"></div>
      <span>多</span>
    </div>
  </div>

  <div class="section">
    <div class="section-title">概念掌握度</div>
    <div class="knowledge-map">
      <div class="concept-grid">
        {{conceptCards}}
      </div>
      <div class="legend">
        <div class="legend-item"><div class="legend-dot" style="background:#39d353;"></div> mastered</div>
        <div class="legend-item"><div class="legend-dot" style="background:#58a6ff;"></div> learning</div>
        <div class="legend-item"><div class="legend-dot" style="background:#d29922;"></div> weak</div>
        <div class="legend-item"><div class="legend-dot" style="background:#8b949e;"></div> unknown</div>
      </div>
    </div>
  </div>

  <div class="section">
    <div class="section-title">最近学习记录</div>
    <div class="timeline">
      {{timelineEntries}}
    </div>
  </div>
</div>
</body>
</html>
```

## 数据填充规则

### heatmapCells

扫描从 meta.created 到今天的每一天。对每天：
- 统计 sessions/ 目录中该日期的 session 文件数量
- 0 次 → `<div class="hm h0"></div>`
- 1 次 → `<div class="hm h1"></div>`
- 2 次 → `<div class="hm h2"></div>`
- 3 次 → `<div class="hm h3"></div>`
- 4+ 次 → `<div class="hm h4"></div>`

### conceptCards

对 knowledge-graph.json 中每个非 undiscovered 的节点，按 status 分组排序（mastered → learning → weak → unknown），生成：

```html
<div class="concept-node {{status}}">
  <div class="name">{{name}}</div>
  <div class="conf">confidence: {{confidence 保留两位小数}}</div>
  <div class="bar" style="width: {{confidence * 100}}%;"></div>
</div>
```

unknown 节点的 confidence 显示为 `—`，bar width 为 0%。

### timelineEntries

从 log.md 中提取最近 10 条记录，每条生成：

```html
<div class="tl-entry">
  <div class="tl-date">{{日期 MM-DD}}</div>
  <div class="tl-content">
    <div class="topic">{{概念名}} <span class="badge b-{{result}}">{{result}}</span></div>
    <div class="detail">{{notes}}</div>
  </div>
</div>
```

result 到 badge class 的映射：pass → b-pass, partial → b-partial, fail → b-fail, discovered → b-disc。
