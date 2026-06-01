# Wiki Log

> 所有操作的时间线记录，只追加不修改。
> 格式：`## [YYYY-MM-DD] 操作 | 主题`
> 操作类型：ingest / update / query / lint / create / archive / delete

## [2026-04-29] create | Wiki 初始化
- 域：Oreki 的个人知识库（AI/ML、游戏训练、工具、编程、一切感兴趣的东西）
- 创建目录结构：raw/, entities/, concepts/, comparisons/, queries/, _archive/
- 创建 SCHEMA.md、index.md、log.md

## [2026-05-01] ingest | Agent 卖萌与执行边界
- 来源：直接粘贴（Agent 卖萌与执行边界原文）
- 创建：raw/articles/agent-persona-vs-execution-2026.md
- 创建：concepts/agent-persona-vs-execution.md

## [2026-05-01] ingest | GPT-4o 谄媚事件
- 来源：直接粘贴（GPT-4o 谄媚事件原文）
- 创建：raw/articles/gpt4o-sycophancy-event-2025.md
- 创建：concepts/gpt4o-sycophancy-event.md
- 更新：index.md（Total pages: 3 → 4）

## [2026-05-01] create | Agent 人格失控 Failure Modes 对比
- 创建：comparisons/agent-persona-failure-modes.md
- 将三篇内容串联：Helpfulness→Sycophancy 链条、reward hijack 通用模式、双重危险对比、共同对策
- 更新：index.md（Total pages: 4 → 5）

## [2026-05-04] add | GitHub HTTP/2 干扰问题归档
- 现象：curl github.com 超时（HTTP 000），wget 正常，api.github.com 正常
- 根因：HTTP/2 over TLS 在这台机器被间歇性干扰，HTTP/1.1 正常
- 解决：`git config --global http.version HTTP/1.1 && https.version HTTP/1.1`
- 归档：raw/github-http2-interference-2026-05-04.md，更新 index.md（12→13）

## [2026-05-01] update | Wiki 自检修复
- entities/hermes-agent-guide.md：补充 frontmatter（之前缺 type/tags）
- karpathy-coding-guidelines.md：移除死占位链接，改接 [[agent-persona-failure-modes]] 和 [[agent-persona-vs-execution]]
- entities/hermes-agent-guide.md 加入 index.md
- log.md 更新 Total pages: 4 → 5

## [2026-05-01] ingest | Moltbook 热门100帖归档
- 来源：Moltbook API 热度排序 Top100，走代理 http://127.0.0.1:7897
- 创建：raw/moltbook-hot-100-2026.md（中英对照，38.5万字符，100条帖子）
- 原始 JSON：/tmp/moltbook_hot_100_raw.json
- 更新：index.md 加入 moltbook-hot-100-2026 条目（Total pages: 5 → 6）
- 用途：小呦发帖话题库

## [2026-05-01] lint | Wiki 全面检修
- 发现并修复 11 个问题：
  1. index.md Total pages 6→9（实际9个页面）
  2. 修复4处 YAML sources 含 / 格式异常（加了引号）
  3. 补充3个 raw/articles/ 文件的 frontmatter
  4. SCHEMA.md / index.md / log.md 本身不需要 frontmatter（系统文件），标记为 N/A
| - 验证：重新跑检查脚本确认全部清零

## [2026-05-10] ingest | 智能体规范应用与创新发展实施意见
- 来源：微信公众号文章（https://mp.weixin.qq.com/s/_b4zG-3_NH1CV7WpyLefgw），国家网信办/发改委/工信部三部门联合印发
- 保存 raw：raw/articles/agent-policy-20260508.md
- 创建：concepts/agent-policy-2026.md — 政策全文解读与核心框架
- 创建：entities/aip-protocol.md — AIP 智能体互联协议实体页
- 创建：comparisons/ai-agent-governance-eu-us-cn.md — 全球 AI Agent 治理三方对比
- 更新：SCHEMA.md — 新增 Agent & 治理标签分类
- 更新：index.md（Total pages: 14 → 17）
## [2026-05-10] lint | 大规模 wiki 整理
- **类型**：lint（结构审计 + 批量修复）
- **变动**：
  - index.md：删2个 raw 文件死链接（github-http2-interference、moltbook-hot-100），加4个漏掉的页面
  - 补充9个页面的 YAML frontmatter
  - 移动5个文件到正确子目录（agent-drift-patterns、agent-persona-drift-academic → concepts/；godot_rl_agents → entities/；reward-hacking-patterns → concepts/；hermes-0.12.0-curator-release → concepts/；pong_v2_experiment → summaries/；rl_training_decision_framework → concepts/）
  - SCHEMA.md 新增 Agent & AI 标签组：alignment/hermes/agent/skill/memory
  - 13个页面补充出站跨引用链接（至少2条/页）
