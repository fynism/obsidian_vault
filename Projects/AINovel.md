我先看了一下你当前的工程结构 —— `app/src/agent/graph.py` 还是一个 LangGraph 的空模板，`pyproject.toml` 只装了 `langgraph` 和 `python-dotenv`。所以下面我给的答案是按照"从零开始搭 Service 层 Demo"来组织的，尽量落到可以直接动手的颗粒度。

---

## 问题 1：流程优化建议

你现在的 4 + 1（Supervisor）设计思路是对的，但在中长篇（几十万字以上）场景下，这几个 Agent 的职责划分还略**粗**，且缺几个关键环节。我给你拆成"**必要的增补**"和"**建议的拆分**"两类。

### 1.1 必要增补的 Agent / 节点

| 新增                                                | 作用                                                        | 理由                                                     |
| ------------------------------------------------- | --------------------------------------------------------- | ------------------------------------------------------ |
| **用户意图 / 立项 Agent (Concept Agent)**               | 把用户一句话需求（"写一本赛博朋克末日爱情") 展开成类型、基调、目标字数、主角配置、核心冲突等结构化 brief | 规划 Agent 直接吃"一句话"，稳定性极差，最好有一层结构化转译                     |
| **世界观构建 Agent (WorldBuilder)** ≠ 你说的"世界观保证 Agent" | 一次性生成/扩写世界圣经（Bible），产物写入向量库                               | 你的"世界观保证 Agent"本质上是**读侧**（校验 + 召回），你还缺**写侧**（产出 Bible） |
| **Memory / State Updater Agent**                  | 章节定稿后，把新出现的人物、地点、伏笔、状态变化增量写回向量库和结构化 DB                    | RAG 不是只读的，中长篇的一致性核心就在这一步，否则写到 30 章人设必崩                 |
| **连续性检查 Agent (Consistency Checker)**             | 比"润色"更前置：专门校验人设/时间线/地理/伏笔是否与 Bible 冲突                     | 把"审核&润色"里语言层和事实层分开，事实错用润色 Agent 做很吃力                   |
| **伏笔/支线追踪器 (可做成工具而不是 Agent)**                     | 一个结构化"伏笔寄存表"，记录 opened_at / must_close_by / status        | 长篇最容易的坑就是埋了没回收                                         |

### 1.2 对你原有 Agent 的调整建议

- **"世界观保证 Agent"** 建议改名为 **Lore Retriever / Lore Guardian**，职责更纯粹：
  - *Retriever 模式*：在写作前注入相关设定（上下文打包）
  - *Guardian 模式*：在写作后校验是否违反设定
  - 避免一个 Agent 同时承担 RAG 检索 + 审校 + 知识更新三种职责，调试时定位问题很麻烦。

- **"长篇规划 Agent"** 不要只做一次，应该做成**层级化 + 可迭代**的：
  - Level 0: 整书大纲（Act / Arc）
  - Level 1: 卷 / 幕大纲
  - Level 2: 章节 roadmap（每章目标、POV、出场人物、推进的伏笔）
  - Level 3: 场景 beat（可选，对复杂章节才展开）
  
  关键是 Level 2/3 **按需动态生成**，不要一口气规划 200 章，会严重漂移。建议用**滑动窗口**：当前章写完 → 回顾 → 规划后面 3~5 章。

- **"写作 Agent"** 单章一次性输出质量通常不够，建议内部再拆：
  - `scene_planner` → 生成本章 beats
  - `scene_writer` → 按 beat 写（可并行写多个场景，然后拼接）
  - `chapter_stitcher` → 衔接、过渡润色
  
  这是一个天然的**子图**。

- **"审核&润色 Agent"** 拆成 **两道关卡**（非常重要）：
  - *Critic*：事实/逻辑/人设/节奏的批评者，输出结构化问题列表
  - *Editor / Polisher*：只做文风、节奏、句式优化
  
  让这两者之间有**循环边（conditional edge）**：Critic 给出 issues → 回到 writer 改 → 再 Critic，直到通过或达到 max_revisions。单一个"审核&润色"Agent 做这两件事，prompt 会非常臃肿。

### 1.3 流程层面的建议

- 加一个**人类介入 (HIL) 节点** 。LangGraph 的 `interrupt()` 机制非常适合在"大纲确认"、"章节定稿"这两个关键点卡一下，Demo 阶段也可以先设成自动通过，后续前端接入就直接接 HIL。
- 加一个**字数/token 预算控制器**，很容易被忽视，但中长篇会直接决定成本是否可控。
- 章节缓存要**区分 draft / reviewed / final 三态**，而不是只有一个 cache。

---

## 问题 2：数据库 / 缓存系统设计

Demo 阶段原则：**三套独立存储 + 清晰的职责边界**，避免把所有东西塞向量库，也别把所有东西塞关系库。

