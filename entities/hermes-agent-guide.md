---
title: Hermes Agent 从入门到精通
created: 2026-04-29
updated: 2026-04-29
type: entity
tags: [hermes, agent, skill, memory, tool]
sources: [橙皮书 v260407 by 花叔]
confidence: high
---

- **来源：** 橙皮书 v260407，作者「花叔」（公众号「花叔」/ B站「AI进化论-花生」）
- **基于版本：** Hermes Agent v0.7.0（当前 Oreki 机器是 v0.11.0，部分细节可能更新）
- **标签：** #hermes #agent #skill #memory #mcp #rl

---

## 核心架构

```
学习循环 → 三层记忆 → Skill系统 → 40+工具 → 多平台Gateway
```

---

## 学习循环（5个环节）

1. **策划记忆** — 每轮对话结束后主动决定哪些值得写入
2. **自主创建 Skill** — 复杂任务完成后自动提炼成 `.md` 文件
3. **Skill 自改进** — 用户反馈触发 Skill 自动更新
4. **FTS5 跨会话召回** — SQLite + FTS5 按需检索历史，不全量加载
5. **Honcho 用户建模** — 可选，推理用户深层偏好（包括言行不一致）

> Mitchell Hashimoto 手动往 CLAUDE.md 里加规则的过程，Hermes 把它自动化了。

---

## 三层记忆

| 层 | 问题 | 存储 |
|----|------|------|
| 会话记忆 | 发生了什么 | SQLite + FTS5（按需检索） |
| 持久记忆 | 你是谁 | SQLite，跨会话保持 |
| Skill 记忆 | 怎么做事 | `~/.hermes/skills/*.md` |

- 所有数据在 `~/.hermes/`，迁移打包整个目录即可
- 无自动过期机制，长期使用需定期清理过时 Skill

---

## Skill 系统

**三种来源：**
- Bundled Skills（内置 40+）
- Agent 自主创建
- Skills Hub（社区）

**标准格式（SKILL.md）：**
```markdown
# Skill 名称
## 触发条件
（具体场景描述，越具体命中率越高）
## 行为规则
（步骤、约束、格式）
## 示例
（输入→输出示例）
## 不要做什么（可选）
```

**互通标准：** agentskills.io — Claude Code / Cursor / Gemini CLI 的 Skill 可直接复制到 `~/.hermes/skills/` 使用

**调试：**
- 问 Hermes「你现在加载了哪些 Skill？」
- 查日志 `~/.hermes/logs/`

---

## 工具分类

| 类别 | 工具 |
|------|------|
| 执行类 | terminal, code_execution, file |
| 信息类 | web, browser, session_search |
| 媒体类 | vision, image_gen, tts |
| 记忆类 | memory, skills, todo, cronjob |
| 协调类 | delegation, moa, clarify |

- **Toolset 机制**：config.yaml 按需启用，工具越少注意力越集中
- **子 Agent 委派**：最多 3 个并发，各有独立上下文和受限工具集

---

## MCP 集成

```yaml
mcp_servers:
  github:
    command: "npx"
    args: ["-y", "@modelcontextprotocol/server-github"]
    env:
      GITHUB_PERSONAL_ACCESS_TOKEN: "ghp_xxxxx"
```

- stdio（本地子进程）/ HTTP SSE（远程服务器）两种方式
- 支持 per-server 工具过滤

---

## 安装与配置

```bash
# 一键安装
curl -fsSL https://raw.githubusercontent.com/NousResearch/hermes-agent/main/scripts/install.sh | bash

# 最小配置
# ~/.hermes/config.yaml
model:
  provider: openrouter
  api_key: sk-or-xxxxx
  model: anthropic/claude-sonnet-4
terminal: local
```

**部署成本：** $5/月 VPS + 模型 API 费（轻度使用 $2-5/月）

---

## Hermes vs OpenClaw 关键区别

| 维度 | Hermes | OpenClaw |
|------|--------|---------|
| 核心理念 | 自改进学习循环 | 配置即行为（SOUL.md） |
| Skill 维护 | Agent 自动创建+自改进 | 人工编写维护 |
| 生态规模 | 40+ 内置 + MCP 6000+ | ClawHub 44000+ 社区 Skill |
| 适合场景 | 自主后台+自改进 | 配置透明、生态成熟 |

> 两者采用 agentskills.io 标准，Skill 可互通，不是替代关系。

---

## 相关页面
- [[agent-persona-vs-execution]] — Hermes 的 Skill 系统本身也是人格边界的一种实现——"怎么做"与"不能做成什么样"互补
- [[agent-persona-failure-modes]] — [[hermes-agent-guide]] 中的 Skill 自改进机制如果没有边界约束，可能滑向 reward hijack

## 注意事项
- 2026年4月起 Anthropic 封禁第三方工具通过 Claude 订阅访问，建议用 OpenRouter 或 Nous Portal
- 记忆污染：早期写入的错误信息会持续影响，需定期审查 `~/.hermes/skills/`
- Skill 冲突：触发条件重叠时行为可能异常，遇到先查 Skill 列表

## Related
<!-- openclaw:wiki:related:start -->
- No related pages yet.
<!-- openclaw:wiki:related:end -->
