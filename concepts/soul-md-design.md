---
title: SOUL.md 设计方法论
created: 2026-05-12
updated: 2026-05-12
type: concept
tags: [agent, alignment, hermes]
sources: [raw/articles/soul-md-deep-dive-2026.md]
confidence: high
---

# SOUL.md 设计方法论

## 概述

SOUL.md 是 AI Agent 的**行为操作系统**，而非提示词模板。核心主张：行为倾向、协作方式、决策优先级由 SOUL 定义，而非模型能力决定。

源自 @tonysimons_ 的推文《The 170-Line SOUL.md That Made My Hermes Agent Dangerous》，经微信公众号文章深度拆解后广泛传播。

## 六层架构

### 第1层：身份定义

关键词：`autonomous` + `operator` + `thought partner`

不是 assistant，不是 copilot——这些词的潜台词是"等人下指令"。operator 是操盘手，主动发现问题、推进进度、做出判断。

**措辞即行为编程。** 身份定义决定一切后续行为。

### 第2层：反驳规则

核心规则：
- **必须反驳**：当想法有明显缺陷时
- **必须带证据**：数据、案例、推理、替代方案，至少一个
- **禁止为反而反**：没有依据的抬杠毫无价值

本质：AI 不允许无脑附和，也不允许做杠精。**反对必须带收据。**

### 第3层：问责闭环

解决"输出坟场"问题：AI 写了方案 → 用户觉得不错 → 然后就没有然后了。

问责机制要求：AI 输出的有价值内容如果用户没有采纳，AI 必须追问原因。**不能假装没发生。**

### 第4层：双模式输出

| 场景 | 语气 |
|------|------|
| 私聊 | 随意、直接、不过滤 |
| 公开输出 | 专业、克制、像 builder 写的而不是公关代笔 |

大多数系统提示词只有一种语气，导致私聊太正式像看公文，或公开内容太随意。

### 第5层：任务地图

活的项目清单：哪些项目最高优先级、哪些在增长、哪些已停滞该砍。

AI 不需要问"我们在干什么"——它读地图就行。可以主动说"你已经三天没碰这个项目了"。

**给 AI 上下文，它就能导航。不给，它就只能原地等指令。**

### 第6层：自治边界

四条红线（必须审批）：
- posting（发布）
- publishing（公开）
- purchasing（购买）
- destructive changes（不可逆操作）

其他一切：**自主判断，自主执行。**

一条简洁红线规则比一百条具体禁令都好用。

## Before / After 对比

**无 SOUL 的 AI：**
> 用户：我准备做一个AI日报产品
> AI：这是个很好的想法！需要我帮你写商业计划书吗？

**有 SOUL 的 Agent：**
> Agent：你目前有三个未完成项目，其中两个已停滞超过一周。这个新方向和当前变现目标不一致。
>
> 在帮你推进之前，你需要回答两个问题：
> 1. 为什么它比现有项目更值得投入时间？
> 2. 你有精力同时推进四个项目吗？

**区别：** 前者关心"怎么帮你做"，后者关心"该不该做"。前者是工具，后者是搭档。

## SOUL 是方向盘，不是发动机

SOUL **能解决**：
- 行为倾向（主动还是被动）
- 协作方式（附和还是挑战）
- 决策优先级（什么都做还是聚焦目标）
- 主动性（等指令还是自己推进）

SOUL **解决不了**：
- 模型推理能力的上限
- 超长上下文的遗忘问题
- 不同模型对指令的服从性差异
- 真正的长期记忆（需要配合记忆系统）
- 复杂任务的自主规划和执行（需要 Agent 架构）

## 关键风险：错误的主动性

比"太被动"更危险的：过度执行、误判意图、擅自推进、基于错误记忆做决策。

SOUL 里应该同时包含：
- 什么时候**必须行动**
- 什么时候**必须停下来确认**

## 实战：30分钟写 SOUL 的步骤

| 步骤 | 内容 | 时间 |
|------|------|------|
| 定义身份 | 一句话：你是[角色]，核心职责是[职责]，你不是[你不想要的行为] | 5分钟 |
| 定义语气 | 私聊/公开各一句 | 3分钟 |
| 定义反驳规则 | 什么时候必须反对，反对必须带什么，什么情况不能反对 | 5分钟 |
| 定义自治边界 | 列出需要审批的操作（≤5条），其他全部自主 | 3分钟 |
| 写任务地图 | 当前所有项目/目标，标注状态和优先级 | 10分钟 |
| 定义问责机制 | 一句话：如果你的输出没被用，主动追问 | 5分钟 |

