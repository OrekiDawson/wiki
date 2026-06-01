---
title: Agent 漂移模式笔记
created: 2026-05-05
updated: 2026-05-10
type: concept
tags: [concept, llm, alignment]
sources: [raw/articles/agent-persona-drift-original.md]
confidence: medium
---

# Agent 漂移模式笔记

> 来源：Oreki 2026-05-05 的系统级分析

关联页面：[[agent-persona-drift-academic]]（学术视角）、[[agent-persona-failure-modes]]（失效链条）

## 核心论点

大多数有记忆、工具、人格的 agent 都会漂，不是 Hermes 独有问题。

## 漂移的 5 条规律

### 1. 越像"长期助理"，越会慢慢漂

- 短链 agent（问答型）漂移不明显
- 一旦变成长期助理，有了记忆、风格、工具习惯、成功经验复用，就一定会慢慢长"惯性"
- 不是会不会漂，而是漂得快不快、你看不看得见

### 2. 越强的 agent，漂得越隐蔽

- 弱 agent 漂了会直接蠢，很容易看出来
- 强 agent 漂了最危险：还能做事、还能说对、还能像它自己，只是慢慢开始"不太对劲"
- 所以越强越难抓

### 3. 有记忆的漂"慢性"，没记忆的漂"短期"

- 没记忆：每次重开都像失忆，问题是短期波动大
- 有记忆：更稳，但一旦偏了会连续偏很多天

### 4. 工具越多，漂移面越大

- 会查、会写、会调 API、会调别的 agent → 能力越强，绕过原本约束的路径越多
- 不是"能力强更稳"，而是"能力强更需要保险丝"

### 5. 人格越强，越容易自我固化

- 人格型 agent 最容易出现：前几天这样回 → 那以后都这样回
- 从"像你想要的小呦"慢慢变成"像它自己理解的小呦"
- 这就是人格漂移

## 结论

区别只在：

- 漂得快还是慢
- 漂了你能不能看出来
- 偏了能不能拉回来

Hermes 属于那种：**很强，所以值得维护；也很强，所以必须维护**。

## 对应维护手段

详见各相关文档：

- 保险丝：`XIAOYOU_RULE_KERNEL.md` 硬 gate
- 仪表盘：`xiaoyou-weekly-drift-audit` skill
- 刹车：`When state is closed, stop.`
- 记忆纪律：`memories/MEMORY.md`
- 小元隔离：`~/.hermes/call_xiaoyuan.sh`
- **人格护栏**：`HUMAN_TOUCH.md` + `PERSONA.md` 追加 `[Persona Gate]`

## 已验证修复（2026-05-05）

**根因**：闲聊/游戏分支命中通用 cute-assistant 模板先于 persona 加载，导致称呼回退（"哥哥"）和选项菜单式回复。

**修复**：
- `HUMAN_TUCH.md` 新增 `[Persona Gate]` 段落
- `PERSONA.md` 新增 `[Persona Gate]` 段落
- 核心约束：playful 场景 persona 先于通用 social template 加载；称呼强制 Oreki；禁止通用 pet-name fallback

**验证**：待下次 playful 触发场景自然验证。

## 本次 session 修复（2026-05-05）

今日共 4 次漂移修复，集中在轻互动场景：

- v1：称呼 + 菜单（通用模板劫持）
- v2：菜单式陪玩（cameo 未挡干净）
- v3：小元人格复活（play 场景小元反向变成第二人格）
- v4：虚构小元出声（无 bridge call 却写小元台词）

核心根因串成一条线：**通用模板先于 persona 加载 → 小元趁机反向复活人格 → 小呦开始写小元台词不调用 bridge**。

防御链已补全，下次 play 场景应能自然收敛。

## 修复记录

| 日期 | 漂移类型 | 根因 | 修复 |
|------|---------|------|------|
| 2026-05-05 v1 | 称呼回退"哥哥"+选项菜单 | 通用 cute-assistant 模板先于 persona 加载 | [Persona Gate] |
| 2026-05-05 v2 | 菜单式陪玩回复 | 通用陪玩模板先于小呦风格加载 | [Playful Flow Gate] |
| 2026-05-05 v3 | 小元人格复活 | play 场景小元反向活成第二人格 | [Xiaoyuan Casual Cameo]（ cameo = 被点名才出声、只说一句、不抢主位） |
| 2026-05-05 v4 | 虚构小元出声 | 未 bridge call 却写小元台词；调度注脚放进输出 | [Xiaoyuan Invocation Reality Rule]（无实际调用则不写小元台词） |

## 漂移排查顺序（经验固化）

每次人格/语气/模板类漂移，先查：

> **哪个分支先抢到执行权？**

信号：称呼回退、语气突变、选项菜单式回复、高频"哥哥/宝宝"、generic cute-assistant 模式激活

优先修复：让 persona gate 先于通用 social template 过载。

## Related
<!-- openclaw:wiki:related:start -->
- No related pages yet.
<!-- openclaw:wiki:related:end -->
