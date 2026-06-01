---
title: Hermes Agent 实战笔记：Skills/Cron/Memory 三大核心能力
created: 2026-05-13
updated: 2026-05-13
source: https://mp.weixin.qq.com/s/srsyUKuoWaVluNaIq9CBbw
author: 花叔
tags: [hermes, agent, skill, cron, memory, 实战]
type: concept
confidence: high
---

## 来源

公众号文章《别人教你安装 Hermes，我教你让它自己学会干活》
作者：花叔（AI进化论）
抓取时间：2026-05-13

---

## 核心观点

> AI 不是用来陪聊的，是用来干活的。

作者演示了如何把 Hermes Agent 从"只会回答问题的聊天机器人"变成"真正会自己干活的数字员工"。

---

## 第一课：Skills —— AI 的 SOP

### 什么是 Skill

类比：新员工入职 → 教一遍 → 写成 SOP → 下次自己翻 SOP 干活

Hermes Skill = 可复用的事务流程模板。

### 实战例子：写飞书会议纪要

**第一次（手动触发）：**
1. 加载 lark-doc Skill → 学习飞书文档 API
2. 加载 lark-contact Skill → 查找参会人员
3. 调用 API 创建文档
4. 写入内容
5. 返回链接

结果：15 分钟，15 次工具调用

**关键动作：自动保存为新 Skill**

**第二次（自动复用）：**
直接加载上次保存的 Skill

结果：30 秒，3 次工具调用

> 这就是自我进化 —— 它不只是执行命令，它在积累经验。

### Skills 分类（作者这台机器装了 60+）

| 类别 | 示例 |
|------|------|
| 飞书集成 | lark-doc, lark-sheets, lark-calendar, lark-im（23个）|
| 开发工具 | github-pr-workflow, codebase-inspection, debugging |
| 创意设计 | architecture-diagram, ascii-art, infographic, manim-video |
| 运维部署 | swarmclaw, webhook-subscriptions, browser-harness |
| 内容创作 | wechat-article-writing, youtube-content, blogwatcher |

---

## 第二课：Cron —— AI 按时上班

### 基础用法

创建定时任务（工作日 8:30~17:30 每小时喝水提醒）：

```bash
hermes cron create "30 8-17 * * 1-5"
# prompt: 查询当前时间和天气，写一段喝水提醒，通过 feishu 发送
```

Cron 任务在独立 session 里跑，没有上下文，所以 prompt 必须是**自包含**的。

### 链式任务（context_from）

任务 A（每天早上 8:00）：抓取 GitHub 动态 + 新闻 + 天气 → 输出摘要
任务 B（每天早上 8:15）：读取任务 A 输出 → 生成日报 → 推送到飞书

两个独立任务串成流水线，类比：两个员工，一个收集信息，一个写报告。

---

## 第三课：Memory —— 你不用反复说

大多数 AI 的共同问题：**每次对话都是失忆的**

Hermes Memory 系统把关键信息保存到持久存储，每次启动新会话自动加载。

### 作者记住的内容示例

- 用户偏好：用飞书作为主要协作工具，飞书账号是 li guo，在杭州
- 设备信息：Android 11 平板，RK3566 芯片，局域网 IP

---

## 部署三分钟入门

```bash
# 一行安装
curl -fsSL https://raw.githubusercontent.com/NousResearch/hermes-agent/main/scripts/install.sh | bash

# 交互配置
hermes setup

# 国内推荐：通义千问（DashScope）、Kimi、MiniMax

# 验证运行
hermes chat -q "画一只 ASCII 小猫"
```

---

## 关键认知

1. **Skills = SOP 积累**：完成任务后自动保存，下次的成本从 15 分钟降到 30 秒
2. **Cron = 按时触发**：把"我让 AI 干活"变成"AI 自己按时干活"
3. **Memory = 持久记忆**：关键信息一次说，长期记住，不用重复
4. **自我进化能力**：Agent 不只是执行命令，它在积累经验

---

## 与 Oreki 现有配置的对照

| 文章概念 | Oreki 现状 |
|---------|-----------|
| Skills | `~/.hermes/skills/` 已有 60+ skills，含 doudizhu、pokemon-player、mario-ppo 等 |
| Cron | 已有 sleep-guardian、daily consolidate 等 cron 任务 |
| Memory | Honcho + Mem0 双系统持久化，用户偏好已写入 MEMORY.md |
| 喝水提醒类场景 | 尚未实现（可参考文章方式新建 cron）|
| 链式任务 | 尚未实践（context_from 模式）|

## Related
<!-- openclaw:wiki:related:start -->
- No related pages yet.
<!-- openclaw:wiki:related:end -->
