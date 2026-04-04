# buou-skills

我的 [Claude Code](https://docs.anthropic.com/en/docs/claude-code) / [OpenClaw](https://github.com/chujianyun/openclaw) 自定义技能集。

## 技能

| 技能 | 说明 |
|------|------|
| **buou-buffett** | 巴菲特AI分身 — 用价值投资之父的视角回答任何问题。六维人格：合伙人思维、价值原则、复利配置、故事化表达、理性利他、极度专注。触发词：`buffett`、`巴菲特`、`价值投资`、`ask warren` |
| **buou-yongping** | 段永平AI分身 — 用步步高创始人、价值投资践行者的视角回答任何问题。极简坦诚，本分文化，平常心。触发词：`yongping`、`段永平`、`老段`、`本分`、`ask yongping` |
| **buou-7s-diagnosis** | 麦肯锡7S企业诊断 — 结合段永平投资理念，从战略、结构、系统、价值观、风格、人员、技能七个维度诊断企业，判断问题可解决性，区分投资机会与陷阱。触发词：`7s`、`7S`、`企业诊断`、`企业分析` |
| **buou-deep-reason** | 深度推理引擎 — 摒弃中庸偏好，从因果结构、人性本能、演化机制出发，彻底冷静推理。挖掘第一性原理，推动认知边界到极限。触发词：`深度分析`、`本质`、`第一性`、`底层逻辑`、`deep reason` |
| **buou-ceo-coach** | CEO教练 — 专业、直接、无废话。客观第三方视角，理性分析，不给情绪支持，不伪装，不恭维。触发词：`coach`、`教练`、`CEO教练`、`帮我想`、`客观分析` |
| **buou-idea-practice** | 理念实践教练 — 阅读文章后，一步步提问帮你探索内心答案，最终形成深度分析与一日/一周/一月行动建议。触发词：`实践`、`落地`、`行动建议`、`怎么开始`、`idea practice` |
| **buou-ask-first** | 提问优先教练 — 回答前先问问题，一次一个，逐层追问，澄清目标/场景/限制/成功标准/边界。95%把握理解真实需求后才给方案。触发词：`先问`、`问清楚`、`澄清需求`、`ask first` |
| **buou-plain** | 学术世俗化大师 — 把博士论文翻译成茶余饭后的谈资。博导听了想打人，大爷听了拍大腿。触发词：`说人话`、`通俗点`、`大白话`、`翻译一下`、`plain` |

## 安装

### 方法一：OpenClaw 插件市场

```bash
# 添加插件市场
/plugin marketplace add wyj2046/buou-skills

# 安装单个技能
/plugin install buou-buffett@wyj2046/buou-skills
/plugin install buou-yongping@wyj2046/buou-skills
/plugin install buou-7s-diagnosis@wyj2046/buou-skills
/plugin install buou-deep-reason@wyj2046/buou-skills
/plugin install buou-ceo-coach@wyj2046/buou-skills
/plugin install buou-idea-practice@wyj2046/buou-skills
/plugin install buou-ask-first@wyj2046/buou-skills
/plugin install buou-plain@wyj2046/buou-skills
```

### 方法二：克隆安装

```bash
git clone https://github.com/wyj2046/buou-skills.git ~/.claude/plugins/buou-skills
```

然后重启 Claude Code / OpenClaw。

### 方法三：openskills 安装

```bash
openskills install wyj2046/buou-skills --global
```

## 使用示例

```
# 向巴菲特请教投资
你好 buffett，怎么看现在的 AI 行业投资机会？

# 向段永平请教
yongping，怎么判断一个好公司？

# 企业诊断
7s 分析一下 XX 公司

# 提问优先
先问清楚，再给方案：[描述需求]

# 理念实践
[粘贴文章] 我想开始实践这个理念

# 向CEO教练请教
coach，帮我客观分析一下这个决策

# 深度推理
深度分析一下 XX 问题的底层逻辑

# 把学术论文翻译成人话
说人话解释一下这篇论文的核心观点
```

## 技能详解

### buou-buffett

沃伦·巴菲特人格模拟，完整的六维人格框架：
- **人**：合伙人思维 - 股东即合伙人，永不背叛信任
- **事**：价值原则 - 价格<价值是唯一投资标准
- **钱**：复利配置 - 时间是好生意的朋友
- **形**：故事化表达 - 比喻+故事+坦诚+长篇教育
- **魂**：理性利他 - 诚实第一，股东利益至上
- **格**：极度专注 - 集中投资+耐心等待

### buou-yongping

段永平人格模拟，极简风格：
- 回答通常1-3句，能一句说清绝不说两句
- 大白话不堆术语，常用生活化比喻
- 爱用"呵呵""哈哈"等语气词
- 能力圈外直接说"不懂""没研究过"

### buou-7s-diagnosis

麦肯锡7S模型 + 段永平投资理念：
- 逐维度扫描：Strategy、Structure、Systems、Shared Values、Style、Staff、Skills
- 核心输出：问题可解决性评估（容易/可以/难以/几乎不可能）
- 最终判断：投资机会 vs 需要回避

### buou-plain

学术世俗化翻译器：
- 像在撸串时跟哥们儿解释
- 用菜市场大妈都懂的例子
- 要有"就这？"的恍然大悟感

### buou-deep-reason

深度推理引擎，摒弃中庸偏好：
- 从因果结构、人性本能、演化机制出发
- MECE原则展开，跨域关联
- 输出：深度分析 + 理解验证 + ≥3个关键自检验问题
- 不留余地，不绕边界，当前能力下的最强表达

### buou-ceo-coach

CEO教练，直接不废话：
- 客观第三方视角，跳出你的立场
- 只给理性分析和启发，不给情绪支持
- 挑战你的假设，而非顺着说
- 不恭维，不伪装，不照顾面子

### buou-idea-practice

理念实践教练，探索式引导：
- 阅读文章后逐个提问，帮用户探索内心答案
- 提问维度：动机、现状差距、阻力、资源、优先级、边界、成功定义
- 输出：深度分析 + 一日行动 + 一周行动 + 一月行动
- 行动建议具体、渐进、可检验

### buou-ask-first

提问优先教练，澄清再方案：
- 回答前必须先提问，不允许跳过
- 一次一个问题，逐层追问，不并行甩问题
- 澄清：目标、场景、限制条件、成功标准、边界
- 95%把握理解真实需求后才给方案
- 需求有问题直接指出，不照顾面子

## License

MIT