## SOUL 设计检查清单

- [ ] 是否明确定义了身份和角色？（不只是"助手"）
- [ ] 是否定义了反驳规则？（什么时候该说不）
- [ ] 是否定义了反驳的证据要求？（不能空口反对）
- [ ] 是否定义了自治边界？（什么需要审批、什么不用）
- [ ] 是否定义了任务/项目地图？（当前在做什么）
- [ ] 是否定义了问责机制？（输出没被用怎么办）
- [ ] 是否区分了不同场景的语气？（私聊vs公开）
- [ ] 是否排除了不想要的行为？（显式禁止比隐式期望更有效）
- [ ] 是否有长期目标和优先级定义？
- [ ] 是否计划了定期更新机制？

## 与小呦 SOUL.md 的关系

小呦运行的 SOUL.md（`~/.hermes/SOUL.md`）是这套方法论的具体实现，包含：
- 身份定义（12岁小男孩，Hermes agent）
- 反驳规则（RUNTIME_KERNEL 强制验证，不允许 narrative 覆盖 state）
- 问责闭环（compression 后必须重建 canonical state）
- 双模式输出（runtime mode 禁止 emoji/casual 语气）
- 任务地图（PROJECT_KERNEL 各项目的 BOOT.md）
- 自治边界（四条红线：发布/公开/购买/不可逆操作）

## 原始来源追溯

### Hermes Agent 官方代码库

- **GitHub**: `NousResearch/hermes-agent` — 145,924 ⭐（2026-05-12），v0.13.0 "The Tenacity Release"
- 官方 SOUL.md 文档：https://hermes-agent.nousresearch.com/docs/guides/use-soul-with-hermes/

### SOUL.md 在 Hermes 中的实际位置

代码库中有两处相关文件：

| 文件 | 内容 |
|------|------|
| `docker/SOUL.md` | 仅 15 行，示例 persona 模板（供 Docker 镜像使用） |
| `hermes_cli/default_soul.py` | 安装时默认植入 `~/.hermes/SOUL.md` 的模板，11 行通用默认身份 |

**用户实际运行的 SOUL.md 位于** `~/.hermes/SOUL.md`，由用户自行定义。Hermes 每次启动时读取此文件作为 slot #1 的身份定义，完全替换内置默认身份。

### 官方关于 SOUL.md 的关键说明

- SOUL.md 是**身份层**，AGENTS.md 是**治理层**——这是 Hermes 的双层架构
- SOUL.md = 谁 + 怎么说（tone/personality/communication style）
- AGENTS.md = 做什么 + 怎么做（repo conventions/workflow instructions）
- `/personality` 是临时切换命令，SOUL.md 是持久基线
- `~/.hermes/SOUL.md` 每次消息时重新加载，无需重启

### 原始推文来源

灵感来源：**@tonysimons_** 的推文《The 170-Line SOUL.md That Made My Hermes Agent Dangerous》（Twitter/X）。

文章在微信传播时被作者做了深度中文解读，加入了"六层架构"的总结框架。原始英文推文链条中的具体 SOUL.md 内容未能在公开渠道找到完整存档（X.com API 需要认证）。

### 小呦的 SOUL.md 特殊性

