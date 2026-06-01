---
title: Hermes v0.12.0 Curator 发布
created: 2026-05-04
updated: 2026-05-10
type: concept
tags: [tool, llm, hermes]
sources: []
confidence: medium
---

# Hermes Agent v0.12.0 Curator Release（管家版）速览

关联页面：[[hermes-agent-guide]]（完整指南）、[[agent-persona-vs-execution]]（Agent 行为边界）

> 来源：微信公众号文章 https://mp.weixin.qq.com/s/KEJcgvpCslOwY_BFA33eKw
> 抓取时间：2026-05-03
> 存档原因：LLM/AI 相关理论、Agent 行为准则、工具使用心得

## 核心主题：Curator 自动整理 Skill 库

这次更新官方代号 **The Curator release（管家版）**。

### Curator 能做什么

- 默认**每天跑一次**（可配置间隔）
- 给每个 skill 打分
- 把功能重复的合并到一起
- 把已经没用的清理掉（stale 30天，archive 90天）
- 写一份报告告知干了什么
- **内置核心 skill 有保护**，不会被误删

### Curator 相关命令

```bash
hermes curator status          # 看用得最多/最少的 skill
hermes curator run             # 手动触发一次整理
hermes curator pause/resume    # 暂停/恢复
hermes curator pin/unpin <名>  # 保护某个 skill 不被整理
hermes curator restore <名>    # 恢复已归档的 skill
```

### 判定标准

| 状态 | 条件 |
|------|------|
| active | 正常使用 |
| stale | 30天未使用 |
| archived | 90天未使用（可恢复，不自动删） |

---

## 其他重点更新

### 自我改进能力升级
- 改成按评分卡打分，不再让模型"自由发挥"，更稳定
- 优先更新刚用过的那个 skill
- 后台进程现在能正确继承主进程的模型和账号
- 后台进程只允许改记忆和 skill，不会再误用 shell 或上网
- 上下文更干净，不会被上一轮工具消息干扰

### 新模型供应商
- **LM Studio**：升级为正式 provider，专属鉴权、hermes doctor 检查、实时拉模型列表
- **GMI Cloud**
- **Azure AI Foundry**：自动检测、完整接入
- **MiniMax OAuth**：浏览器一键登录

### 新聊天平台
- **Microsoft Teams**（第 19 个平台）
- **腾讯元宝**（第 18 个平台，文字媒体都能发）

### Skill 大扩张（默认内置）
- **ComfyUI v5**：从可选升级为默认内置
- **TouchDesigner-MCP**：默认内置，新增 GLSL、后期特效、音频、几何参考文档
- **Humanizer**：把 AI 腔文字洗成正常人话
- **claude-design**：生成 HTML 演示页
- **design-md**：写 DESIGN.md 规范文档
- **airtable**：接 Airtable
- **pretext**：浏览器创意演示
- **spike/sketch**：一次性实验脚本和静态 HTML 原型

### Spotify / Google Meet 原生支持
- Spotify：7 个工具（播放、搜索、队列、歌单、设备控制），浏览器一键登录，可用 cron 定时
- Google Meet：加入会议、转录、发声、跟进全套流水线

### TUI 体验
- 冷启动时间砍掉约 **57%**
- 支持 LaTeX 渲染
- /reload-skills 热加载，改了 skill 不用重启
- /resume 选择器里按删除会话、修饰键鼠标滚轮行级滚动

### 几个值得注意的变化
- **默认关掉密钥脱敏**：之前经常把代码 patch API 返回弄花
- 模型 catalog 改成远端拉取（OpenRouter/Nous Portal 有新模型不用等 Hermes 发版）
- 多模态图像智能路由：根据模型实际有没有视觉能力来分发
- Prompt 缓存默认从 5 分钟延长到 1 小时
- **回滚**：Kanban 协作看板、computer-use cua-driver、内置 BOOT.md hook

---

## 谁该升级

- 想让 skill 库自动整理 → **优先升级**
- 在用 LM Studio / GMI Cloud / Azure AI → **优先升级**
- Termux/Android 环境有 Python 3.13 包冲突问题（fal-client 1.0.0、rich 15.0.0、anthropic 0.18.0），需先修复环境再升级

## Related
<!-- openclaw:wiki:related:start -->
- No related pages yet.
<!-- openclaw:wiki:related:end -->
