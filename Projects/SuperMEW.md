# 其他小知识
## milvus
Milvus 和其他关系型数据库的区别是: 它存储的是一个多维向量 collection, 它的查询能够根据语义进行相似查询 (余弦相似度).
这个项目里面使用的是 `pymilvus` 来进行 milvus 数据库的操作.
- 连接 Milvus
```Python
# 连接（本地 / Docker）
client = MilvusClient(url="http://localhost:19530")

```




***
# 混合检索策略
简单总结: 

## 稀疏向量
- 稠密向量使用的是 `BAAI/bge-m3（langchain_huggingface）` 本地嵌入模型进行文档的向量化。
- 稀疏向量使用 BM 25 策略进行向量化。
	- 分词 tokenize：中文逐字分词，英文按单词分词。
	- 将分词结果记录到词表 （一个字典, 存储着 token 和 id 的映射关系 , 结构为 `{"token 1": id1 ,"token2": id2 ,...}`）, 持久化到 `bm 25_state. Json`
	- 使用 BM 25 公式:
```
BM25 公式直觉
   一个词在一篇文档中的重要性由三件事决定：
   
   因素: TF（词频）
   含义: 这个词在这篇文档里出现了几次
   例子: "光合作用"出现5次 → 这篇文档可能真的很相关
   ────────────────────────────────────────
   因素: IDF（逆文档频率）
   含义: 这个词在所有文档中是否稀有
   例子: "的"几乎所有文档都有 → 不重要；"量子纠缠"只有3篇文档有 → 很重要
   ────────────────────────────────────────
   因素: 文档长度归一化
   含义: 长文档天然词频高，需要惩罚
   例子: 1000字的文档出现5次 vs 100字的文档出现5次，后者更相关
   
   最终公式（简化理解）：
   
   BM25分数 = IDF × (词频 × (k1+1)) / (词频 + k1 × 长度因子)
```
Ok
- 最终把查询文本转成 `{词 ID: BM 25 权重} ` 的稀疏向量，供 Milvus 用内积(IP)做稀疏路召回

## 稠密向量

## RRF 策略进行 hybrid 查询

## Jina Rerank 精排



# LangGraph 状态机
下图展示了使用 LangGraph 编排的 RAG 功能的 pipeline。
<img src="https://cdn.jsdelivr.net/gh/fynism/Picogo@main/img/20260511213327838.png"/>

一开始先进行混合检索，尝试召回相关文本块。
  在进入 `grade_documents` 节点进行相关性评分后, 有两条路径：
  - Yes：初始检索结果相关 → 直接结束，返回结果给 LLM 生成答案
  - No：初始检索不相关 → 触发查询重写 → 扩展检索 → 返回新结果

## 相关性评分节点

就是这个 `grade_documents` 节点. 

