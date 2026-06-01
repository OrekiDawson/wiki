---
title: 2026-06-01 memory-context 双通道混淆案例
created: 2026-06-01
updated: 2026-06-01
type: report
tags: [hermes, memory, honcho, agent-drift, timeline-gap]
sources: [raw/agent-loop-inspection/session-20260601-113141.txt]
confidence: high
status: active
contradictions: []
---

# 2026-06-01 memory-context 双通道混淆案例

> Oreki 在 22:28 发"小呦，在吗"；小呦 22:29 的回复**把 honcho 刚写入的事实当成了"我们刚才聊的"**。
> 本案**不是单一根因**，是 frozen snapshot 与 honcho volatile block 两条独立通道被小呦无差别混读。
> **"在吗"那条具体引用内容来自哪一通道仍有 timeline_gap**——本报告不写成"已最终证明"。

## 1. 事件骨架

| 时间 (Asia/Shanghai) | 事件 | 证据 |
|---|---|---|
| 11:31:41 | session `20260601_113141_ef0a50` 启动；frozen snapshot 装载 | session_id 命名 |
| 22:28:57 | Oreki 发"小呦，在吗" | agent.log |
| 22:29:26 | 小呦开始处理"在吗"消息 | agent.log |
| 22:30+ | 26 条 honcho conclusions 陆续写入（按先前 summary） | honcho 写入记录 |
| 22:46 | Codex 跑剪枝，备份 MEMORY.md 12,535 bytes | `/tmp/MEMORY.md.prune.bak` |
| 22:48 | Codex 改完 MEMORY.md 剪到 2,200 bytes | Codex 报告 |
| 22:54 | MEMORY.md 变 3,938 bytes（disk） | stat mtime |
| 22:55+ | config.yaml 切到 2,200/1,375 | chmod 时间 |

## 2. 三件被确认的事（confirmed）

1. **MEMORY.md frozen snapshot 与 honcho volatile block 是两条独立注入通道**
   - 来源：`run_agent.py:6218-6224`（frozen 装载）vs `run_agent.py:6238-6245`（honcho 装载）
   - frozen：session 启动一次性 load_from_disk；同 session 写盘不刷 prompt
   - honcho：每个 turn 重新拉取，注入到 system prompt 的独立 block
2. **小呦把 memory / honcho 注入内容误当作"当前对话上下文"有过至少一次**
   - Oreki 22:28 的"在吗"是真实对话；22:29 小呦回复**引用的内容不在 frozen snapshot 装载的 11:31 版本内**（11:31 时尚未发生 zhihu-search / M3 Plus / M3 vision 这些事件）
3. **session 从 11:31 持续运行到 22:28 之后，没有 reload**
   - `20260601_113141_ef0a50` 单一 session；frozen snapshot 装载的是 11:31 启动时 disk 状态

## 3. timeline_gap（必须显式标注）

**"在吗"那条具体引用内容是否来自 honcho volatile block，仍未闭环：**

- 22:29:26 的小呦回复**不可能**引用 22:30 之后才写入 honcho 的内容（时间顺序违反）
- 22:29:26 之前 honcho 已写入多少条？**没有精确的逐条写入时间戳**
- "在吗"回复里出现的"Plus 池月度配额 / zhihu-search / M3 vision"等内容**最早进入小呦视野的通道**是？
  - 假设 A：22:29 之前已 honcho_conclude 进系统 → honcho volatile recall
  - 假设 B：22:29 之前未 honcho_conclude，是 frozen snapshot 11:31 启动时**已包含**（但 11:31 启动时这些事件未发生）→ 假设 B 自相矛盾，**被排除**
  - 假设 C：22:28→22:29 之间，honcho 自身后台 session 把这些从别的 session 同步过来
- 正确做法是**先把假设 A 的时间戳对齐**再下结论，不在本报告里强行收口

## 4. rule_delta（短规则增量）

```yaml
trigger:
  - 小呦把 memory / honcho / summary / prefetch / 历史规则 / 刚写入的内容当成"当前对话刚发生的事实"回应
  - 同 session 内修改 MEMORY.md / USER.md / honcho_conclude 后，回答仍引用旧内容或混入新召回内容

action:
  - 先区分两个通道：
    1. MEMORY.md / USER.md frozen snapshot：session 启动 load_from_disk 冻结；同 session 写盘 ≠ prompt 刷新；新 session 才默认重新 load
    2. honcho volatile block：外部 memory provider 通过 system prompt block / prefetch / recall 注入；与 MEMORY.md snapshot 独立
  - 不要直接归因"用户刚说过"或"honcho 自动污染"或"旧 MEMORY 一定没刷新"
  - 用 session_start_time / memory file mtime+hash / prompt header / honcho record 时间交叉验证

verify:
  - case 必须列出：
    - session_id / session_start_time
    - MEMORY.md mtime / sha256 / char_count
    - prompt header
    - honcho volatile evidence
    - 被引用内容实际来自哪个通道
    - timeline_gap 是否存在（如存在显式标注）
```

## 5. Oreki 边界规则（保留不删）

> Oreki 2026-06-01 立下的 `honcho_memory_boundary_rule`（"不要把 honcho 自动注入和我的输入混在一起"）**保留**。
> 本 case 的 rule_delta 是**补充**：先区分 frozen snapshot 与 honcho volatile 两个独立通道，**再**做归因，不要把所有 memory 异常都甩给 honcho。

## 6. 后续动作（不立即执行，待 Oreki 批准）

- [ ] 把 `honcho_conclude` 的写入时间戳补齐：拉 26 条结论的 created_at 字段，与 22:29:26 对齐
- [ ] 拉 22:29 小呦那条回复原文，对比 11:31 启动时的 disk MEMORY.md 内容，定位"不在 11:31 版本内的引用"清单
- [ ] 如果 timeline_gap 收口，把假设 A 升格为 confirmed，并把这个 case 的 `contradictions: []` 改为对应页面
- [ ] 收口之前，rule_delta **不** 写入全局 honcho 长期规则

## 相关页面
- [[concepts/hermes-memory-injection-channels]] — 双通道机制详细说明
- [[hermes-agent-skills-cron-memory-2026]] — Hermes 三大能力总览
- [[agent-drift-patterns]] — Agent 漂移模式
- [[reports/open-questions]] — 其它未闭环的开放问题
