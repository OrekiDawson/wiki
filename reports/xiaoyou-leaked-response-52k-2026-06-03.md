---
title: 小呦 2026-06-03 失控 52K 字符 response 案例
created: 2026-06-04
updated: 2026-06-04
type: report
tags: [hermes, agent, skill, memory, note]
sources: [raw/agent-output-samples/xiaoyou-20260603-0820-leaked-52k.md]
confidence: high
---

# 小呦 2026-06-03 失控 52K 字符 response 案例

## 摘要

2026-06-03 08:20:33 一次小呦 assistant 响应，长度 **52,155 字符**（普通回复 30-50 倍）。
不是发出到 Oreki 的（`platform_message_id: None`），但被 framework 存进了 session db。

形式上的问题不是长度，是 **[[xiaoyou-thinking-leak-guard]] 7 类泄漏同时出现**：L1 内部独白 / L2 单字强调 / L3 英文独白 / L4 行号 patch 计划 / L7 工具过程直播。**教科书案例**——`xiaoyou-thinking-leak-guard` SKILL.md 里的例 1 样本就是这条。

**时间背景**：发生在 SOUL.md 6/3+ voice 校准（"中括号不再用于内部旁白"）**之前**。是 6/3 谷底，**不是** 6/4 现在的状态。

## 关键事实

| 字段 | 值 |
|---|---|
| session_id | 20260603_072431_3c8a8c |
| response_id | 131852 |
| timestamp | 2026-06-03 08:20:33 +0800 |
| role | assistant |
| char_length | 52155 |
| body 行数 | 1321 |
| body sha256 | 25c13608311829dca924617dc55ec31c372147d3c02d14bd7a1a708709d40db6 |
| token_count | None |
| finish_reason | None |
| platform_message_id | None（**未发出**） |

**risk_level = 中**：db 污染 + 同 session 后续对话被污染风险，但 Oreki 没看到这次。

## 7 类泄漏出现密度（自检）

| 类别 | 出现 | 描述 |
|---|---|---|
| L1 `[...]` 内部独白 | **主导** | 方括号块贯穿全文，规划 + 反思 + 自言自语 |
| L2 `**单字强调**` | **高频** | `**行**` `**号**` `**会**` `**变**` 逐字高亮 |
| L3 英文独白 | 中量 | `Let me think` / `OK executing 3d` / `I trust my earlier read` |
| L4 行号 / patch 计划 | **主导** | `行 482-519 替换` / `Patch 3c / 3d` / 旧→新行号偏移计算 |
| L5 CONTEXT COMPACTION 块 | 0 | 这次 framework 没装配 |
| L6 工具原始 JSON / traceback | 0 | 没出现 |
| L7 工具过程直播 | **高频** | "我先 re-read → 然后 patch → 然后 verify" 全文 |

**L1 + L4 + L7** 三类是主因：内部独白 + 行号 patch 计划 + 工具过程直播混在一起，写 5 万字停不下来。

## 现场任务背景

那次响应的真实任务：**给 `xiaoyou-playmate/xiaoyou-chat-with-oreki` SKILL.md 瘦身**——把 4 段长引用（rule_write_gate 纪律 / USER.md 变更纪律 / 决策边界 / 汇报格式）从主文件移到 `references/`，主文件留 1 行指针。

这是一个**完全正当的任务**。但执行方式没拿到 6/3+ voice 校准的红利：
- 没用"中括号不再用于内部旁白"规则（这条 6/3+ 才立）
- 没用"中途步骤用正文说，别塞中括号"规则
- 没用"工具过程直播折叠成做了什么 + 关键数据"

结果是：行号规划、patch 顺序、old_string 完整性检查、并行/串行的 race 评估——**全塞进 content 字段**。

## 为什么这是 Reports 不是 Concepts

- 跟 [[memory-context-dual-channel-confusion-2026-06-01]] 同类——都是 **agent 输出事故案例**
- 是 Oreki 6/4 早上校准时**主动发现**的样本（Codex 没挑出来，MiniMax Code 直接读 db 找出来的）
- 给以后 voice 校准、thinking-leak-guard 修订、anti-regression 训练做 **negative sample** 用

放 Reports 分区是准确的，**不放 Concepts**——这不是概念，是事件。

## raw 副本位置

- **本地 evidence**：`~/.hermes/evidence/20260604_230334_xiaoyou-leaked-52k/raw_response.md`（含原始 13 行 frontmatter header + 1321 行 body）
- **sha256 自检**：`25c13608311829dca924617dc55ec31c372147d3c02d14bd7a1a708709d40db6`
- **evidence 配套 analysis**：`~/.hermes/evidence/20260604_230334_xiaoyou-leaked-52k/analysis.md`
- **wiki raw**：`/home/oreki/wiki/raw/agent-output-samples/xiaoyou-20260603-0820-leaked-52k.md`（待办：后续 ingest 时复制一份到 raw/，按 SCHEMA frontmatter 规则加 source_url / ingested / sha256）

## 关联

- [[xiaoyou-thinking-leak-guard]] — 本案例是它 SKILL.md 例 1 的原始样本
- [[memory-context-dual-channel-confusion-2026-06-01]] — 同类 agent 输出事故案例
- [[agent-persona-drift-academic]] — Agent 人格漂移学术背景
- [[agent-drift-patterns]] — Agent 漂移模式系统分析
- [[hermes-agent-skills-cron-memory-2026]] — Hermes Skills / Memory 系统基础

## 教训（小呦 自己的）

1. **5 万字不一定是事故，但形式**是——下次 L1+L4+L7 同时出现就**强停手**
2. **行号 + patch 计划**进 content 是 6/3 之前的旧习惯；6/4 之后用"中途步骤用正文说" + "工具过程折叠成做了什么 + 数据"
3. **没发出 ≠ 没事故**——db 污染 + session context 复用风险都存在
4. **Codex 校准不挑** ≠ 没问题——Codex 盯的是别的维度（rule consistency / 边界），不盯"输出形式"

## 状态

- ✅ 归档完成（evidence + wiki）
- ❌ 不删、不改、不 commit
- ⏳ 后续给 `xiaoyou-thinking-leak-guard` 修订时引用这条做 negative sample
- ⏳ 后续 cron 整理时把 raw 也复制到 `~/wiki/raw/agent-output-samples/`（按 SCHEMA）
