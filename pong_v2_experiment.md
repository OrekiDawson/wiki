# Pong v2 实验记录

## 实验日期
2026-05-03

## v1 实验（失败）

**配置**：
- `alive_bonus = 0.01`
- `ent_coef = 0.01`
- `n_steps = 2048`
- `episodic_life = False`

**结果 @ 821K 步**：
- native_reward ≈ -21（随机水平，未学到）
- entropy → 0（塌缩，agent 已死）
- shaped_reward ≈ -13.4（假象，reward hacking）

**失败根因**：
`alive_bonus = 0.01` 太大，agent 发现"只要不死，每步就 +0.01"，学到了"不死策略"而非"赢球策略"。累计 shaped reward 好看是因为 agent 存活时间变长，而非真的在赢球。

---

## v2 实验（方向确认）

**配置**（只改了一个关键参数）：
- `alive_bonus = 0.001`（降低 10x）
- `ent_coef = 0.02`（提高 2x）
- `n_steps = 4096`（翻倍）
- `episodic_life = True`

**结果 @ 331K 步**：
| 指标 | v1 @ 821K | v2 @ 331K | 状态 |
|------|----------|----------|------|
| native_reward | ≈ -21 | **-1.21** | ✅ 进目标区间 |
| entropy | → 0 | **0.807** | ✅ 健康 |
| ep_len | ~760 | **2396** | ✅ 合理 |

**结论**：v2 方向确认，无需改动任何参数，等待 1M 步大评估。

---

## 技术细节

### ball_tracking 代码不生效
Pong-v5 的 `info` 字典中**没有** `ball_x` / `ball_y` 字段（和 Atari Pong 不同）。`train_pong_ppo_v2.py` 中的 `ball_tracking` 逻辑不会报错，但也不生效。

```python
# 这段代码在 Pong-v5 不生效
if 'ball_x' in info and 'ball_y' in info:
    ball_tracking = ...
    reward += ball_tracking
```

### alive_bonus 的正确范围
- `0.01`：太大，诱导不死策略
- `0.001`：合适，让 agent 专注赢球
- `0.0001` 或 `0`：太激进，agent 可能 early death 但没学到

### n_steps 的影响
- `2048`：标准，对抗 Pong 够用
- `4096`：减少更新次数，每次更新更稳定，适合高方差环境

---

## 训练日志

- 训练脚本：`/home/oreki/pong_baseline/train_pong_ppo_v2.py`
- 日志文件：`/home/oreki/pong_baseline/logs/pong_ppo_v2_20260503_171730.log`
- Checkpoint 目录：`/home/oreki/pong_baseline/logs/pong_ppo_v2_20260503_171731/`
- 目标步数：5M 步（预计 ~3 小时）