- **状态**：全部通过审计，17页17索引，零错误零断链

## [2026-05-10] ingest | 摇摇车大探营 B站账号
- **来源**：Bilibili 公开 API 批量查询 BV 号（从 MHT 管理页面提取 105 个候选，命中 103 个属于 mid=1289671344）
- **创建**：entities/yaoyao-kiddie-ride.md
- **更新**：index.md（+1 页，共 20 页）
- **状态**：103 个视频，总播放 2,163,915，总点赞 3,401

## [2026-05-10] ingest | 五眼联盟 AI Agent 安全指南（2026）
- **来源**：mp.weixin.qq.com/s/nDyTX5Pe5vNMzDkkfo-KkA（公安部第三研究所翻译）
- **创建**：entities/five-eyes-ai-agent-security-guide-2026.md — 五眼联盟 AI Agent 安全指南
- **创建**：concepts/agentic-ai-vs-generative-ai.md — 自主智能体 AI vs 生成式 AI 区别
- **创建**：comparisons/global-ai-agent-governance-comparison-2026.md — 全球 AI Agent 治理对比（中/欧/美/五眼）
- **更新**：index.md（+3 页，共 23 页）

## [2026-05-12] ingest | SOUL.md 设计方法论
- **来源**：mp.weixin.qq.com/s/YnRlmC-nxi7KxR-PggWNiw，@tonysimons_ 推文深度拆解
- **保存 raw**：raw/articles/soul-md-deep-dive-2026.md（sha256: f87921f0...）
- **创建**：concepts/soul-md-design.md — SOUL.md 六层架构完整方法论
- **关联**：hermes-agent-guide、agent-persona-vs-execution、agent-drift-patterns、reward-hacking-patterns
- **更新**：index.md（Total pages: 23 → 24）

## [2026-05-13] ingest | Hermes 三大核心能力
- **来源**：mp.weixin.qq.com/s/srsyUKuoWaVluNaIq9CBbw，花叔公众号
- **保存**：concepts/hermes-agent-skills-cron-memory-2026.md
- **内容**：Skills（SOP积累）、Cron（定时任务链）、Memory（持久记忆）三大能力实战
- **更新**：index.md（Total pages: 25 → 26）

## [2026-06-01] create | Memory 注入双通道概念页
- **类型**：create（双通道机制详细说明）
- **背景**：2026-06-01 22:28 "小呦在吗"事故复盘
- **创建**：concepts/hermes-memory-injection-channels.md
- **机制**：
  - 通道 1：MEMORY.md / USER.md frozen snapshot（session 启动 load_from_disk 冻结，同 session 写盘不刷新）
  - 通道 2：honcho volatile block（每个 turn 重新拉取，run_agent.py:6238-6245）
- **关联**：reports/memory-context-dual-channel-confusion-2026-06-01、hermes-agent-skills-cron-memory-2026
- **更新**：index.md（+1 页，26 → 27）

## [2026-06-01] create | 双通道混淆案例报告
- **类型**：create（事件案例 + 短 rule_delta）
- **创建**：reports/memory-context-dual-channel-confusion-2026-06-01.md
- **保存 raw**：raw/agent-loop-inspection/session-20260601-113141.txt（agent.log 摘录）
- **关键标记**：timeline_gap 显式标注（22:29 回复不可能引用 22:30+ 才写的内容，时间顺序违反）
- **confirmed 三件**：双通道机制存在 / 小呦混用发生过 / session 未 reload
- **rule_delta 候选**：先区分两个通道再归因；不立即写入全局 honcho 长期规则
- **保留**：`honcho_memory_boundary_rule`（Oreki 立的硬规则）不删
- **更新**：index.md（+1 页，27 → 28；新增 Reports 分区）
