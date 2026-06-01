---
title: RL 训练三阶段决策框架
created: 2026-05-04
updated: 2026-05-10
type: concept
tags: [rl, training]
sources: []
confidence: medium
---


关联页面：[[rl-training-bible-xiaoyou-v1]]（训练圣经）、[[pong_v2_experiment]]（实验验证）
# RL 训练决策框架

## 三阶段原则

### Phase 1：观察期（0 ~ 300K 步）
- **目标**：验证方向是否正确
- **判断标准**：
  - `native_reward` 从基线（≈-21）向 -2~0 移动
  - `entropy` > 0.5（agent 还有探索空间）
  - `ep_len` 合理（不能 reward 不涨但 ep_len 暴涨）
- **动作**：只观察，不改任何超参数
- **停止条件**：native_reward 达到 -2~0 区间

### Phase 2：等待期（300K ~ 1M 步）
- **目标**：等待 agent 自发突破
- **判断标准**：继续监控上述三项指标
- **动作**：绝对不调 reward、不加 curriculum、不重启训练
- **风险**：entropy 塌缩到 0 → 立即停止，进入 Phase 3

### Phase 3：干预期（> 1M 步或异常信号）
- **触发条件**：
  - entropy < 0.5 持续 50K 步
  - native_reward 停滞 200K 步无改善
  - ep_len 异常暴涨但 reward 不涨
- **可选动作**：
  - 降低 `ent_coef`（增加利用）
  - 提高 `learning_rate`
  - 添加 curriculum（在简单场景训练后再切难）
- **原则**：每次只改一个，保持其他参数不变

## 核心原则

1. **真实 reward 优先**：native_reward 是唯一真实信号，shaped reward 是辅助
2. **不追 shaped reward**：累计 shaped reward 好看不等于 agent 真的在学
3. **entropy 是健康指标**：entropy → 0 意味着 agent 停止探索，已经死了
4. **不改原则**：方向确认后，忍住不动，等足够步数让 agent 自己学到

## 关键指标监控

| 指标 | 含义 | 健康值 | 危险信号 |
|------|------|--------|----------|
| native_reward | 真实累积 reward | -2 ~ 0 | < -10 |
| entropy | 探索多样性 | > 0.5 | < 0.2 |
| ep_len | 每轮步数 | 合理波动 | 暴涨但不涨 reward |
| shaped_reward | 辅助信号 | 参考 | 虚高是假象 |

## 记录格式

每次 checkpoint 记录：
```
Step: XXXXX
native_reward: X.XX
entropy: X.XXX
ep_len: XXXX
shaped_reward: X.XX
```

## Related
<!-- openclaw:wiki:related:start -->
- No related pages yet.
<!-- openclaw:wiki:related:end -->
