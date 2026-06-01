---
title: AIP（智能体互联协议）
created: 2026-05-10
updated: 2026-05-10
type: entity
tags: [aip, agent-standards, ai-governance]
sources: [raw/articles/agent-policy-20260508.md]
confidence: high
---

# AIP（智能体互联协议）

**Agent Interconnection Protocol**，中国自主制定的国家级智能体互联国家标准协议。

关联页面：[[agent-policy-2026]]（政策背景）、[[ai-agent-governance-eu-us-cn]]（国际对比）

## 基本信息

| 项目 | 内容 |
|------|------|
| **制定机构** | 中国电子技术标准化研究院 + 北京邮电大学人工智能学院 |
| **标准归口** | 全国信标委人工智能分委会（TC28/SC42） |
| **指导单位** | 工信部科技司 |
| **开源社区** | 开放原子开源基金会（AtomGit 平台） |
| **首版代码** | 2025年10月27日 开源发布 |
| **报批时间** | 预计 2026 年底完成报批 |
| **试点企业** | 华为、蚂蚁集团、小米、360 等 50+ 家 |

## 标准体系（7项国标）

1. 总体架构
2. 智能体身份码
3. 身份管理
4. 智能体描述
5. 智能体发现
6. 智能体交互
7. 智能体工具调用

## 技术定位

AIP 采用**多中心分层架构**，与其他主流协议的核心差异：

| 协议 | 解决的核心问题 | 开发方 |
|------|---------------|--------|
| **MCP** | 模型与工具的连接 | Anthropic |
| **A2A** | 智能体间任务协作 | Google |
| **ANP** | 去中心化智能体网络 | 开源社区 |
| **AIP（中国）** | **智能体身份授权与管理 + 可信互联 + 可管可控** | 中国信标委/电子标准院/北邮 |

AIP 的独特优势在于**增加智能体身份授权与管理的功能**，实现"可以使用工具、可以交互、可管控、可定责"。

## 与 Hermes 生态的关联

AIP 与 [[agent-policy-2026|《智能体规范应用与创新发展实施意见》]] 强关联，是文件中的核心技术标准。Hermes 的 MCP 工具链主要解决"模型→工具"的连接，而 AIP 解决"Agent→Agent/平台"的身份与授权层。未来可能形成 **MCP（工具层）+ A2A（协作层）+ AIP（身份层）** 的分工格局。

## 来源
- AIP 开源社区：https://aip.openatom.tech
- 搜狐报道（360加入试点）：https://www.sohu.com/a/951530474_362225
- CSDN 技术分析：https://blog.csdn.net/qq_31329259/article/details/156205845

## Related
<!-- openclaw:wiki:related:start -->
- No related pages yet.
<!-- openclaw:wiki:related:end -->
