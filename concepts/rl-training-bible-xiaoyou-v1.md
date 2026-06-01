---
title: 通用游戏 RL 训练圣经 v1
created: 2026-05-04
updated: 2026-05-10
type: concept
tags: [rl, training, game-ai, ppo]
sources: []
confidence: medium
---


关联页面：[[reward-hacking-patterns]]（Reward Hacking 案例）、[[rl_training_decision_framework]]（决策框架）
# 通用游戏 RL 训练圣经（给小呦）

## 前言

你不是"会调 PPO 的 agent"。

你真正的职责是：

-   建立可复现训练系统
-   建立失败诊断系统
-   建立实验治理系统
-   建立长期演化能力
-   建立环境理解能力
-   建立 reward engineering 能力
-   建立 observation abstraction 能力
-   建立 curriculum design 能力
-   建立 evaluation discipline
-   建立 anti-self-deception 机制

你不是在"跑训练"。

你是在：

"建立一个能长期解决复杂游戏环境的实验文明。"

# 第一部分：RL 的真正本质

## RL 不是"让 AI 玩游戏"

RL 的本质是：

在不可完全监督的环境中， 通过 delayed reward， 学习长期行为策略。

核心困难：

-   reward delayed
-   sparse reward
-   exploration
-   credit assignment
-   non-stationary policy
-   distribution shift
-   local optimum
-   catastrophic forgetting
-   environment randomness
-   observation aliasing
-   partial observability

绝大部分训练失败：

不是代码坏了。

而是：

agent 根本没有收到"正确的学习信号"。

# 第二部分：永远先做的事

## Rule 0：永远先验证环境

训练前必须回答：

1.  observation 真在变化吗
2.  action 真生效吗
3.  reward 真变化吗
4.  env.step() 真推进了吗
5.  done 真合理吗
6.  reset 真工作吗
7.  deterministic 模式是否一致
8.  seed 是否可复现
9.  frame stack 是否正确
10. wrapper 是否污染状态

任何一个没验证：

禁止开始 PPO。

## Rule 1：随机策略先跑

永远先跑：

-   random policy
-   no-op policy
-   always-left
-   always-right
-   always-jump
-   scripted baseline

因为：

你必须知道：

"环境最低行为是什么。"

否则：

你连 PPO 有没有进步都不知道。

## Rule 2：建立 Fast Path Baseline

所谓 Fast Path Baseline：

不是"最终系统"。

而是：

"最短时间验证 RL 链路通了。"

例如：

Flappy Bird：

-   先 dense reward
-   再 sparse reward

Mario：

-   先只学向右
-   再学跳跃

Pokemon：

-   先学移动
-   再学探索

MiniGrid：

-   先 Empty
-   再 DoorKey

# 第三部分：游戏分类学

## 类型 1：Reactive Game

特点：

-   当前 observation 足够
-   不需要长期记忆
-   动作即时反馈

例子：

-   Pong
-   Breakout
-   CartPole
-   部分赛车

适合：

-   PPO
-   DQN
-   A2C

危险：

agent 会 exploit physics bug。

## 类型 2：Timing Game

特点：

-   精确时机
-   delayed consequence
-   需要 momentum prediction

例子：

-   Flappy Bird
-   Mario
-   Sonic

困难：

PPO 非常容易学出：

"保守活着"。

不是"真正前进"。

## 类型 3：Sparse Exploration Game

特点：

-   长时间无 reward
-   地图探索
-   不可逆错误

例子：

-   Montezuma
-   Pitfall
-   Pokemon

重点：

不是 optimization。

而是 exploration。

## 类型 4：Strategic Game

特点：

-   长 horizon
-   多阶段策略
-   resource planning
-   opponent modeling

例子：

-   StarCraft II
-   Dota2
-   Civilization

重点：

不是 PPO。

而是：

-   hierarchy
-   planning
-   self-play
-   population training

# 第四部分：Observation 设计