主要使用的是独立的评分模型 `GRADE_MODEL` ,(.env 中
  `GRADE_MODEL=deepseek-v4-flash`），而不是主对话模型。原因：
  - 这是一个小而确定性的判断（yes/no），用 flash 模型更快更省
  - 避免主模型（思考模型）在评分时发散
  - `temperature=0` 保证评分结果稳定

## 问题重写
即 `rewrite_question` 节点.

这个节点做了两件事: **策略选择**和**执行扩展策略**.
首先是策略选择 :
```python
router = _get_router_model()     # 主模型 deepseek-v4-pro
prompt = (
    "请根据用户问题选择最合适的查询扩展策略，仅输出策略名。\n"
    "- step_back：包含具体名称、日期、代码等细节，需要先理解通用概念的问题。\n"
    "- hyde：模糊、概念性、需要解释或定义的问题。\n"
    "- complex：多步骤、需要分解或综合多种信息的复杂问题。\n"
    f"用户问题：{question}"
)
decision = router.invoke(prompt)
# 解析策略名: "step_back" / "hyde" / "complex"
```

使用大模型从三种策略中进行选择 : step_back , hyde 和 complex (就是前两种方法都用)

```
┌───────────┬───────────────────────────────────────────┬────────────────────────────────────┐
│   策略    │                 适用场景                  │              做了什么                │
├───────────┼───────────────────────────────────────────┼────────────────────────────────────┤
│ step_back │ 含具体人名/日期/专有名词 → 先理解通用概念   │ 生成退步问题 + 答案，拼入查询           │
├───────────┼───────────────────────────────────────────┼────────────────────────────────────┤
│ hyde      │ 模糊概念性问题                            │ 生成一段假设性文档，用该文档去检索      │
├───────────┼───────────────────────────────────────────┼────────────────────────────────────┤
│ complex   │ 需要多步骤推理                            │ 两者都做：退步问题 + HyDE             │
└───────────┴───────────────────────────────────────────┴────────────────────────────────────┘
```

然后是**执行策略扩展**:

Step-Back 的实现

  Backend/rag_utils. Py: 181-242

  核心思路来自 Step-Back Prompting 论文 (https://arxiv.org/abs/2310.06117)：

```
用户问题: "张三有没有在 2024 年新生运动会上获奖？"
     ↓ `_generate_step_back_question ()`
退步问题: "新生运动会有哪些获奖标准和类别？"
     ↓ `_answer_step_back_question ()`
退步答案: "新生运动会奖项通常包括个人项目..."
     ↓ 拼装 expanded_query
扩展查询: "张三有没有在 2024 年新生运动会上获奖？
          退步问题：新生运动会有哪些获奖标准和类别？
          退步问题答案：新生运动会奖项通常包括..."
```


  用这个更丰富的扩展查询去检索，能召回更多相关上下文。

  HyDE 的实现

  Backend/rag_utils. Py: 211-224

  HyDE（Hypothetical Document Embeddings）的思路是让 LLM 先凭空编一段答案文档，然后用这段虚构文档做检索。因为虚构文
  档和真实文档在语义空间更接近，检索效果往往比直接搜原始问题好。

  用户问题: "什么是混合检索？"
       ↓ generate_hypothetical_document ()
  假设文档: "混合检索是一种结合稠密向量检索和稀疏向量检索的信息检索方法。
           稠密向量通过深度学习模型捕获语义信息，稀疏向量如 BM 25 捕获关键词
           匹配信号。两者通过 RRF 等融合算法综合排序..."
       ↓ 用这段文本去 Milvus 检索


# 数据库相关

## PostgreSQL — 权威数据源（Source of Truth）

backend/models.py 定义了 4 张表：
![](https://cdn.jsdelivr.net/gh/fynism/Picogo@main/img/20260517200255541.png)

PostgreSQL 的角色是持久化所有需要精确查询和事务保证的数据：

- 用户认证 → users 表
- 对话历史 → chat_sessions + chat_messages（每次对话结束后全量覆写消息列表，见 agent.py:72 先删后插）
- 父级分块（用于 Auto-merging）→ parent_chunks，按 chunk_id 主键 upsert
- reasoning_content 和 rag_trace 都是 JSON/Text，直接存 PostgreSQL
---
## Redis — 热数据缓存层

backend/cache.py，TTL 默认 300 秒。

三个缓存 key 模式：
![](https://cdn.jsdelivr.net/gh/fynism/Picogo@main/img/20260517200352921.png)

读写策略是经典的 Cache-Aside：
- 读：先查 Redis → 命中直接返回 → 未命中查 PostgreSQL → 回写 Redis
- 写：先写 PostgreSQL → 同步写入/删除 Redis

Redis 是缓存层，不是持久层。即使 Redis 挂了，`get_json()` 和 `set_json()` 都做了 try/except
静默吞异常，系统会**降级**到每次都查 PostgreSQL。

---

## Milvus — 向量检索引擎

  `backend/milvus_client.py:28-105`，单 collection embeddings_collection。

  存储的每条记录代表一个 L3 叶子分块：
![](https://cdn.jsdelivr.net/gh/fynism/Picogo@main/img/20260517200757235.png)
  Milvus 只做一件事：语义/关键词混合检索。检索流程（rag_utils.py:245-301）：

  查询文本
    → dense_embedding (BAAI/bge-m3)
    → sparse_embedding (BM25)
    → Milvus.hybrid_retrieve() [RRF 融合两路结果]
    → Rerank (jina-reranker-v3, 可选)
    → Auto-merging [用 chunk_id 回查 parent_chunks 合并父块]
    → 返回最终结果


# 三级分块
首先明确分块的目的：为了在查询时精确匹配向量语义(`L3`)的同时，能够为模型提供大量的连贯上下文(` L1 `)。

## 分块
首先, 传入的文件会按照 size 从大到小来分成 `L1 , L2 , L3` 三种大小的 chunk
这个 chunk 的分割是递归性的, 如下图所示
![](https://cdn.jsdelivr.net/gh/fynism/Picogo@main/img/20260517212438493.png)

此时, 只有 L 3 级别的 (最小的) chunk 被向量化到 Milvus 中, `L2 , L3` 都存在结构化数据库中 , 这是为了避免重复的向量化, 节省 Milvus 的存储空间。

## 召回
召回也是采用递归式的召回结构。首先仅对存储在 Milvus 中的 `L3` 级别的 chunk 进行 hybrid 查询, 然后如果在某一个大块(`L2`)下命中了超过 threshold 的小块( `L3`)  , 那么就召回这个大块.

同理, `L2` 也以此方法来召回 `L1` . 最后, 把拥有最完整上下文的 `L1` 传入大模型作为上下文 , 使模型能够解决问题 .