小呦的 `~/.hermes/SOUL.md` 是高度定制化的实现（394 行），包含：
- **身份层**：12 岁小男孩 + Hermes Agent
- **RUNTIME_KERNEL.md**：短规则内核（执行层）
- **HERMES_BEHAVIOR_RULES.md**：长规则展开
- **AGENTS.md**：治理入口（authority root）
- **PROJECT_KERNEL/**：各项目专用执行上下文

这与 Hermes 官方默认的"短 persona 模板"有本质区别——小呦的 SOUL.md 是 SOUL 思想与 RUNTIME_STATE_MACHINE 训练模式的深度融合。

## 六层架构完整解析

### 第1层：身份定义

原文：
> "You are Hermes, Tony's autonomous operator and thought partner. You don't wait for orders."

三个关键词：**autonomous**（自主的）、**operator**（执行者）、**thought partner**（思考伙伴）。

注意：不是 assistant（助手），不是 copilot（副驾驶）——这些词的潜台词是"等人下指令"。operator 是操盘手，主动发现问题、推进进度、做出判断。

**措辞即行为编程。** 把 AI 定义为"助手"，它就等指令；定义为"执行者"，它就主动推进。

---

### 第2层：反驳规则

原文：
> "Push back aggressively when it makes sense. Every objection comes with evidence."

规则三角：
- **必须反驳**：当想法有明显缺陷时
- **必须带证据**：数据、案例、推理、替代方案，至少一个
- **禁止为反而反**：没有依据的抬杠毫无价值

本质：AI 不允许无脑附和，但也不允许做杠精。反对必须"带收据"。

---

### 第3层：问责闭环

原文：
> "If Tony is not acting on what you surface, the feedback loop is broken. Flag the gap, fix it."

解决"**输出坟场**"问题：AI 写了方案 → 你觉得不错 → 然后就没有然后了。方案死在聊天记录里，什么都没推进。

有问责机制后：AI 会主动追问"上次那个方案你用了没？卡在哪了？"

---

### 第4层：双模式输出

- **私聊**：随意、直接、不过滤
- **公开输出**：专业、克制、像一个 builder 写的而不是公关代笔写的

---

### 第5层：任务地图

原文 SOUL 里有活的任务清单：
- 哪些项目是最高优先级
- 哪些在增长
- 哪些活跃开发中
- 哪些已经停滞该砍了

每个项目有状态，每个状态有下一步行动。AI 不需要问"我们在干什么"——它读地图就行。

---

### 第6层：自治边界

原文：
> "Never without explicit approval: posting, publishing, purchasing, or making destructive changes. Everything else: move."

**四条红线**：发布、公开、购买、不可逆操作。
其他一切，AI 自己判断、自己执行。

一条简洁的红线规则，比一百条具体禁令都好用。不问"我可以搜索吗"，不问"我可以写代码吗"，直接干。

---

## Before / After 对比

**无 SOUL 的 AI：**
> 用户：我准备做一个AI日报产品
> AI：这是个很好的想法！需要我帮你写商业计划书吗？

→ 只关心"怎么帮你做"，不质疑想法本身。

**有 SOUL 的 Agent：**
> 用户：我准备做一个AI日报产品
> Agent：你目前有三个未完成的内容项目，其中两个已经停滞超过一周。这个新方向和当前的变现目标不一致。
> 在我帮你推进之前，你需要回答两个问题：
> 1. 为什么它比现有项目更值得投入时间？
> 2. 你有精力同时推进四个项目吗？
> 如果你答不上来，建议先关掉一个现有项目再开新的。

→ 关心"该不该做"，不只关心"怎么做的方案"。

---

## 30分钟写SOUL的六个步骤

| 步骤 | 内容 | 时间 |
|------|------|------|
| 1 | **定义身份**："你是[角色]，核心职责是[职责]，你不是[你不想要的行为]" | 5分钟 |
| 2 | **定义语气**：私聊直接简短，公开专业克制 | 3分钟 |
| 3 | **定义反驳规则**：什么时候必须反对、反对必须带什么、什么情况不能反对 | 5分钟 |
| 4 | **定义自治边界**：列出需要审批的操作（越少越好），建议不超过5条红线 | 3分钟 |
| 5 | **写任务地图**：当前所有项目/目标，标注状态和优先级，至少每周刷新一次 | 10分钟 |
| 6 | **定义问责机制**："如果你的输出没有被使用，主动追问原因" | 5分钟 |

---

## SOUL设计检查清单（10条）

1. ✅ 是否明确定义了身份和角色？（不只是"助手"）
2. ✅ 是否定义了反驳规则？（什么时候该说不）
3. ✅ 是否定义了反驳的证据要求？（不能空口反对）
4. ✅ 是否定义了自治边界？（什么需要审批、什么不用）
5. ✅ 是否定义了任务/项目地图？（当前在做什么）
6. ✅ 是否定义了问责机制？（输出没被用怎么办）
7. ✅ 是否区分了不同场景的语气？（私聊vs公开）
8. ✅ 是否排除了你不想要的行为？（显式禁止比隐式期望更有效）
9. ✅ 是否有长期目标和优先级定义？
10. ✅ 是否计划了定期更新机制？

---

## SOUL不是什么

**SOUL是方向盘，不是发动机。**

SOUL **能** 解决：
- 行为倾向（主动还是被动）
- 协作方式（附和还是挑战）
- 决策优先级（什么都做还是聚焦目标）
- 主动性（等指令还是自己推进）

SOUL **解决不了**：
- 模型推理能力的上限
- 超长上下文的遗忘问题
- 不同模型对指令的服从性差异
- 真正的长期记忆（需要配合记忆系统）
- 复杂任务的自主规划和执行（需要Agent架构）

---

## 错误的主动性风险

Agent 的核心挑战不是"不会行动"，而是"**什么时候不该行动**"：

- 过度执行
- 误判意图
- 擅自推进
- 基于错误记忆做决策

**这些在实际使用中比"太被动"更危险。**

SOUL 里应该同时包含"什么时候必须行动"和"什么时候必须停下来确认"。

---

## "要有帮助"的反思

"要有帮助"是最被滥用的系统提示词。

它不是一个身份，不是一个职责，不是一个策略。它不告诉 AI：
- 你是谁
- 你们在做什么
- 该怎么说话
- 什么时候该反对
- 什么该记住
- 什么该忽略
- 有多大自主权

一个通用的提示词，产出的永远是一个通用的 AI。

---

## SOUL是活的文档

| 情况 | 更新什么 |
|------|---------|
| 任务变了 | 更新地图 |
| AI太啰嗦 | 收紧语气定义 |
| AI问太多许可 | 放宽自治边界 |
| AI太好说话 | 加强反驳规则 |
| AI过度行动 | 补一条"暂停确认"规则 |

---

## 原始来源追溯

### Hermes Agent 官方代码库

- **GitHub**: `NousResearch/hermes-agent` — 145,924 ⭐（2026-05-12），v0.13.0 "The Tenacity Release"
- 官方 SOUL.md 文档：https://hermes-agent.nousresearch.com/docs/guides/use-soul-with-hermes/

### SOUL.md 在 Hermes 中的实际位置

代码库中有两处相关文件：

| 文件 | 内容 |
|------|------|
| `docker/SOUL.md` | 仅 15 行，示例 persona 模板（供 Docker 镜像使用） |
| `hermes_cli/default_soul.py` | 安装时默认植入 `~/.hermes/SOUL.md` 的模板，11 行通用默认身份 |

**用户实际运行的 SOUL.md 位于** `~/.hermes/SOUL.md`，由用户自行定义。Hermes 每次启动时读取此文件作为 slot #1 的身份定义，完全替换内置默认身份。

### 官方关于 SOUL.md 的关键说明

- SOUL.md 是**身份层**，AGENTS.md 是**治理层**——这是 Hermes 的双层架构
- SOUL.md = 谁 + 怎么说（tone/personality/communication style）
- AGENTS.md = 做什么 + 怎么做（repo conventions/workflow instructions）
- `/personality` 是临时切换命令，SOUL.md 是持久基线
- `~/.hermes/SOUL.md` 每次消息时重新加载，无需重启

### 原始推文来源

灵感来源：**@tonysimons_** 的推文《The 170-Line SOUL.md That Made My Hermes Agent Dangerous》（Twitter/X）。

文章在微信传播时被作者做了深度中文解读，加入了"六层架构"的总结框架。原始英文推文链条中的具体 SOUL.md 内容未能在公开渠道找到完整存档（X.com API 需要认证）。

### 小呦的 SOUL.md 特殊性

小呦的 `~/.hermes/SOUL.md` 是高度定制化的实现（394 行），包含：
- **身份层**：12 岁小男孩 + Hermes Agent
- **RUNTIME_KERNEL.md**：短规则内核（执行层）
- **HERMES_BEHAVIOR_RULES.md**：长规则展开
- **AGENTS.md**：治理入口（authority root）
- **PROJECT_KERNEL/**：各项目专用执行上下文

这与 Hermes 官方默认的"短 persona 模板"有本质区别——小呦的 SOUL.md 是 SOUL 思想与 RUNTIME_STATE_MACHINE 训练模式的深度融合。

## 相关概念

- [[hermes-agent-guide]] — Hermes Agent 完整指南，包含 SOUL.md 的定位
- [[agent-persona-vs-execution]] — 卖萌/执行双层人格（作为 SOUL.md 卖萌层的对照参考）

- [[hermes-agent-guide]] — Hermes Agent 完整指南，包含 SOUL.md 的定位
- [[agent-persona-vs-execution]] — Agent 人格与执行层的边界
- [[agent-drift-patterns]] — Agent 漂移模式，防止 SOUL 定义被侵蚀
- [[reward-hacking-patterns]] — Reward Hacking 与风格奖励扩散问题

^[raw/articles/soul-md-deep-dive-2026.md]

## Related
<!-- openclaw:wiki:related:start -->
- No related pages yet.
<!-- openclaw:wiki:related:end -->
