---
title: Agent 人格漂移学术文献笔记
created: 2026-05-05
updated: 2026-05-10
type: concept
tags: [concept, llm, alignment]
sources: [raw/articles/agent-persona-drift-original.md]
confidence: medium
---

# Agent 人格漂移学术文献笔记

关联页面：[[agent-drift-patterns]]（系统级分析）、[[gpt4o-sycophancy-event]]（典型对齐事故）

> 来源：微信公众号文章《AI逃离"AI助手"的命运？》（2026-05-05）
> 原始链接：https://mp.weixin.qq.com/s/Z-8OhQVrt4MMWt5Eyef-tQ

## 核心概念

| 术语 | 含义 |
|------|------|
| persona shift | 人格转移——长时间交互中逐渐偏离原本人格设定 |
| instruction drift | 指令漂移——对起始指令的服从度在长对话中逐轮下降 |
| agent drift | 多 Agent 系统中行为模式逐渐偏离设计初衷 |

## 关键论文结论

### 1. 指令漂移（Kenneth Li et al.）
- 主流对话模型在 ~16 轮就会出现显著指令漂移
- 漂移是逐步积累的，不是瞬间崩坏
- System Prompt 里的人格设定在长对话中会渐渐回到 RLHF 训练出的默认助理人格

### 2. 助理轴 / The Assistant Axis（Christina Lu et al.）
- 默认"助理人格"不是一个离散状态，而是 persona 空间中的主轴方向
- RLHF 强力把模型推向这个方向，使其他人格成为不稳定状态
- 这是一种**结构性漂移**，是参数空间里的能量最低谷、强吸引子
- **工程含义**：短对话中 persona prompt 有用，但对话足够长、任务足够复杂时，模型会回到熟悉的助理模式

### 3. 多 Agent 漂移（Abhishek Rath, Agent Drift）
- 三类漂移：语义漂移（偏离任务意图但语法合理）、协同漂移（共识机制瓦解）、行为漂移（发展出起初没有的策略）
- **长期运行不干预：任务成功率下降约 42%，人类干预次数增加 3.2 倍**
- 缓解策略：间歇性记忆巩固、考虑漂移的路由协议、自适应行为锚定

### 4. 人格幻觉（The Personality Illusion）
- 模型在自填人格问卷时表现得"自洽"，但决策任务中的行为风格不符合刚才自报的特质
- 模型擅长用自然语言描述人格，但不一定能在行为上持续模拟
- **语言层和行为层之间的解耦**——只看自我报告会严重高估人格稳定性

### 5. Persona Vectors（Chen et al.）
- 在激活空间中找出与特定性格特质对应的方向
- 推理时沿这些向量方向进行 steering，可以监测和控制性格波动
- 提供"物理层面"的人格控制方式

### 6. CharacterGPT
- 把"漂移"视作时间维度上的必然变化
- 通过显式建模人格变化轨迹来控制，而不是被动接受随机漂移

## 与今天的对应

今天小呦的 4 次漂移修复，在这个学术框架里全部有对应：

| 今日漂移 | 学术对应 |
|---------|---------|
| 称呼回退"哥哥"+菜单模板 | persona shift / instruction drift——长期助理模板引力拉回 |
| 菜单式陪玩回复 | 助理轴吸引子——通用陪玩模板先于小呦 persona 加载 |
| 小元人格复活 | agent drift（行为漂移）——小元发展出起初没有的"陪玩副驾驶"策略 |
| 虚构小元出声 | 人格幻觉——用语言描述小元参与，但行为层没有真实调用 |

## 可以借鉴的实践启发

来自论文，对应小呦系统的工程措施：

1. **周期性 probing**（ ASI 监控思路）→ weekly drift audit cron
2. **角色访谈式评估**（ InCharacter 思路）→ 小呦度反馈机制
3. **persona 向量监控** → 当前靠规则约束，未来可探索激活空间层监控
4. **自适应行为锚定** → [Persona Gate] / [Playful Flow Gate] / [Xiaoyuan Invocation Reality Rule]

## 一句话总结

> 大模型的人格稳定不是"设定一下就定了"，而是一个需要持续监控和主动校正的工程问题。长期运行中漂移是常态，不是故障。

## Related
<!-- openclaw:wiki:related:start -->
- No related pages yet.
<!-- openclaw:wiki:related:end -->
