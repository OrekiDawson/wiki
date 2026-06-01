# Wiki Schema

## Domain
Oreki 的个人知识库——涵盖 AI/ML 研究、强化学习、游戏训练（宝可梦/FlappyBird）、编程工具、Linux 运维、以及一切感兴趣的东西。

## Conventions
- 文件名：小写 + 连字符，无空格（如 `ppo-algorithm.md`、`pokemon-emerald.md`）
- 每个 wiki 页面以 YAML frontmatter 开头
- 使用 `[[wikilinks]]` 链接页面（每页至少 2 个出站链接）
- 更新页面时必须更新 `updated` 日期
- 每个新页面必须加入 `index.md` 对应分区
- 每次操作必须追加到 `log.md`

## Frontmatter
```yaml
---
title: 页面标题
created: YYYY-MM-DD
updated: YYYY-MM-DD
type: entity | concept | comparison | query | summary
tags: [从下方分类选]
sources: [raw/articles/xxx.md]
confidence: high | medium | low
---
```

## Tag Taxonomy
### AI/ML
- `rl` — 强化学习
- `ppo` — PPO 算法
- `dagger` — DAgger 算法
- `cnn` — 卷积神经网络
- `llm` — 大语言模型
- `training` — 训练方法
- `inference` — 推理
- `fine-tuning` — 微调

### 游戏 & 项目
- `pokemon` — 宝可梦系列
- `flappybird` — FlappyBird 项目
- `game-ai` — 游戏 AI
- `emulator` — 模拟器

### 工具 & 技术
- `python` — Python 相关
- `linux` — Linux/Debian
- `docker` — Docker
- `gpu` — GPU/CUDA
- `tool` — 工具/软件

### Agent & 治理
- `agent-policy` — AI Agent 政策法规
- `aip` — 智能体互联协议
- `ai-governance` — AI 治理/监管
- `agent-standards` — Agent 标准协议（MCP/A2A/AIP）
- `compliance` — 合规与安全

### Agent & AI
- `alignment` — AI 对齐 / 安全
- `hermes` — Hermes Agent 相关
- `agent` — Agent 架构 / 行为
- `skill` — Skill 系统
- `memory` — 记忆系统

### 人物 & 组织
- `person` — 人物
- `lab` — 研究机构
- `company` — 公司

### 其他
- `concept` — 通用概念
- `comparison` — 对比分析
- `note` — 笔记/备忘

## Page Thresholds
- 出现在 2+ 来源 → 建页面
- 是某个来源的核心主题 → 建页面
- 只是顺带提到 → 不建页面
- 超过 200 行 → 拆分

## Update Policy
新信息与现有内容冲突时：
1. 检查日期——新来源通常优先
2. 如果真的矛盾，两种说法都保留并注明日期和来源
3. frontmatter 标注 `contested: true`
4. 报告给 Oreki 确认
