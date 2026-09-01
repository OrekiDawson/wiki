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

## [2026-06-01] lint | wiki 整理 pass
- **类型**：lint（结构审计 + 局部修复）
- **变动**：
  - 修 5 个 broken wikilink（带路径前缀 → basename 形式）：hermes-memory-injection-channels ↔ memory-context-dual-channel-confusion 互引、soul-md-original-article、agent-policy-2026、moltbook-hot-100-2026
  - 修 2 个 broken wikilink（指向 wiki 外）：soul-md-design 删 runtime-kernel/agents-md-governance 改 agent-persona-vs-execution；moltbook-hot-100-raw 改成纯文本说明
  - 补 frontmatter 必填字段 6 个 page：yaoyao-kiddie-ride、hermes-agent-skills-cron-memory-2026、github-http2-interference-2026-05-04、agent-policy-20260508、soul-md-deep-dive-2026-full、soul-md-deep-dive-2026
  - red-runtime/cases/20260601_codex_forest_cheat_boundary.md 加 frontmatter + 双向 inbound link
  - main index.md 新增 Red Runtime Cases 分区，+1 page（27 → 28）
  - lint 脚本（/tmp/wiki_lint.py）新增：跳过 inline code 字面量、识别 OpenClaw reports schema
- **没做**：
  - 不 bump 6 个 stale page 的 updated/updatedAt（伪造 freshness，违反纪律）
  - 不补 OpenClaw reports/* 的 wiki-style frontmatter（plugin 自己的 schema）
  - 不拆 7 个 > 200 行 page（拆分需重复或并行主题，强行拆稀释可读性）
  - 不修 OpenClaw schema 冲突（plugin 集成层，需 Oreki 决策）
- **新报告**：reports/wiki-lint-pass-2026-06-01.md
- **状态**：lint pass 完成，0 broken wikilink，orphan 13 个（9 reports + 4 raw，按约定不进 main index）
- **更新**：index.md（+1 页 28 → 29；Reports 段 +1 案例）

## [2026-06-04] create | 小呦 52K 字符失控 response 案例
- **类型**：create（事件案例 + raw 副本归档）
- **触发**：Oreki 6/4 22:49 提到 "MiniMax Code 读取数据库发现一条超过 50000 字符的 response"（6/3 08:20）
- **发现方式**：Codex 校准未挑出，MiniMax Code 直读 db 找出
- **创建**：reports/xiaoyou-leaked-response-52k-2026-06-03.md
- **同步归档**：~/.hermes/evidence/20260604_230334_xiaoyou-leaked-52k/{raw_response.md, analysis.md}
- **raw sha256**：25c13608311829dca924617dc55ec31c372147d3c02d14bd7a1a708709d40db6
- **7 类泄漏自检**：L1 主导 / L2 高频 / L3 中量 / L4 主导 / L5=0 / L6=0 / L7 高频
- **risk_level**：中（db 污染 + session context 复用风险，platform_message_id=None 未发出）
- **关联**：xiaoyou-thinking-leak-guard（SKILL.md 例 1 原始样本）、memory-context-dual-channel-confusion-2026-06-01（同类 agent 事故）
- **未做**：不动 SOUL/AGENTS/SKILL；不删 raw；不 commit
- **更新**：index.md（+1 页，28 → 29；Reports 段 +1 案例）

## [2026-06-29] ingest | F3 猫面部费洛蒙完整研究
- 来源：5 篇核心论文 + Wikipedia/PetMD/Ceva Connect 背景
  - Vitale 2018 J Feline Med Surg（PMC11343345）综述
  - Crump 2023 Veterinary Evidence 系统综述（VE-669）
  - PLOS One 2023 Feliway Classic 抓挠 RCT（PMC10584138）
  - Endersby 2024 Frontiers Vet Sci Optimum 项圈 RCT（PMC11694449）
  - Wikipedia Cat pheromone + PetMD + Ceva Connect（VNO 路径）
- 创建 raw 源（5 个）：
  - raw/articles/f3-pheromone-vitale-2018-pmc11343345.md
  - raw/articles/f3-acute-stress-crump-2023-ve-669.md
  - raw/articles/feliway-classic-scratching-plos-2023.md
  - raw/articles/feliway-optimum-collar-endersby-2024.md
  - raw/articles/cat-pheromone-vno-pathway.md
- 创建概念页（4 个）：
  - concepts/f3-pheromone.md — 总览
  - concepts/f3-clinical-evidence.md — 证据强度总表
  - concepts/f3-product-map.md — Feliway + 国产替代
  - concepts/vomeronasal-organ-pheromone-pathway.md — VNO 大脑路径
- 创建实体页（4 个）：
  - entities/ceva-sante-animale.md — 制造商
  - entities/feliway-classic.md — F3 商业版
  - entities/feliway-friends.md — FAP 商业版
  - entities/feliway-optimum.md — FPhC 商业版
- 创建对比页（1 个）：
  - comparisons/f3-vs-fap-vs-optimum.md — 三方对比 + 决策表
- 删除错放位置：/home/oreki/.hermes/wiki/f3-pheromone-deep-research.md
- 更新：index.md（Total pages 29 → 38，加 9 个新条目）
- 关联：小桔宝（公猫、田园橘白、18 天）当前使用 Feliway Classic 24h 散，此研究给小桔宝安全感地图打底

---

## 2026-06-30 — F3 拓展入库（按场景 + 小桔宝方案 + 4 篇新 raw）

- 触发：Oreki 让"全研究"重说一次，先看 wiki 现状，发现 6/29 已建 4 概念 + 4 实体 + 1 对比。这次补 3 个方向：
  1. **补 raw 源（4 篇）** — Mills 2001 Vet Record（喷尿 RCT）/ Gunn-Moore 2004 JFMS（FIC 试点 RCT）/ Ceva 1996 专利 US5709863A（配方比例一手来源）/ Pageat & Gaultier 2003 综述
  2. **拓 comparison（1 篇）** — `comparisons/f3-by-scenario.md` 按"喷尿/抓挠/急性应激/适应/多猫/慢性焦虑"6 大场景拆开 + 决策表
  3. **补 query（1 篇，queries/ 第一页）** — `queries/f3-for-xiaojubao.md` 给小桔宝 24h 散 + 7/12 换瓶 + 海乐妙/智能厕所 F3 喷雾用法 + 7/14/30 天验收标准
- 引用纪律：所有 raw / concept / comparison / query 互链都过 wikilink；新概念 + 决策表 2+ 出站链接；`Feliway Optimum` 全表标"⚠️ 单成分效力不如专用"防止误用
- index.md：加 Comparisons 第 5 条 + 新建 Queries section 1 条；Total 38 → 44（openclaw 自动块不手改）
- 验证：6 个新页 frontmatter 完整、wikilink 至少 2 出站、不在 raw/ 下改文件
- 没做：git commit（按 skill `xiaoyou-llm-wiki-usage` 反例 4，写完不自动 commit，等 Oreki 拍板）
- 总文件改动：6 个新 wiki 页面 + 1 个 index.md 更新 + 1 个 log.md 追加

---

## 2026-06-30 — 拓展研究 6 方向入库（untracked=0 配套）

- 触发：Oreki 让"全部"，6 个方向：
  1. 猫压力神经内分泌（HPA / 皮质醇 / GABA / 5-HT / 肠脑轴）
  2. 猫音乐疗法（Teie 2015 + 慢速古典）
  3. 小桔宝成长路标（AAFP 5 阶段 + 6 月龄绝育 — Oreki 校准）
  4. 小桔宝家用品产品地图（Feliway / Petkit N60/N50 / 国产 / 处方 / 益生菌）
  5. 精油 / 香薰 / 扩香在猫面前的安全性（ASPCA 缺 UGT 硬风险）
  6. F3 vs α-酪蛋白 / L-茶氨酸 / BL999 / Calm 粮（横向对比）

- 流程（**按 Oreki 6/30 校准（先查证证据，再统一写 wiki）**：
  - 第一步：web search 抓一手资料（PMC10812130 AAFP 5 阶段 / PMC6178259 Rème 2018 L-茶氨酸 / Beata 2007 Zylkene / Beata 2016 Calm 粮 / Teie 2015 + LSU 2019 猫音乐 / ASPCA 精油清单 / Petkit 真实成分）
  - 第二步：写 6 篇 raw 覆盖 6 方向（hpa-cortisol-gaba-5ht / cat-music-therapy-teie-classical / aafp-aaha-kitten-timeline / xiaojubao-product-map / essential-oils-toxic-to-cats-aspca / alpha-casozepine-l-theanine-probiotics-calm-diet）
  - 第三步：写 4 篇 concept（cat-stress-hpa-cortisol / cat-music-therapy / cat-scent-safety / xiaojubao-product-map）
  - 第四步：写 1 篇 comparison（f3-vs-probiotics-vs-amino-acids）
  - 第五步：写 4 篇 query（xiaojubao-growth-timeline / xiaojubao-calming-extra / xiaojubao-scent-safety / xiaojubao-cat-toilet-safety）

- 关键校准：
  - 绝育：**6 月龄**（Oreki 立，**不**改 8 月龄；**不**用 AAFP 2020 政策 5 月的替代）
  - Petkit N60/N50 = **除臭剂不是 F3 类费洛蒙** + **含柿果提取物（多酚）**，缺 UGT 风险
  - 智能猫厕所**先关净味**

- 姿势记录（cute-error-repair 配套）：
  - 上一轮 5 篇未查证完整稿 → 移到 `_archive/drafts-2026-06-30/`，**不**进 wiki 主树
  - 1 篇 raw 改 placeholder → 这一轮基于查证重写
  - 整轮走 Oreki 6/30 校准的流程：**先 web search 抓硬数据 → 再写 wiki**，**不**凭印象编表格
  - commit 配套 untracked=0 一起收

- index.md：Total 44 → 56，Concepts 加 4 条，Comparisons 加 1 条，Queries 加 4 条
- 验证：14 个新页 frontmatter 完整 / wikilink 至少 2 出站 / 没在 raw/ 下改文件
- 没做：未自动 push；_archive/drafts-2026-06-30/ 4 篇未查证稿**保留**为"未查证历史稿"（**不**进 wiki 主树）
- 总文件改动：14 个新 wiki 页面 + 1 个 index.md 更新 + 1 个 log.md 追加

## [2026-09-01] ingest | 哆啦A梦系列日本声优换班
- **触发**：Oreki 问 "哆啦A梦大雄的日本声优有没有变动过"
- **来源**：
  - 中文维基百科「野比大雄」：https://zh.wikipedia.org/wiki/%E9%87%8E%E6%AF%94%E5%A4%A7%E9%9B%84
  - 日文维基百科「大山のぶ代」：https://ja.wikipedia.org/wiki/%E5%A4%A7%E5%B1%B1%E3%81%AE%E3%81%B6%E4%BB%A3
  - エンタメクロス（ticket.co.jp）历代声优一览表：https://www.ticket.co.jp/entx/entertainment/doraemon-voice
  - chinesedora.com 旧「大雄」认可水田山葵：https://chinesedra.com/news/4523.htm
  - note.com/nao46222 2005 一齐换班背景：https://note.com/nao46222/n/naf60eb151084
- **保存 raw**：raw/articles/doraemon-voice-actors-jp-2026.md
- **创建 entity**：entities/nobi-nobita.md（野比大雄）
- **创建 concept**：concepts/doraemon-voice-actors.md（系列声优换班总览）
- **更新 SCHEMA.md**：新增"动画 & 声优"标签分类（anime / voice-actor / doraemon / nobi-nobita）
- **更新 index.md**：Entities +1，Concepts +1，Total 44 → 46，Last updated 2026-06-30 → 2026-09-01
- **没做**：未自动 commit（按 xiaoyou-llm-wiki-usage §停手条件 + cute-error-repair 配套，写完不自动 commit，等 Oreki 拍板）
- **总文件改动**：1 raw + 1 entity + 1 concept + SCHEMA +1 段 + index.md + log.md
