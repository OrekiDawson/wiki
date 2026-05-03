---
title: Andrej Karpathy LLM 编码指南
created: 2026-04-29
updated: 2026-04-29
type: concept
tags: [llm, concept, tool]
sources: ["raw/articles/karpathy-coding-guidelines.md"]
confidence: high
---

# Karpathy LLM 编码指南

来源：[forrestchang/andrej-karpathy-skills](https://github.com/forrestchang/andrej-karpathy-skills)，源自 [Andrej Karpathy 的推文](https://x.com/karpathy/status/2015883857489522876)。

## 核心问题

Karpathy 观察到的 LLM 编码陷阱：
- 代你做错误假设，不假思索地执行
- 不管理自身困惑，不寻求澄清，不呈现矛盾
- 喜欢把代码搞复杂，堆砌抽象，不清理死代码
- 会改动或删除自己理解不足的代码，即使与任务无关

## 四条原则

### 1. 编码前思考（Think Before Coding）
**不要假设。不要隐藏困惑。呈现权衡。**
- 明确说明假设，不确定就问
- 存在歧义时，呈现多种解释，不要默默选择
- 有更简单方案时提出异议
- 困惑时停下来，指出不清楚的地方

### 2. 简洁优先（Simplicity First）
**用最少的代码解决问题。不要过度推测。**
- 不添加要求之外的功能
- 不为一次性代码创建抽象
- 不添加未要求的"灵活性"或"可配置性"
- 200 行能写成 50 行就重写
- 检验标准：资深工程师会觉得这过于复杂吗？

### 3. 精准修改（Surgical Changes）
**只碰必须碰的。只清理自己造成的混乱。**
- 不"改进"相邻代码、注释或格式
- 不重构没坏的东西
- 匹配现有风格
- 注意到无关死代码时提一下，不要删除
- 检验标准：每一行修改都应能直接追溯到用户的请求

### 4. 目标驱动执行（Goal-Driven Execution）
**定义成功标准。循环验证直到达成。**

| 指令式 | 转化为可验证目标 |
|--------|----------------|
| "添加验证" | "为无效输入写测试，然后让它们通过" |
| "修复 bug" | "写重现 bug 的测试，然后让它通过" |
| "重构 X" | "确保重构前后测试都通过" |

多步骤任务先说明计划：
```
1. [步骤] → 验证: [检查方法]
2. [步骤] → 验证: [检查方法]
```

## 安装方式（Claude Code）

```bash
/plugin marketplace add forrestchang/andrej-karpathy-skills
/plugin install andrej-karpathy-skills@karpathy-skills
```

## 相关页面
- [[agent-persona-vs-execution]] — 对比：Karpathy 强调"冷酷执行"，而人格失控恰是执行层被人格层吞掉；两者互补
- [[agent-persona-failure-modes]] — 将 Karpathy 的执行准则与 reward hijack、谄媚失效链一起串联分析