### 2.1 存储分层

```
┌─────────────────────────────────────────────────────────┐
│ 1. 结构化存储 (Postgres / SQLite)   ← 事实 & 关系         │
│ 2. 向量存储 (Chroma / Qdrant / PGVector)  ← 语义召回      │
│ 3. KV / 缓存 (Redis / 本地文件)       ← 运行时 & 草稿     │
│ 4. 对象存储 (本地 FS / S3)            ← 最终章节 Markdown │
└─────────────────────────────────────────────────────────┘
```

Demo 阶段我推荐的最小组合：**SQLite + Chroma（或 PGVector）+ 本地文件系统**，无需上 Redis，LangGraph 自己的 Checkpointer 就够用了。等前端接入再换 Postgres + Redis。

### 2.2 结构化库（SQLite / Postgres）表设计（建议草案）

- `novels(id, title, genre, tone, target_length, status, created_at)`
- `bible_entries(id, novel_id, type[character|location|faction|item|rule], name, payload_json, version)` — 单一事实源
- `character_states(id, novel_id, character_id, chapter_id, state_json)` — 随章节推进的动态状态（血量、关系、持有物）
- `outlines(id, novel_id, level[book|arc|chapter|scene], parent_id, title, summary, beats_json, order)`
- `chapters(id, novel_id, outline_id, number, title, status[planned|drafting|reviewing|final], word_count, file_path)`
- `foreshadowings(id, novel_id, description, opened_at_chapter, planned_close_chapter, actual_close_chapter, status[open|hinted|closed|abandoned])`
- `revisions(id, chapter_id, round, critic_issues_json, diff, created_at)` — 版本/批改历史
- `events_timeline(id, novel_id, chapter_id, in_world_time, location_id, participants, summary)` — 时间线校验用

**关键设计点**：所有"世界观事实"只有 `bible_entries` 一份，向量库只是它的**语义索引**，不重复存储事实本身。

### 2.3 向量库设计

建议**按 collection / namespace 分开**（不是全扔一个库里），检索时更精准：

| Collection | 文档粒度 | 用途 |
|---|---|---|
| `lore_characters` | 单个人物条目 | 人设召回 |
| `lore_world` | 规则 / 地点 / 势力 / 物件 | 设定召回 |
| `chapter_summaries` | 每章的"事实摘要"（不是原文）| 长程剧情记忆 |
| `scene_snippets` | 场景级原文切片 | 文风一致性 / 引用 |
| `foreshadowings` | 伏笔描述 | 写作时提醒"该回收了" |

**Metadata 一定要带全**：`novel_id`, `chapter_range`, `type`, `entity_id`, `version`，这样检索时可以 filter（例如"只检索第 1~20 章出现过的东西"）。

### 2.4 缓存 / 运行时状态

- **LangGraph Checkpointer**：Demo 阶段用 `SqliteSaver`，一行配置搞定，负责 graph 的断点续跑。
- **章节草稿缓存**：不要直接覆盖，用 `chapters.file_path` 指向一个版本化的文件（`ch_0012_draft_v3.md`）。
- **上下文打包缓存**：同一章内 writer 多次迭代，RAG 召回结果应该 memoize，避免重复检索。

### 2.5 "RAG 保一致性"的关键 trick

中长篇最致命的问题是：**随便召回 top-k 会漏关键事实**。建议：

1. 写作前做**两级召回**：先按章节大纲命中的"实体 id"去 SQL 精确拉卡片，再用向量库做语义补召回。
2. **定期蒸馏**：每 5~10 章让一个后台 agent 重新生成"人物当前状态摘要"并写回，防止向量库越塞越散。
3. **chunk 策略**：Bible 类条目按"语义单元"切（一个人物一条），不要按固定字数切。

---

## 问题 3：Graph 架构设计（多子图）

我推荐**一个主 Graph（Supervisor）+ 四个子 Graph** 的结构。子图用 LangGraph 的 `subgraph` 机制，可以独立测试、独立持久化。

### 3.1 顶层 Supervisor Graph

```
START
  │
  ▼
[intake]  ← 用户 brief / 恢复已有 novel
  │
  ▼
[route]  ── 条件路由 ──┐
  │                    │
  ├─► subgraph: WorldBuildingGraph   （首次 or 补建设定）
  ├─► subgraph: PlanningGraph        （生成/更新 outline）
  ├─► subgraph: ChapterWritingGraph  （写一章）
  ├─► subgraph: MemoryUpdateGraph    （章节定稿后落库）
  │
  ▼
[progress_check]  ── 未完 ──► 回到 route
  │
  ▼
END
```

Supervisor 不自己做创作，只做**状态判断 + 调度**。State 里维护 `current_phase`, `current_chapter`, `pending_revisions`。

