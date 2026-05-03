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
- 验证：重新跑检查脚本确认全部清零
