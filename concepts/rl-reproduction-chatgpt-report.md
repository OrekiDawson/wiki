---
title: RL 复现实验报告（ChatGPT 生成）
created: 2026-05-04
updated: 2026-05-10
type: concept
tags: [rl, training, tool]
sources: []
confidence: medium
---


关联页面：[[rl-training-bible-xiaoyou-v1]]（训练圣经）、[[pong_v2_experiment]]（实验记录）
# 基于你这台机器的 RL 复现深度研究报告

## 摘要

你的配置并不是"只能跑玩具环境"的个人机，而是一台**足以做单机单卡像素 RL 复现**的中档工作站：配置清单显示你有 **AMD Ryzen 5 5600G（6 核 12 线程）**、**16 GB 内存**、**NVIDIA GeForce RTX 2060 12 GB**，驱动支持 **CUDA 12.4**，并且系统已经能正常识别独显。对现代单卡 RL 来说，这意味着你完全可以把"复现目标"从经典控制/小网格世界，提升到 **MiniGrid、Atari、Procgen、部分 ViZDoom** 这一级别；但仍然不建议把目标放到超大规模自博弈、超长时程探索或多机分布式系统上。fileciteturn0file0

基于"公开可复现、工程可行、成本可控、最好能在你机器上真的跑起来"这四个标准，我给你的结论是：**首选复现目标应当是 MiniGrid DoorKey / MultiRoom 系列的 PPO 纯 RL 通关**；它最稳、最快、最容易完整闭环，且它训练出来的"稀疏奖励探索 + 子目标依赖 + 记忆需求"能力，对后续迁移到 Super Mario Bros.[\[1\]](https://cdn.openai.com/procgen.pdf) 类平台跳跃任务非常有价值。第二阶段再做 **Procgen CoinRun**，第三阶段再做 **Atari Breakout/Pong** 或 **ViZDoom basic**。[\[2\]](https://minigrid.farama.org/environments/minigrid/DoorKeyEnv/)

如果你只想一句话的路线图：**先用 MiniGrid 跑通一整套训练/评估/可视化/断点恢复管线，再把同一套工程纪律迁移到 Procgen CoinRun，最后再做 gym-super-mario-bros 的 curriculum + imitation + action abstraction 组合实验。** 这条路径成功率最高，也最符合你的机器与目标。[\[3\]](https://github.com/Farama-Foundation/Minigrid)

## 配置结论与工程边界

从配置清单看，你的机器适合的不是"纯 CPU 慢慢磨"的路线，而是**单卡 CUDA 加速 + 中等并行环境数**的路线。RTX 2060 12 GB 足以容纳 PPO / APPO / RND 这一类卷积策略网络；6 核 12 线程则足够支撑 MiniGrid、Atari、Procgen 这类同步或中等并行环境，但不适合把并行环境数一口气拉到几十上百并再叠加大型自博弈框架。16 GB 系统内存也意味着你应该避免超大 replay buffer、超高 `num_envs_per_worker` 或同时跑多个视频记录进程。fileciteturn0file0 [\[4\]](https://cdn.openai.com/procgen.pdf)

对你来说，最实用的工程判断不是"能不能理论上训练"，而是"能不能在**单机 12--48 小时**内看到趋势，并在**1--7 天**内得到可验收结果"。在这个标准下，MiniGrid、Atari 和 Procgen 都是合格候选；ViZDoom 的基础场景可做，但高配 APPO 论文级复现实验明显更吃 CPU；Montezuma\'s Revenge 的 RND 是著名成果，但 CleanRL 文档自己就明确写了其 2000M steps 基准**约需 250 小时**，因此它更适合作为"拉高探索上限的挑战项目"，不适合作为你的第一目标。[\[5\]](https://docs.cleanrl.dev/rl-algorithms/ppo-rnd/)

还有一个很重要的现实点：很多老的 retro/mario/sonic 仓库停留在旧 Gym API、旧 Python、旧 TensorFlow 或旧 `gym-retro` 时代；而你这台机器已经具备现代 CUDA/PyTorch 单卡条件，所以**优先选现代维护中的实现**会显著提高成功率。我在本报告里把首选目标放在 `MiniGrid + PPO`、备选放在 `CleanRL + Atari/Procgen`、`Sample Factory + ViZDoom`，就是基于这个考虑。[\[6\]](https://stable-baselines3.readthedocs.io/)

## 可优先复现的五个成果

下表中的"最小硬件需求"和"训练时间"是**结合你的机器做的工程估计**，不是论文原文数字；"是否纯 RL / 是否公开代码 / 主要方法"则基于论文与官方/高质量实现。fileciteturn0file0

  ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
  成果                             原始机构 / 论文                               纯 RL    公开代码   主要方法                            估计最小硬件需求                          估计训练时间   复现难度
  -------------------------------- --------------------------------------------- -------- ---------- ----------------------------------- ----------------------------------------- -------------- ----------
  `MiniGrid DoorKey / MultiRoom`   Minigrid & Miniworld；Minigrid DoorKey 文档   是       是         PPO / A2C，稀疏奖励导航             4 核 CPU 即可；你这台机有富余             0.5--6 小时    **1/5**

  `Atari Breakout / Pong`          DQN / Rainbow / PPO on ALE                    是       是         PPO 或 Rainbow / DQN                单张 6 GB+ GPU 或快 CPU；你可直接单卡跑   6--24 小时     **2/5**

  `Procgen CoinRun / StarPilot`    Procgen Benchmark                             是       是         PPO + IMPALA-style CNN              单张 8 GB+ GPU 更舒服；你可单卡跑         8--30 小时     **2/5**

  `ViZDoom basic scenarios`        ViZDoom；Sample Factory 复现实验              是       是         APPO / PPO / RNN                    单张 GPU + 较强 CPU；你能做基础场景       12--48 小时    **3/5**

  `Montezuma's Revenge with RND`   RND 论文                                      是       是         PPO + Random Network Distillation   Linux + 单张 GPU + 长时运行               4--10 天       **5/5**
  ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

上表依据如下：MiniGrid 官方文档明确给出 DoorKey 的任务性质、奖励范围、训练教程，并且官方仓库直接指向 `rl-starter-files` 作为**已验证收敛**的训练方案；这使它成为最稳妥的首选。[\[7\]](https://minigrid.farama.org/environments/minigrid/DoorKeyEnv/)

Atari 方面，ALE 仍然是最经典的像素控制基准；DQN 和 Rainbow 是奠基结果，而 CleanRL 的 `ppo_atari.py` 给出了现代 PyTorch 单文件实现，并在 `Breakout`、`Pong` 上给出与 Baselines 对齐的结果，因此它非常适合你的机器做"高水平成果"复现。[\[8\]](https://ale.farama.org/index.html)

Procgen 的价值在于它既有游戏感，又有**泛化评估**：官方仓库和论文都强调其环境在单 CPU 核上可达"thousands of steps per second"，CleanRL 则把官方默认 200M timesteps 缩成 `25e6` 的 easy-mode 版本以节省计算，这对个人机非常友好。[\[9\]](https://openai.com/index/procgen-benchmark/)

ViZDoom 适合做"更像真实游戏"的第一人称视觉控制；ViZDoom 官方文档强调其 Gymnasium/Python API、轻量和高渲染速度，而 Sample Factory 文档提供了从 `doom_basic` 到大规模 battle 环境的脚本与参数。对你来说，**基础场景可做，论文级大规模 battle 不建议作为第一刀**。[\[10\]](https://vizdoom.farama.org/index.html)

Montezuma RND 则是"个人单卡挑战项目"。RND 原论文说它在该游戏上首次做到超过平均人类表现、并**偶尔完成第一关**；CleanRL 对应实现也可复现趋势，但文档明确写了其 2000M steps 基准的极长运行时间，因此它更适合你在前面四个项目都跑顺后再上。[\[11\]](https://arxiv.org/abs/1810.12894)

## 五个成果的逐项复现指南

### 首推基座：MiniGrid DoorKey / MultiRoom

关键资料建议直接看 、、 和 。DoorKey 官方说明里明确说它是一个"拿钥匙---开门---到终点"的稀疏奖励任务，奖励范围在 `(0,1)`，成功时奖励为 `1 - 0.9 * step_count / max_steps`；官方仓库还直接指出 `rl-starter-files` 在该环境上**已测试并已知默认超参会收敛**。[\[12\]](https://minigrid.farama.org/environments/minigrid/DoorKeyEnv/)

最稳的做法，不是自己从零写 PPO，而是先直接复用 `rl-starter-files` 的训练 / 评估 / 可视化全流程。仓库读我已经给出三条关键命令：训练 `MiniGrid-DoorKey-5x5-v0` 到 80k frames、可视化、以及 100 局评估。它的日志会同时落地到 txt、CSV、TensorBoard，并支持中断与恢复，这恰好符合你想要的"可复制脚本 + 训练日志模板 + 工程闭环"。[\[13\]](https://github.com/lcswillems/rl-starter-files)

推荐超参上，我建议**第一轮完全不改默认 PPO 超参**，只改环境难度与总帧数。因为这里最常见的错误不是"模型太弱"，而是"还没把环境、日志、断点恢复、评估协议跑顺就开始搜超参"。推荐进度是：`DoorKey-5x5 @ 80k frames` → `DoorKey-6x6 @ 150k` → `DoorKey-8x8 @ 400k` → `DoorKey-16x16 @ 1M`；如果转向 `MultiRoom`，再加 `--recurrence 4` 测记忆。前两步在你机器上通常是小时级任务。这个建议是工程安排，不是论文固定值。DoorKey 的"稀疏奖励 + 子目标依赖"特征，则来自官方环境定义。[\[14\]](https://minigrid.farama.org/environments/minigrid/DoorKeyEnv/)

常见坑主要有三类。第一，**把 reward 均值当 success rate**：DoorKey 成功与失败是二值事件，但成功奖励会被步数折扣，所以你必须同时记录"成功率"和"平均 reward"。第二，**过早上 16x16**：这会让你错把"难度问题"看成"算法失效"。第三，**有些任务必须加 memory**：像 `RedBlueDoors` 或更复杂的 MultiRoom，不加 recurrence 常常会卡住。`rl-starter-files` 读我已经专门给出了 `--recurrence` 的用法。[\[14\]](https://minigrid.farama.org/environments/minigrid/DoorKeyEnv/)

### 高性价比像素基准：Atari Breakout / Pong

Atari 方向最值得看的材料是 、、，以及 DQN / Rainbow 论文页。ALE 是标准 Atari 环境；DQN 和 Rainbow 是历史上的关键高水平结果；而 CleanRL 给出的 `ppo_atari.py` 属于现代、简洁、可读的 PyTorch 版本，读我里直接写了 `BreakoutNoFrameskip-v4` 的运行命令，并给出与 Baselines 对齐的 `Breakout` / `Pong` 结果。[\[8\]](https://ale.farama.org/index.html)

如果你追求"最快看到像素 RL 学会一个经典游戏"，我建议优先跑 Pong[\[15\]](https://openai.com/index/procgen-benchmark/)，再跑 Breakout[\[16\]](https://www.samplefactory.dev/01-get-started/basic-usage/)。理由很简单：Pong 最快验证"训练循环 + GPU + 视频回放 + evaluation"是否正常，而 Breakout 更能检验卷积特征与时序控制。CleanRL 文档里的基准命令就是 `python cleanrl/ppo_atari.py --env-id BreakoutNoFrameskip-v4`；你可以把 `env-id` 换成 `PongNoFrameskip-v4`，其余默认值先不动。[\[17\]](https://docs.cleanrl.dev/rl-algorithms/ppo/)

依赖上，按 ALE 官方安装 `ale-py`，再用 CleanRL 的 Atari requirements 即可；如果你走 Dopamine/Rainbow 路线，则接受 TensorFlow/JAX 或 gin 配置复杂度更高的代价，换来与经典论文更贴近的算法形态。由于你偏好 PyTorch、且更强调工程可行性，所以我建议：**先用 CleanRL PPO 做高水平复现，Rainbow 留作对照实验，而不是第一刀。** 这个取舍来自实现复杂度与可维护性的差异。[\[18\]](https://ale.farama.org/getting-started/)

常见坑包括：ROM/环境安装不一致、把 `episodic_return` 曲线短期波动误判为发散、视频录制拖慢训练，以及没有固定种子导致不同 run 的结论互相打架。Atari 的正确工程做法是：**每个环境至少 3 个种子，先短跑 1M steps 做 sanity check，再上正式长跑。** CleanRL 文档里的 benchmark 脚本本来就是按多 seed 设计的。[\[17\]](https://docs.cleanrl.dev/rl-algorithms/ppo/)

### 最接近 Mario/Sonic 迁移感受的项目：Procgen CoinRun / StarPilot

这里建议直接看 、、、。Procgen 的优点是：环境是"游戏样式"的、具备程序生成带来的泛化压力，而且官方明确强调它们在单 CPU 核上就有很高吞吐。CleanRL 又把其训练配置简化成了可以在个人机上跑的 easy-mode `25e6` timesteps 版本。[\[19\]](https://openai.com/index/procgen-benchmark/)

如果你更关心"平台跳跃迁移"，那就优先 `CoinRun`；如果你更关心"官方文档现成命令"，那先用 `starpilot` 跑通。CleanRL 文档里显示 `ppo_procgen.py` 的典型用法是 `python cleanrl/ppo_procgen.py --env-id starpilot`，并给出了其实现与 Baselines 的对齐结果。默认超参也非常清晰：`total_timesteps=25e6`、`learning_rate=5e-4`、`num_envs=64`、`num_steps=256`、`gamma=0.999`、`num_minibatches=8`、`update_epochs=3`，网络是 IMPALA-style CNN。[\[20\]](https://docs.cleanrl.dev/rl-algorithms/ppo/)

结合你的机器，我建议把 Procgen 当成**第二阶段主项目**。做法上保留官方默认算法结构，但先把 `num_envs` 从 64 下调到 24--32，避免 6 核 12 线程的 CPU 成为瓶颈；当你观察到 GPU 利用率低、SPS 很稳定时，再逐步升到 48 或 64。CoinRun 方向的最终评估不要只看平均 reward，还要单独记录"完成率"，因为文献里对 CoinRun 的描述就是：拿到 coin 会给 10 分并终止，其他奖励为 0，这很适合定义成功率。fileciteturn0file0 [\[21\]](https://proceedings.mlr.press/v162/langosco22a/langosco22a.pdf)

常见坑主要是环境安装和过拟合。Procgen 的真正价值在于训练/测试 level 分离带来的泛化评估；如果你只在固定 level 上反复测，得到的不是你后续最需要的"类 Mario 泛化能力"，而只是记忆特定版图。建议至少保留一个 held-out 测试 level 集合作阶段性验收。[\[22\]](https://arxiv.org/abs/1912.01588)

### 更像真实游戏控制：ViZDoom basic scenarios

、 和 是这一路线最重要的入口。ViZDoom 官方文档强调它支持 Gymnasium/Python 接口，而且轻量、快、跨平台；Sample Factory 则给出了从 `doom_basic` 到 `doom_battle` 的完整训练脚本，并在文档里明确区分了"10 核机器的合适设置"和"36 核服务器的论文复现实验设置"。这对你做资源评估非常有帮助。[\[23\]](https://vizdoom.farama.org/index.html)

对你的机器，建议不要一上来就冲 `doom_battle`。最合理的路径是：先用 Sample Factory 的 basic usage 跑 `doom_basic` 之类的基础场景，验证 `num_workers`、`num_envs_per_worker`、RNN、TensorBoard 都正常；等这一套稳定后，再考虑更复杂的导航或战斗任务。Sample Factory 文档给出的最基本命令就包括 `python -m sf_examples.vizdoom.train_vizdoom --env=doom_basic ...` 和对应 `enjoy_vizdoom`。[\[24\]](https://www.samplefactory.dev/01-get-started/basic-usage/)

这里最关键的工程建议是：**ViZDoom 更吃 CPU 调度与环境并行效率，而不只是显卡。** 你的 2060 能撑模型前向，但 6 核 12 线程不适合照抄 20--72 workers 的大配置。你应该从 4--8 workers 起步，小心测 SPS、policy lag 和 CPU 占用，而不是盲目堆并行。这个判断来自你的硬件条件与 Sample Factory 对 10 核、36 核配置的明确说明。fileciteturn0file0 [\[25\]](https://www.samplefactory.dev/09-environment-integrations/vizdoom/)

### 探索上限挑战：Montezuma's Revenge with RND

如果你想要一个"有论文含金量、也能证明你这台机子真的能做 hard-exploration"的项目，那么 RND 是最值得留到后面做的挑战。关键资料是 、。原论文把 RND 定义为把固定随机网络的预测误差当成内在奖励，证明它能在 Montezuma\'s Revenge 这种困难探索游戏上取得重大进展，并偶尔完成第一关。[\[26\]](https://arxiv.org/abs/1810.12894)

对你的机器，这个项目**能跑，但绝不应当是首选**。因为 CleanRL 的对应实现本身就写得很直白：它依赖 EnvPool，且在 Windows / macOS 上有平台限制；更重要的是，它给出的 Montezuma 2000M steps benchmark 运行时间大约是 250 小时。也就是说，它更适合你等前面项目都稳定后，拿来做"探索强化"专项，而不是用来验证工程链路。[\[27\]](https://docs.cleanrl.dev/rl-algorithms/ppo-rnd/)

如果你真的要做，建议把它拆成两个阶段：先用 `Breakout` / `Pong` 的普通 PPO 验证 Atari 栈，再切到 Montezuma 的 RND；并把目标定义成"取得稳定非零探索进展/第一关关键 room 进展"，而不是试图在个人机上直接追论文级高分。这一点非常重要。[\[28\]](https://docs.cleanrl.dev/rl-algorithms/ppo/)

## 首选目标的详细一键复现方案

### 为什么我把首选目标定为 MiniGrid DoorKey

我把首选目标定成 `MiniGrid-DoorKey-5x5-v0 → 6x6 → 8x8 → MultiRoom`，而不是更炫的 Procgen/Atari，原因只有三个：第一，它是**真正的纯 RL 通关型任务**；第二，它有**官方推荐、已验证收敛**的公开训练框架；第三，它能让你在最短时间内把"训练---评估---视频---日志---断点恢复---验收标准"整套工程打穿。对一个后续想做 Mario 的人来说，这个基座比"直接上大任务然后排错三天"更值钱。[\[12\]](https://minigrid.farama.org/environments/minigrid/DoorKeyEnv/)

### 建议的目录结构

    rl-lab/
    ├── envs/
    ├── rl-starter-files/
    ├── logs/
    ├── reports/
    └── storage/

### 一键流程

下面这组命令是我建议你真正执行的最小闭环。它们直接基于 `rl-starter-files` 读我中的训练 / 可视化 / 评估命令，只做了模型名和路径规范化。`DoorKey-5x5-v0` 的 80k frames 是仓库示例值。[\[13\]](https://github.com/lcswillems/rl-starter-files)

    conda create -n rl-minigrid python=3.10 -y
    conda activate rl-minigrid

    git clone https://github.com/lcswillems/rl-starter-files.git
    cd rl-starter-files
    pip install -r requirements.txt

    python -m scripts.train \
      --algo ppo \
      --env MiniGrid-DoorKey-5x5-v0 \
      --model dk5_ppo_seed1 \
      --save-interval 10 \
      --frames 80000

    python -m scripts.evaluate \
      --env MiniGrid-DoorKey-5x5-v0 \
      --model dk5_ppo_seed1 \
      --episodes 100

    python -m scripts.visualize \
      --env MiniGrid-DoorKey-5x5-v0 \
      --model dk5_ppo_seed1 \
      --argmax

    tensorboard --logdir storage/dk5_ppo_seed1

### 推荐超参与理由

首轮我建议你只控制四个变量：环境、总帧数、是否加 memory、随机种子。其余 PPO 超参全部沿用 `rl-starter-files` 默认值。理由不是"默认最优"，而是 **Minigrid 官方仓库已经明确写了这个训练方案经过测试、默认超参已知会收敛**；当目标是建立高可信复现起点时，这比自己随手改 learning rate 更重要。[\[29\]](https://github.com/Farama-Foundation/Minigrid)

我的具体建议是：

-   `DoorKey-5x5-v0`：`80k frames`，`seed=1/2/3`
-   `DoorKey-6x6-v0`：`150k frames`
-   `DoorKey-8x8-v0`：`400k frames`
-   `DoorKey-16x16-v0`：`1M frames`
-   `MultiRoom`：先从较小房间数开始，必要时加 `--recurrence 4`

这组值是工程化 curriculum，不是官方唯一答案。核心思路是：**先把"通关率"做成，然后再扩难度，而不是在大任务上等一个漫长、模糊的 loss 曲线。**

### 训练日志模板

`rl-starter-files` 的日志字段已经很够用，它会打印并保存更新数、总帧数、FPS、回报统计、policy loss、value loss、梯度范数等指标。你额外再维护一份更适合复现实验追踪的 CSV 即可。仓库对训练日志字段含义已有说明。[\[13\]](https://github.com/lcswillems/rl-starter-files)

建议模板：

    run_id,seed,env,total_frames,update,wall_time_sec,fps,train_return_mean,train_return_std,train_return_min,train_return_max,train_ep_len_mean,entropy,value,policy_loss,value_loss,grad_norm,eval_success_rate_100,eval_reward_mean_100,checkpoint_path,notes
    dk5_ppo_seed1,1,MiniGrid-DoorKey-5x5-v0,80000,10,512,156.2,0.64,0.22,0.00,0.92,34.1,1.12,0.48,-0.013,0.041,0.61,0.97,0.74,storage/dk5_ppo_seed1/model.pt,first stable run

### 验收标准

DoorKey 的成功奖励是正数，失败时奖励为 0，因此你应该把**是否获得正奖励**直接作为成功判定。官方环境页也明确说明了这一点。[\[30\]](https://minigrid.farama.org/environments/minigrid/DoorKeyEnv/)

我建议的现实验收线是：

-   `DoorKey-5x5-v0`：100 局评估中 **≥95% 成功率**
-   `DoorKey-6x6-v0`：**≥90%**
-   `DoorKey-8x8-v0`：**≥75%**
-   `DoorKey-16x16-v0`：**≥40%** 即可视为"工程跑通，开始进入难任务阶段"

后两条是工程验收线，不是文献标准；作用是帮助你快速判断"该升级难度"还是"该先做算法/实验设计改进"。

### 回滚与重跑策略

如果 `5x5` 在 20k--30k frames 之后仍然长期接近 0 成功率，优先检查三件事：环境是否真的在跑、模型是否落盘、评估是否用了对应 checkpoint。不要先改算法。`rl-starter-files` 原生支持保存与重启，这一点就是你首选它而不是自己乱搭脚本的重要原因。[\[13\]](https://github.com/lcswillems/rl-starter-files)

如果 `8x8` 或 MultiRoom 卡住，执行以下回滚顺序：

1.  回到上一难度，用 3 个种子确认不是偶然坏 seed。
2.  保持 PPO 默认超参不动，只增加总帧数。
3.  仍无提升，再引入 `--recurrence 4`。
4.  再不行，做 curriculum，而不是一下子上 intrinsic reward。

这个顺序的目的，是把"环境难度"、"记忆需求"、"探索不足"三个问题拆开诊断，而不是把所有改动混在一起。

## 失败时的备选目标与快速复现路径

### 备选一：Procgen StarPilot / CoinRun

这是你从 MiniGrid 向 Mario/Sonic 风格任务迁移的最佳第二站。CleanRL 文档直接提供了 `ppo_procgen.py` 的 usage，并说明它为了省算力采用了 easy mode + `25e6` timesteps。对你的机器，我建议先从 `starpilot` 验证，再切 `coinrun`。[\[31\]](https://docs.cleanrl.dev/rl-algorithms/ppo/)

快速路径：

    conda create -n rl-procgen python=3.10 -y
    conda activate rl-procgen
    git clone https://github.com/vwxyzjn/cleanrl.git
    cd cleanrl
    pip install -r requirements/requirements-procgen.txt
    python cleanrl/ppo_procgen.py --env-id starpilot --track --capture-video

如果一切正常，再把 `--env-id` 改成 `coinrun`，并把 `num_envs` 调低到 24--32 以适配你的 CPU。默认超参建议先别动。fileciteturn0file0 [\[32\]](https://raw.githubusercontent.com/vwxyzjn/cleanrl/master/cleanrl/ppo_procgen.py)

### 备选二：Atari Pong / Breakout

这是最经典、文献最成熟、教程最多的一条线。CleanRL 的 `ppo_atari.py` 文档给了直接可跑命令，并给出 `Pong` / `Breakout` 的结果区间。作为个人机高水平复现，它非常合适。[\[17\]](https://docs.cleanrl.dev/rl-algorithms/ppo/)

快速路径：

    conda create -n rl-atari python=3.10 -y
    conda activate rl-atari
    git clone https://github.com/vwxyzjn/cleanrl.git
    cd cleanrl
    pip install -r requirements/requirements-atari.txt
    python cleanrl/ppo_atari.py --env-id PongNoFrameskip-v4 --track --capture_video

### 备选三：ViZDoom doom_basic

如果你想早点摸到第一人称视觉控制的味道，就用这个。Sample Factory 的 basic usage 已经给出 `doom_basic` 的训练与 enjoy 命令；先跑小并行，再慢慢调大。[\[24\]](https://www.samplefactory.dev/01-get-started/basic-usage/)

快速路径：

    conda create -n rl-vizdoom python=3.10 -y
    conda activate rl-vizdoom
    pip install "sample-factory[vizdoom]"
    python -m sf_examples.vizdoom.train_vizdoom \
      --env=doom_basic \
      --experiment=DoomBasic \
      --train_dir=./train_dir \
      --num_workers=4 \
      --num_envs_per_worker=4 \
      --train_for_env_steps=1000000

## 针对 gym-super-mario-bros 的改进实验计划

的官方仓库给了你做 Mario 研究时最重要的几条底层事实：它默认可以使用完整 256 离散 NES 动作；也提供 `RIGHT_ONLY`、`SIMPLE_MOVEMENT`、`COMPLEX_MOVEMENT` 三组动作抽象；环境支持单关 ID 和 `RandomStages` 采样；奖励函数不是"是否过关"本身，而是**向右位移 + 时钟惩罚 + 死亡惩罚**，并且 `info` 字典里有 `flag_get`、`x_pos`、`world`、`stage` 等关键字段。也就是说，Mario 最容易掉进去的坑，是 agent 学会"吃 shaped reward"，却没有真正学会**稳定通关**。[\[33\]](https://github.com/Kautenja/gym-super-mario-bros)

下面这五类实验，是我认为最值得做、也最匹配你机器与目标的。

  ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
  方法                 实验设计                                                                  必要改动                                                        评估指标                                         预期难点                    资源估计
  -------------------- ------------------------------------------------------------------------- --------------------------------------------------------------- ------------------------------------------------ --------------------------- ----------------------------
  Curriculum           从 `1-1` 单关开始，逐步到 `1-2`、`1-3`、`1-4`，再到 `RandomStages` 子集   用 stage-specific env + `RandomStages` 逐级扩训练分布           `flag_get` 成功率、`x_pos`、通关时间、样本效率   难点转移处会掉点            1 GPU，12--48h / 阶段

  Imitation            先收集少量人类/脚本演示，做 BC 或 DAgger，再 PPO fine-tune                接入示范轨迹存储、监督 loss；推荐用 `imitation` 的 BC/DAgger    成功率、早期学习速度、与纯 PPO 的对比            演示覆盖不足、分布偏移      1 GPU，外加 1--3h 数据采集

  World-model          DreamerV3 风格 latent world model 先在 `1-1` 与小型 stage 集合试跑        替换 model-free backbone，加入重建/预测分支                     成功率、world model loss、想象 rollout 质量      代码复杂度高，VRAM 紧张     1 GPU，1--3 天小规模

  Hierarchical         高层选"技能"，低层执行 primitive actions；可从 Option-Critic 思路入手     增加 option head / termination head；或手工技能库先做半参数化   每 skill 使用频率、成功率、序列长度、泛化        option collapse、训练不稳   1 GPU，1--2 天 / 原型

  Action abstraction   先从 `RIGHT_ONLY` 或 `SIMPLE_MOVEMENT` 起步，再升到 `COMPLEX_MOVEMENT`    用 `JoypadSpace` 限缩动作，再逐步解冻动作集                     成功率、学习速度、动作熵、无效动作占比           动作过少会限制高阶技巧      成本最低，小时级
  ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

### Curriculum

Mario 仓库已经提供了单关环境 ID 模板和 `RandomStages` 机制，因此最自然的 curriculum 不是"改 reward"，而是**先改训练分布**：`1-1` 单关 → `1-1/1-2` 混合 → `1-1..1-4` → castle-only / underground-only 子集 → RandomStages 全集。这比一开始就在 32 关全集上训练更稳定，也更容易定位失败点。[\[33\]](https://github.com/Kautenja/gym-super-mario-bros)

### Imitation

如果你的目标是"尽快拿到能过旗杆的行为"，那么 imitation 是对纯 PPO 最有效的现实加速器。`imitation` 文档对 DAgger 的定义很标准：策略先 supervised 学示范，再在自己访问到的状态上不断查询专家动作并聚合数据，这正好用来解决 Mario 这种"纯 BC 一偏离就滚雪球"的问题。对你来说，最实用的版本是：**10--30 条** `1-1` **演示 → BC 预训练 → PPO 或 A2C 微调**。[\[34\]](https://imitation.readthedocs.io/en/latest/algorithms/dagger.html)

### World-model

World-model 路线最值得参考的是 。DreamerV3 的价值不在于"对 Mario 一定最优"，而在于它天然适合处理**稀疏奖励、长时依赖、像素输入**，并且对 action abstraction 与 curriculum 都很容易结合。现实建议是：不要一开始就把它用在 Mario 全部世界；先在 `1-1` 或少量关卡子集上测试 latent model 是否能稳定学到"向右推进、跳跃、避坑"的想象轨迹，再决定是否扩规模。[\[35\]](https://github.com/danijar/dreamerv3)

### Hierarchical

如果你最终想挑战"真正类平台跳跃任务"的长期结构，那么层级化几乎是绕不开的。Option-Critic 论文给出的核心思想，是在 primitive action 之上再学习 temporally extended options；用到 Mario 上，最直接的工程化设计就是让高层在少量技能之间切换，例如：`run_right`、`timed_jump`、`enemy_dodge`、`wait`。这类方法并不保证马上比 PPO 强，但它对解释性和迁移性很有帮助。[\[36\]](https://arxiv.org/abs/1609.05140)

### Action abstraction

这是 Mario 上**成本最低、收益最稳定**的改动。仓库明确说默认动作空间是 256 个 NES 动作，而 `JoypadSpace` 提供了 `RIGHT_ONLY`、`SIMPLE_MOVEMENT`、`COMPLEX_MOVEMENT` 三组动作列表。你的第一轮实验完全没必要从 256 动作起飞；最佳实践是：先用 `RIGHT_ONLY` 或 `SIMPLE_MOVEMENT` 学"向右推进 + 基本跳跃"，收敛后再切到 `COMPLEX_MOVEMENT`。这本质上是一种最轻量的 action abstraction curriculum。[\[33\]](https://github.com/Kautenja/gym-super-mario-bros)

## 可视化、流程图与立即执行命令

你至少需要四类图：**训练回报曲线**、**成功率随时间变化**、**行为视频/关键帧对比**、以及**超参敏感性热图**。对 MiniGrid，最重要的是"成功率"和"平均成功奖励"分开画；对 Mario，最重要的是把 shaped reward 与 `flag_get` 成功率分开画，否则你很容易把"向右跑得很快但没过关"的策略误当成进步。对 Procgen CoinRun，则应该并排画训练 levels 与 held-out test levels 的曲线。MiniGrid、CleanRL 和 Mario 官方资料分别给了日志字段、训练指标和 `info` 键，足够支撑这些图。[\[37\]](https://github.com/lcswillems/rl-starter-files)

### 训练流程图

    flowchart TD
        A[读取配置与创建环境] --> B[短跑 sanity check]
        B --> C[正式训练]
        C --> D[周期性保存 checkpoint]
        D --> E[100 局评估]
        E --> F{达到验收线?}
        F -- 是 --> G[导出视频与报告]
        F -- 否 --> H[回滚到上一难度或加 recurrence / curriculum]
        H --> C

### 首选目标时间线

    timeline
        title MiniGrid 首选复现时间线
        Day 1 : DoorKey-5x5 跑通
              : 完成日志、评估、视频输出
        Day 2 : DoorKey-6x6 / 8x8
              : 3 seeds 对齐结果
        Day 3 : 门钥匙更大规模
              : 引入 recurrence
        Day 4 : MultiRoom 小规模 curriculum
              : 形成可迁移 PPO 工程模板

### 建议生成的图表

-   `train_return_vs_frames.png`：横轴 `frames`，纵轴 `train_return_mean`
-   `success_rate_vs_frames.png`：横轴 `frames`，纵轴 `eval_success_rate_100`
-   `episode_length_vs_frames.png`：检查是否在"拖时间"
-   `hyperparam_heatmap_frames_recurrence.png`：横轴 `frames`，纵轴 `recurrence`，颜色为成功率
-   `mario_flagget_vs_xpos.png`：把 `flag_get` 成功率与 `mean_x_pos` 放在同一实验目录下对照

### 立即可执行的十条命令 / 脚本片段

    conda create -n rl-minigrid python=3.10 -y
    conda activate rl-minigrid
    git clone https://github.com/lcswillems/rl-starter-files.git
    cd rl-starter-files && pip install -r requirements.txt
    python -m scripts.train --algo ppo --env MiniGrid-DoorKey-5x5-v0 --model dk5_ppo_seed1 --save-interval 10 --frames 80000
    python -m scripts.evaluate --env MiniGrid-DoorKey-5x5-v0 --model dk5_ppo_seed1 --episodes 100
    python -m scripts.visualize --env MiniGrid-DoorKey-5x5-v0 --model dk5_ppo_seed1 --argmax
    tensorboard --logdir storage/dk5_ppo_seed1
    git clone https://github.com/vwxyzjn/cleanrl.git && cd cleanrl && pip install -r requirements/requirements-procgen.txt
    python cleanrl/ppo_procgen.py --env-id starpilot --track --capture_video

我没有在这次对话里实际长时间跑你的训练任务，因此上面的"训练时间"与"验收线"中，凡是我明确写成"工程估计"的部分，都应当按你的真实 SPS 和 GPU/CPU 利用率再校准一轮；但项目优先级、代码来源、环境边界和首选路线，已经足够直接落地。对你这台机器来说，**现在最值得立刻做的不是追求最难 benchmark，而是先把 MiniGrid 完整跑成一个高质量复现实验，然后把工程骨架迁移到 Procgen CoinRun 和 Mario。**

[\[1\]](https://cdn.openai.com/procgen.pdf) [\[4\]](https://cdn.openai.com/procgen.pdf) https://cdn.openai.com/procgen.pdf

<https://cdn.openai.com/procgen.pdf>

[\[2\]](https://minigrid.farama.org/environments/minigrid/DoorKeyEnv/) [\[7\]](https://minigrid.farama.org/environments/minigrid/DoorKeyEnv/) [\[12\]](https://minigrid.farama.org/environments/minigrid/DoorKeyEnv/) [\[14\]](https://minigrid.farama.org/environments/minigrid/DoorKeyEnv/) [\[30\]](https://minigrid.farama.org/environments/minigrid/DoorKeyEnv/) https://minigrid.farama.org/environments/minigrid/DoorKeyEnv/

<https://minigrid.farama.org/environments/minigrid/DoorKeyEnv/>

[\[3\]](https://github.com/Farama-Foundation/Minigrid) [\[29\]](https://github.com/Farama-Foundation/Minigrid) https://github.com/Farama-Foundation/Minigrid

<https://github.com/Farama-Foundation/Minigrid>

[\[5\]](https://docs.cleanrl.dev/rl-algorithms/ppo-rnd/) [\[27\]](https://docs.cleanrl.dev/rl-algorithms/ppo-rnd/) https://docs.cleanrl.dev/rl-algorithms/ppo-rnd/

<https://docs.cleanrl.dev/rl-algorithms/ppo-rnd/>

[\[6\]](https://stable-baselines3.readthedocs.io/) https://stable-baselines3.readthedocs.io/

<https://stable-baselines3.readthedocs.io/>

[\[8\]](https://ale.farama.org/index.html) https://ale.farama.org/index.html

<https://ale.farama.org/index.html>

[\[9\]](https://openai.com/index/procgen-benchmark/) [\[15\]](https://openai.com/index/procgen-benchmark/) [\[19\]](https://openai.com/index/procgen-benchmark/) https://openai.com/index/procgen-benchmark/

<https://openai.com/index/procgen-benchmark/>

[\[10\]](https://vizdoom.farama.org/index.html) [\[23\]](https://vizdoom.farama.org/index.html) https://vizdoom.farama.org/index.html

<https://vizdoom.farama.org/index.html>

[\[11\]](https://arxiv.org/abs/1810.12894) [\[26\]](https://arxiv.org/abs/1810.12894) https://arxiv.org/abs/1810.12894

<https://arxiv.org/abs/1810.12894>

[\[13\]](https://github.com/lcswillems/rl-starter-files) [\[37\]](https://github.com/lcswillems/rl-starter-files) https://github.com/lcswillems/rl-starter-files

<https://github.com/lcswillems/rl-starter-files>

[\[16\]](https://www.samplefactory.dev/01-get-started/basic-usage/) [\[24\]](https://www.samplefactory.dev/01-get-started/basic-usage/) https://www.samplefactory.dev/01-get-started/basic-usage/

<https://www.samplefactory.dev/01-get-started/basic-usage/>

[\[17\]](https://docs.cleanrl.dev/rl-algorithms/ppo/) [\[20\]](https://docs.cleanrl.dev/rl-algorithms/ppo/) [\[28\]](https://docs.cleanrl.dev/rl-algorithms/ppo/) [\[31\]](https://docs.cleanrl.dev/rl-algorithms/ppo/) https://docs.cleanrl.dev/rl-algorithms/ppo/

<https://docs.cleanrl.dev/rl-algorithms/ppo/>

[\[18\]](https://ale.farama.org/getting-started/) https://ale.farama.org/getting-started/

<https://ale.farama.org/getting-started/>

[\[21\]](https://proceedings.mlr.press/v162/langosco22a/langosco22a.pdf) https://proceedings.mlr.press/v162/langosco22a/langosco22a.pdf

<https://proceedings.mlr.press/v162/langosco22a/langosco22a.pdf>

[\[22\]](https://arxiv.org/abs/1912.01588) https://arxiv.org/abs/1912.01588

<https://arxiv.org/abs/1912.01588>

[\[25\]](https://www.samplefactory.dev/09-environment-integrations/vizdoom/) https://www.samplefactory.dev/09-environment-integrations/vizdoom/

<https://www.samplefactory.dev/09-environment-integrations/vizdoom/>

[\[32\]](https://raw.githubusercontent.com/vwxyzjn/cleanrl/master/cleanrl/ppo_procgen.py) https://raw.githubusercontent.com/vwxyzjn/cleanrl/master/cleanrl/ppo_procgen.py

<https://raw.githubusercontent.com/vwxyzjn/cleanrl/master/cleanrl/ppo_procgen.py>

[\[33\]](https://github.com/Kautenja/gym-super-mario-bros) https://github.com/Kautenja/gym-super-mario-bros

<https://github.com/Kautenja/gym-super-mario-bros>

[\[34\]](https://imitation.readthedocs.io/en/latest/algorithms/dagger.html) https://imitation.readthedocs.io/en/latest/algorithms/dagger.html

<https://imitation.readthedocs.io/en/latest/algorithms/dagger.html>

[\[35\]](https://github.com/danijar/dreamerv3) https://github.com/danijar/dreamerv3

<https://github.com/danijar/dreamerv3>

[\[36\]](https://arxiv.org/abs/1609.05140) https://arxiv.org/abs/1609.05140

<https://arxiv.org/abs/1609.05140>

## Related
<!-- openclaw:wiki:related:start -->
- No related pages yet.
<!-- openclaw:wiki:related:end -->
