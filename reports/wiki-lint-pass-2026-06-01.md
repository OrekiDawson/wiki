---
title: 2026-06-01 wiki 整理报告（lint pass）
created: 2026-06-01
updated: 2026-06-01
type: report
tags: [wiki, lint, maintenance, openclaw-compat]
confidence: high
sources: []
---

# 2026-06-01 wiki 整理报告（lint pass）

> Oreki 要求"先整理一下 wiki，顺便看看你能复习哪些知识"。
> 本报告记录小呦本轮做的 lint + 修复，以及**没做**的事（gap 透明化）。

## 复习到的知识

1. **OpenClaw memory-wiki 插件架构**——vault 模式 `isolated`、render 模式 `native`、agent-digest.json + claims.jsonl 两套机器可读数据
2. **wiki 双 frontmatter schema 不一致**——wiki 自己用 `title/created/updated/type/tags`；OpenClaw 用 `pageType/id/title/updatedAt`；混用导致 OpenClaw stale-pages 报告 19 个
3. **llm-wiki skill 的 lint 11 步**——orphan / broken / index / frontmatter / stale / contradictions / quality / drift / size / tag / log
4. **red-runtime/cases/ 子目录**——Red runtime 边界事件案例区，独立于 reports/ 但和 xiaoyou_red/evidence/ 互补

## 跑了什么

`/tmp/wiki_lint.py`（本轮新写，6 类检查）：
- Orphans（零 inbound）
- Broken wikilinks（target 不存在）
- Index completeness（disk vs main index 差集）
- Frontmatter 必填字段
- Page size（>200 行）
- Stale（updated > 30 天）
- Confidence 字段

## 修了什么

| 类别 | 数量 | 修法 |
|---|---|---|
| Broken wikilinks（带路径前缀）| 5 | 改用 basename 形式 |
| Broken wikilinks（指向 wiki 外）| 2 | 删除（moltbook 自身）或替换为站内页 |
| Missing frontmatter 字段 | 6 | 补 `updated` / `created` / `type` / `title` / `confidence` |
| Main index 缺 page | 1 | `red-runtime/cases/20260601_codex_forest_cheat_boundary` 加进新分区"Red Runtime Cases" |
| 双向 inbound link | 1 | `memory-context-dual-channel-confusion` ↔ `20260601_codex_forest_cheat_boundary` 互引 |

## 没修的（gap 透明化）

| 类别 | 数量 | 不修原因 |
|---|---|---|
| Orphan `reports/*`（9 个）| 9 | OpenClaw 自动维护，加 wikilink 反而会破 plugin 语义 |
| Orphan `raw/*`（4 个）| 4 | 原材料按约定不进 main index |
| Stale 6 个 page（5-01/4-29 创建）| 6 | **不 bump `updated`**——这是伪造 freshness，比承认错更糟；OpenClaw 报 19 个 stale 实际是缺 `updatedAt` 字段，不是内容 stale |
| Missing frontmatter `reports/*`（9 个）| 9 | OpenClaw 自己的 schema（`pageType/id/title/updatedAt`），不是 wiki 自己的 `created/updated/type/tags`；不归 lint 管 |
| Page size > 200 行（7 个）| 7 | 全部是 raw article 或深度概念笔记，按 SCHEMA 规则"超过 200 行才拆"——但**拆分需要先有重复或并行主题**，这些 page 都单一主题，**强行拆反而稀释可读性** |
| Page size `red-runtime/cases/20260601_codex_forest_cheat_boundary`（202 行）| 1 | 临界值，不拆 |
| SCHEMA.md 里 `[[wikilinks]]` 字面量 | 1 | 修 lint 脚本（识别 inline code），不是改 SCHEMA |

## 发现的 schema 冲突（gap）

- **wiki 自己的 `updated` 字段** vs **OpenClaw 的 `updatedAt` 字段**——两套 schema 混用
- OpenClaw stale-pages 报 19 个 = "缺 `updatedAt` 字段" ≠ "内容真的 stale"
- 修法候选：(a) 给所有 page 加 `updatedAt: <date>` 字段兼容 OpenClaw；(b) 改 OpenClaw 配置让它认 wiki 的 `updated`
- 本轮**不**做这个——这是 plugin 集成层的事，需要 Oreki 决定走 (a) 还是 (b)

## 相关页面
- [[memory-context-dual-channel-confusion-2026-06-01]] — 同期事件案例
- [[hermes-memory-injection-channels]] — Memory 双通道概念
- [[20260601_codex_forest_cheat_boundary]] — Red runtime 边界案例
- [[soul-md-design]] — SOUL.md 设计方法论（包含 wiki 整理纪律）