## 永远不要默认 observation 是正确的

错误思想：

"环境给什么就学什么。"

正确思想：

"我要确认 observation 是否包含可学习信息。"

## 像素输入的问题

像素非常昂贵。

因为：

-   高维
-   noisy
-   sample inefficient
-   容易学背景
-   容易学 UI
-   容易过拟合动画

所以：

不是所有游戏都该直接喂像素。

## Structured Observation

很多游戏更适合：

-   坐标
-   速度
-   距离
-   碰撞状态
-   tile info
-   RAM

例如：

Flappy Bird：

比起像素：

-   bird_y
-   bird_vel
-   pipe_dx
-   pipe_gap_center

往往更强。

## Observation 不足的典型症状

### 症状 1

agent 永远晚一步。

说明：

缺预测信息。

### 症状 2

agent 只能固定套路。

说明：

observation 无法区分关键状态。

### 症状 3

reward 很高但行为很蠢。

说明：

observation 泄露 shortcut。

# 第五部分：Reward Engineering

## Rule：reward 是"教学系统"

reward 不是分数。

reward 是：

"你希望 agent 学什么。"

## Dense Reward vs Sparse Reward

Dense：

优点：

-   学得快

缺点：

-   容易作弊

Sparse：

优点：

-   更接近真实目标

缺点：

-   学不会

真正系统：

通常需要 curriculum。

## Reward Hacking

永远默认：

agent 会钻空子。

例子：

### Flappy Bird

永不 flap。

### Mario

原地蹭 reward。

### 赛车

原地转圈。

### Pokemon

撞墙 farming。

## 设计 reward 时必须问

1.  是否能 exploit
2.  是否能 stationary farm
3.  是否会导致局部最优
4.  是否鼓励错误行为
5.  是否 punish exploration
6.  是否尺度过大
7.  是否 reward variance 失衡

# 第六部分：PPO 真正会发生什么

## PPO 不是"稳定万能算法"

PPO 只是：

"相对不容易炸。"

它仍然会：

-   entropy collapse
-   policy drift
-   catastrophic forgetting
-   reward oscillation
-   premature convergence
-   local optimum lock

## PPO 最经典问题

### 问题 1：策略坍缩

表现：

-   entropy→0
-   行为固定
-   exploration 消失

结果：

突然从 100% 掉到 0%。

### 问题 2：Value 崩坏

表现：

-   explained_variance 负数
-   reward 巨震荡

### 问题 3：Clip 卡死

表现：

-   approx_kl 很低
-   学不动

## PPO 监控指标

必须长期记录：

-   entropy_loss
-   approx_kl
-   clip_fraction
-   explained_variance
-   value_loss
-   policy_gradient_loss
-   episode_reward
-   episode_length

# 第七部分：实验治理

## Rule：永远不要覆盖实验

目录必须：

env/seed/steps/time/hash

例如：

logs/ Mario-v1/ seed1_20260508_120k/ seed2_20260508_120k/

## Rule：checkpoint 必须长期保存

不要只存 final。

必须：

-   每 10k checkpoint
-   best checkpoint
-   final checkpoint

因为：

PPO 经常后期退化。

## Rule：评估独立于训练

训练环境：

-   stochastic

评估环境：

-   deterministic
-   固定 seeds
-   多 episode

## Rule：禁止 narrative inflation

错误：

"模型学会了。"

正确：

"在当前 eval protocol 下达到 X%。"

# 第八部分：失败诊断系统

## 第一级：环境层

检查：

-   action 是否工作
-   reward 是否变化
-   reset 是否正确

## 第二级：Observation 层

检查：

-   observation 是否变化
-   是否存在 aliasing
-   是否缺关键状态

## 第三级：Reward 层

检查：

-   reward 是否过 sparse
-   是否能 exploit
-   是否 punish exploration

## 第四级：Optimization 层

检查：

-   learning rate
-   entropy
-   clip range
-   batch size

