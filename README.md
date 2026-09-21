# novel-writing-workflow

> 用「5 阶段 + 9 技能 + 7 步循环」把 AI 的长篇创作锁进结构化流程。
> 解决 AI 写小说写到几万字就开始崩人设、丢伏笔、前后矛盾的问题。

一套可直接落地的 AI 长篇小说创作工作流，包含 1 个主调度技能、7 个子技能、1 个自动化技能，
全部以可复制的 Markdown 模板形式提供（`templates/`）。

适用于 [WorkBuddy](https://www.workbuddy.cn/docs/workbuddy/Overview)、Claude Code 等支持
Agent Skills（`SKILL.md` + frontmatter）的工具，也可以纯手工照着模板用。

**[English](#english-tldr)**

---

## 一、问题到底出在哪

让 AI 写小说，前一万字基本都能看。再往后就跑偏，而且崩的方式高度固定：

| 崩法 | 具体表现 |
|---|---|
| **人物 OOC** | 前 10 章温吞内向的主角，第 30 章突然开始飙狠话，没有任何铺垫 |
| **伏笔断联** | 第 3 章埋的「神秘信件」，到结尾也没回收，作者自己都忘了 |
| **设定吃书** | 第 8 章说主角不会游泳，第 40 章他在水里救人救得很顺 |
| **物品/状态错乱** | 前面明明把照片交给警察了，后面又从口袋里掏出来 |
| **剧情自相矛盾** | 时间线对不上，人物位置对不上，说了 A 又变成 B |

**根源不是模型不够聪明，是它真的记不住。**

长篇小说动辄几十上百章，光正文就几十万字，远超任何模型的上下文窗口。
即使窗口够大，塞进去的代价也高，而且模型对上下文中间部分的注意力本身就会衰减。

所以靠「把前文全塞进去」这条路走不通。

## 二、核心思路：把记忆搬出模型

> **既然 AI 记不住，那就别让它记 —— 把「记忆」从模型上下文里搬到外部文档。**

把长篇创作需要的状态，压缩成四份可读、可维护、体积可控的文档：

| 文档 | 存什么 | 为什么这样存 |
|---|---|---|
| **人物状态表** | 每个角色的位置、身心状态、人际关系、持有物品、已知信息 | 写新章前读它，OOC 检查靠它 |
| **伏笔追踪表** | 每条伏笔的编号、埋设章节、内容、预计回收章节、状态 | 唯一的伏笔账本，杜绝断联 |
| **章节梗概** | 每章 100 字内的核心事件与转折 | 替代「读前文」，几十章也读得完 |
| **世界观设定** | 世界规则、力量体系、地点、组织 | 防止设定吃书 |

**每章创作前把这四份读进来，写完再更新回去。** 这就是整套工作流的地基。

代价是每章要多花一点 token 做状态维护，收益是写到第 100 章时你还能对上第 3 章的伏笔。

## 三、框架：5 阶段 / 9 技能 / 7 步循环

### 5 个阶段

```
① 工作空间初始化    →  建目录结构
② 技能体系搭建      →  配置 9 个技能文件
③ 前期创作准备      →  世界观 / 角色 / 大纲 / 前三章
④ 单章标准化创作    →  7 步循环，逐章推进
⑤ 审查与修改        →  每 5 章做一次整体审查
```

### 9 个技能

**主技能**（`00-orchestrator.md`）只做调度，不直接生成内容：

- **平台小说章节全流程创作** —— 按顺序调用下面 7 个子技能

**7 个子技能**，每个只做一件事，输入输出边界清晰：

| # | 技能 | 职责 |
|---|---|---|
| 1 | 资料整理与前文梳理 | 读四份状态文档，产出「前文状态报告」 |
| 2 | 单章剧情推演 | 规划本章 3–5 个核心事件、冲突、章末钩子 |
| 3 | 平台标准正文生成 | 按平台风格生成约 2000 字，内置去 AI 味处理 |
| 4 | 合规与一致性审查 | 检查 OOC、剧情矛盾、平台规则；**只出报告不改稿** |
| 5 | 内容优化打磨 | 按审查报告针对性修改，强化画面感与钩子 |
| 6 | 故事线与人物线更新 | 回写四份状态文档 |
| 7 | 自动版本备份 | 每章存档，保留完整版本历史 |

**1 个自动化技能**（`08-daily-backup-and-compliance-scan.md`）：

- **每日存稿备份与合规扫描** —— 独立于主流程的兜底：增量备份 + 违禁词/敏感内容扫描 + 风险分级预警

### 7 步循环

每一章都走同一条流水线，**顺序不能乱**：

```
① 资料整理  →  ② 剧情推演  →  ③ 正文生成  →  ④ 合规审查
                                                    ↓
⑦ 自动备份  ←  ⑥ 故事线更新  ←  ⑤ 内容打磨（如需）
```

关键设计是 **④ 审查与 ⑤ 打磨分离**：审查技能被明确禁止改稿，只输出问题清单；
修改交给打磨技能或人来做。审查者不能同时当修改者，否则问题会被「顺手改掉」而没人知道。

## 四、目录结构

工作流会在你的小说项目里建出这套结构：

```
小说项目/
├── 大纲细纲/
│   ├── 全书大纲.md
│   ├── 章节细纲.md
│   └── 章节梗概.md          ← 每章追加，替代「读前文」
├── 设定文档/
│   ├── 世界观设定.md
│   ├── 人物设定.md
│   ├── 人物状态表.md        ← 每章更新
│   └── 伏笔追踪表.md        ← 每章更新
├── 正文存稿/
│   └── 第001章.md ...
├── 技能配置/                ← 本仓库 templates/ 复制到这里
└── 版本备份/
    ├── 第005章/
    │   ├── 第005章_v250622-1430.txt
    │   └── metadata.json
    └── backup_index.md
```

## 五、快速开始

### 方式 A：作为 Agent Skill 安装（推荐）

```bash
git clone https://github.com/FBX-Dev/novel-writing-workflow.git \
  ~/.workbuddy/skills/novel-writing-workflow
```

装好后直接对 Agent 说：

```
请按照"小说创作工作流"技能，帮我搭建小说创作环境。
小说类型：都市悬疑
主角设定：高中生，意外获得能看见"光影"的能力
预计篇幅：长篇连载
不能碰的红线：不要虐主，主角人设不能崩
```

Agent 会自动建目录、生成 9 个技能文件、配置好 7 步循环。

### 方式 B：手工使用

把 `templates/` 里 9 个文件复制到你的小说项目的 `技能配置/` 目录，
然后按 `QUICKSTART.md` 的步骤走。模板是纯 Markdown，
用任何 LLM 的对话窗口手工走一遍流程都行 —— 只是没法自动调用。

### 方式 C：已有草稿的项目接入

```
我的小说项目在 [路径]，已有大纲和正文 12 章。
请帮我接入"小说创作工作流"，补齐人物状态表、伏笔追踪表、章节梗概。
```

Agent 会倒推前 12 章的状态，生成缺失的追踪文档。

### 日常创作

```
创作第 6 章
要求：回收第 3 章埋下的"神秘信件"伏笔
```

批量创作时，建议每章开始前确认方向，别一口气跑十章 ——
不然跑偏了要一起返工：

```
连续创作第 6-10 章
每章创作前都与我确认方向
```

## 六、模板清单

`templates/` 目录下 9 个文件，文件名用英文是为了跨平台安全，内容全中文：

| 文件 | 对应技能 |
|---|---|
| `00-orchestrator.md` | 主技能 · 平台小说章节全流程创作 |
| `01-source-and-context-review.md` | 子技能 1 · 资料整理与前文梳理 |
| `02-chapter-plot-planning.md` | 子技能 2 · 单章剧情推演 |
| `03-prose-generation.md` | 子技能 3 · 平台标准正文生成 |
| `04-compliance-and-consistency-review.md` | 子技能 4 · 合规与一致性审查 |
| `05-revision-and-polish.md` | 子技能 5 · 内容优化打磨 |
| `06-storyline-and-character-update.md` | 子技能 6 · 故事线与人物线更新 |
| `07-versioned-backup.md` | 子技能 7 · 自动版本备份 |
| `08-daily-backup-and-compliance-scan.md` | 自动化 · 每日存稿备份与合规扫描 |

每个模板都包含：技能概述 / 适用场景 / 输入格式 / 输出格式 / 执行步骤 / 输出示例 /
注意事项 / 调用示例。直接照着改就行。

## 七、几个实操要点

**去 AI 味**。模板里给了明确清单：避免「然而/因此/由此可见」这类书面连接词、
避免对仗工整的排比、避免过于完美的逻辑推导。反向要求是：口语化、允许句子不完整、
对话符合人物性格、保留不完美的细节。**注意别做过头** —— 为了降 AI 检测率
把数字全写成中文大写这种「刻意反套路」，人工一眼就能看出来。

**审查一定要做，且不要跳过**。这是整套流程里最容易被省掉、但最值钱的一步。
发现 OOC 就记进「错误库」，同一个角色的同类错误会越来越少。

**推理审查建议换更强的模型**。生成用快的，审查用慢的 —— 审查要读的东西多、
要做的判断复杂，值得多花点时间。

**伏笔数量要克制**。一册书同时挂着的未回收伏笔建议控制在 5–8 条，
多了读者记不住，你自己也容易漏。

**每 5 章做一次整体审查**。单章审查只能发现局部问题，
节奏拖沓、主线跑偏这类毛病只有拉长了看才看得出来。

## 八、已知限制

- **状态文档要人盯着维护**。Agent 更新了状态表，不复核就可能把错的写进去，
  后面的章节会把这个错一路放大。
- **不是全自动写手**。这套东西的价值在于把流程固定下来，
  创意方向、关键转折、人设底线仍然得人来定。
- **平台合规规则会变**。`自动化技能` 里的违禁词库需要你自己维护，
  仓库里不提供词库（各平台规则不同且更新频繁）。
- **适合长篇，不适合短篇**。几万字的短篇直接写更快，维护状态的成本划不来。

## 九、设计来源

本工作流的框架参考了 WorkBuddy 社区公开的 AI 小说创作教程，
并结合实际长篇项目的使用反馈做了细化（尤其是「审查与打磨分离」「状态文档四件套」
「伏笔编号制」这几处）。

---

## English TL;DR

**novel-writing-workflow** is a structured workflow for writing full-length novels with LLM agents.

The problem: AI writes fine for the first ~10k words, then characters go out of character,
foreshadowing gets dropped, and continuity breaks. The root cause isn’t model capability —
it’s that the model **cannot remember** a 100-chapter manuscript.

The fix: move memory **out of the context window and into documents**.
Four state files — character status, foreshadowing ledger, chapter summaries, worldbuilding —
are read before each chapter and written back after. That’s the foundation.

On top of it: **5 phases, 9 skills, a 7-step per-chapter loop.**
The key design choice is that the review skill is forbidden from editing —
it only reports problems; a separate polish skill (or a human) applies fixes.

Nine copy-paste-ready Markdown templates live in `templates/`.
Works with any agent supporting `SKILL.md`, or purely by hand.

Content is in Chinese (target audience: Chinese web-fiction authors).

## License

[MIT](LICENSE)
