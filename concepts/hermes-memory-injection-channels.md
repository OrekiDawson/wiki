---
title: Hermes Agent Memory 注入双通道（MEMORY.md frozen snapshot × honcho volatile block）
created: 2026-06-01
updated: 2026-06-01
type: concept
tags: [hermes, memory, honcho, system-prompt]
sources: [raw/agent-loop-inspection/hermes-run-agent-memory-load.md]
confidence: high
---

# Hermes Agent Memory 注入双通道

> 当小呦在同 session 内发现"刚答的内容被自己当成过去对话"，问题往往不是单一根因。
> 真实情况是 **两条独立的 system prompt 注入通道**在小呦内部无差别混读。

## 两条独立通道

### 通道 1：MEMORY.md / USER.md frozen snapshot
- **装载时机**：session 启动时调用 `load_from_disk()` 一次性读取到内存
- **同 session 行为**：写盘（memory tool add/replace/remove）**不刷新** system prompt
- **新 session 行为**：默认重新 load_from_disk
- **机制来源**：`run_agent.py` L6218-6224 / `memory_tool.py`
- **特征字段**：prompt header 形如 `[X% — N/char_limit chars]`，N 是**启动时**的 disk 大小

### 通道 2：honcho volatile block
- **装载时机**：每个 turn 重新从 honcho 拉取
- **来源**：`run_agent.py` L6238-6245 的 `_memory_manager.build_system_prompt()`
- **特征**：包含 `honcho_conclude` 写入的事实、`honcho_search/prefetch/recall` 的召回内容
- **同 session 行为**：每次 turn 都可能刷新，**与 frozen snapshot 不同步**

## 为什么容易混淆
- 两个通道都进入 system prompt 的同一段（小呦无差别读）
- 小呦无来源标记：它无法知道"这句话来自 frozen snapshot 还是 honcho volatile"
- 误判后果：把 honcho 刚 recall 出来的内容当成"刚才聊的"，或把 frozen snapshot 旧版当成"还没发生"

## 区分方法（交叉验证）
1. **session_start_time** ← session_id 前缀
2. **memory file mtime / sha256 / char_count** ← 磁盘现状
3. **prompt header**（`[X% — N/limit chars]`）← N 对比启动时 disk
4. **honcho record 时间**（`honcho_context` 拉取的时间戳）
5. **被引用内容的特征**（如果是新增概念名 / 新版本号 / 新事实 → 优先查 honcho）

## 相关页面
- [[reports/memory-context-dual-channel-confusion-2026-06-01]] — 双通道混淆案例（2026-06-01 在吗事故）
- [[hermes-agent-skills-cron-memory-2026]] — Hermes 三大核心能力总览
- [[agent-drift-patterns]] — 漂移模式，含"输入源误识别"