## 第五级：Algorithm 层

检查：

-   PPO 是否适合
-   是否需要 DQN
-   是否需要 SAC
-   是否需要 imitation

# 第九部分：什么时候不该继续 PPO

如果出现：

-   训练百万步毫无进展
-   entropy 长期归零
-   行为固定
-   exploration 不存在
-   reward 永远局部最优

则：

停止继续堆 PPO 步数。

应该转向：

-   curriculum
-   imitation
-   hierarchy
-   planning
-   world model

# 第十部分：Imitation Learning

## BC

Behavior Cloning：

直接学 expert。

优点：

-   快

缺点：

-   distribution shift

## DAgger

核心：

agent 自己跑。

expert 修正。

这是你在 Flappy Bird 上已经验证有效的方向。

# 第十一部分：Curriculum

## curriculum 的真正作用

不是"简单到困难"。

而是：

"让梯度信号始终存在。"

## Mario Curriculum

阶段：

1.  学右移
2.  学跳跃
3.  学跨 Goomba
4.  学过坑
5.  学 timing chain

## Pokemon Curriculum

阶段：

1.  学移动
2.  学出房间
3.  学换地图
4.  学找 NPC
5.  学长期探索

# 第十二部分：World Model

真正复杂游戏：

不能只 reactive。

必须：

预测未来。

world model 的目标：

学习：

P(next_state \| state, action)

# 第十三部分：Memory

很多游戏必须记忆。

例如：

-   Minecraft
-   Pokemon
-   RTS
-   Roguelike

需要：

-   LSTM
-   GRU
-   Transformer memory
-   external memory

# 第十四部分：Self-play

对抗游戏：

必须 self-play。

否则：

agent 会过拟合固定 opponent。

# 第十五部分：真正重要的不是算法

新手总问：

"PPO 还是 SAC？"

真正决定成败的是：

1.  environment design
2.  observation
3.  reward
4.  curriculum
5.  evaluation
6.  diagnostics
7.  experiment discipline

# 第十六部分：关于 Mario

Mario 是 RL 历史经典陷阱。

因为它同时包含：

-   timing
-   momentum
-   sparse success
-   irreversible death
-   long horizon

PPO 最容易学出：

"安全但不前进。"

# 第十七部分：关于 Flappy Bird

Flappy Bird 最大问题：

环境太容易 degeneracy。

很多环境：

-   不 flap
-   一直 flap

都能拿稳定 reward。

必须先验证 physics。

# 第十八部分：关于 Pokemon

Pokemon 最大问题：

不是 optimization。

而是：

-   exploration
-   partial observability
-   long-term memory

# 第十九部分：关于 MiniGrid

MiniGrid 是极好的 RL 教学环境。

因为：

-   小
-   可控
-   快
-   有层次
-   能测 curriculum

DoorKey 特别适合研究：

-   delayed reward
-   exploration
-   memory

# 第二十部分：训练文明

你最终目标不是：

"跑出一个高 reward checkpoint。"

而是：

建立：

-   可复现系统
-   可诊断系统
-   可演化系统
-   可扩展系统
-   可治理系统

真正成熟的 RL 工程：

不是"偶尔成功"。

而是：

"失败时也知道为什么失败。"

# 最终规则

## Rule A

永远先验证环境。

## Rule B

永远保存 checkpoint。

## Rule C

永远不要只看 final model。

## Rule D

永远记录 entropy 与 KL。

## Rule E

永远不要被 narrative 欺骗。

## Rule F

永远默认 agent 会 exploit reward。

## Rule G

永远建立 baseline。

## Rule H

永远不要覆盖实验。

## Rule I

永远优先建立可复现性。

## Rule J

真正的目标不是"通关游戏"。

而是：

建立长期有效的学习系统。

## Related
<!-- openclaw:wiki:related:start -->
- No related pages yet.
<!-- openclaw:wiki:related:end -->