### 3.2 子图 A：WorldBuildingGraph（一次性 / 补建）

```
START → [concept_expander] → [bible_drafter] → [bible_critic]
                                      ▲           │
                                      └─── loop ──┘ (≤ N 轮)
                              → [bible_indexer] → END
```

- `bible_drafter` 产出人物/地点/规则/势力等结构化条目
- `bible_indexer` 写入 SQL + 向量库

### 3.3 子图 B：PlanningGraph（层级大纲）

```
START → [level_detector]
   ├─► [book_outline]   （只在首次）
   ├─► [arc_outline]    （按需）
   └─► [chapter_roadmap]（滑动窗口，规划未来 3~5 章）
                → [foreshadow_planner]
                → [plan_critic] ──loop──┐
                       │                │
                       └─► [persist_plan] → END
```

- 关键：**不要一次规划到底**，每次只扩展到下一层级。

### 3.4 子图 C：ChapterWritingGraph（核心循环）

这是你花最多时间调的地方，我画详细点：

```
START
  │
  ▼
[context_packer]               ← RAG 两级召回 + 上一章摘要 + 本章 beats
  │
  ▼
[scene_planner]                ← 章节 → beat 列表
  │
  ▼
[scene_writer]                 ← 可并行/串行写多个 beat
  │
  ▼
[chapter_stitcher]             ← 拼接 + 过渡
  │
  ▼
[consistency_checker]          ← 用 Bible + timeline 校验事实层
  │
  ▼
[critic]                       ← 节奏/人设/对话批评
  │
  ▼  (conditional)
  ├── has_issues & rounds < max ──► [reviser] ──► 回 consistency_checker
  └── clean ──► [polisher] ──► [hil_gate (optional)] → END
```

**设计要点**：
- `context_packer` 是整个系统的 RAG 核心，单独做成节点方便替换检索策略。
- `consistency_checker` 和 `critic` 分两个节点，前者输出**结构化错误列表**（带引用的 Bible 条目 id），后者输出**文学层反馈**。
- 用 `conditional_edges` 做修改循环，state 里必须有 `revision_round` 防止死循环。

### 3.5 子图 D：MemoryUpdateGraph（定稿后）

```
START → [chapter_summarizer]
      → [entity_extractor]         ← 新角色/地点/物件
      → [state_diff]                ← 人物状态变化
      → [foreshadow_tracker]        ← opened/closed 标记
      → [bible_patcher]             ← 产生 Bible 补丁（需 critic 审一次）
      → [indexer]                   ← 写 SQL + 向量库
      → END
```

这个子图**千万不能省**，它是"写得越久越一致"而不是"写得越久越崩"的唯一保障。

### 3.6 State Schema 建议

主图的 State 尽量扁平，避免嵌套过深（LangGraph reducer 更好写）：

```python
class NovelState(TypedDict):
    novel_id: str
    phase: Literal["init", "worldbuild", "planning", "writing", "done"]
    current_chapter: int
    target_chapter: int
    last_error: str | None
    revision_round: int
    pending_issues: list[Issue]          # 结构化问题，带 reducer
    draft: str | None                    # 当前章草稿
    context_bundle: ContextBundle | None # RAG 召回结果
```

子图用**自己的 state schema**，主图只传必要的 `novel_id` + `current_chapter`，结果通过数据库/文件落地，而不是靠 state 传大字符串 —— 这是性能和持久化的关键。

### 3.7 Demo 阶段可以先砍掉的部分

为了快速跑通 Demo，建议**先实现这个最小闭环**：

1. Supervisor（极简，只按 phase 线性推进）
2. WorldBuildingGraph（可以先只让用户手工给一份 Bible json，跳过生成）
3. PlanningGraph（只做章节级 roadmap，省掉 book/arc）
4. ChapterWritingGraph（但要有 `context_packer` + `critic/polisher` 循环）
5. MemoryUpdateGraph（最小：只做 chapter_summarizer + indexer）

跑通 5~10 章后，再逐个补：伏笔追踪、HIL、多场景并行、向量蒸馏。

---

## 一个小提醒

你现在 `pyproject.toml` 里只有 `langgraph`，后面会至少需要：`langchain-core`, `langchain-openai`（或其它模型提供方）, `chromadb` 或 `qdrant-client`, `sqlalchemy`, `pydantic`, `tiktoken`。建议一开始就把"模型调用"和"检索"抽成 `services/` 下的接口类（`LLMService`, `RetrievalService`, `MemoryService`），Agent 只依赖接口，这样后面接 FastAPI 时可以直接复用 service 层，不用重写。

如果你想，下一步我可以帮你把**目录结构 / 每个 Agent 的 prompt 骨架 / 最小可运行 Demo 的代码蓝图**具体列出来，你告诉我想先深入哪一块。
