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

使用大模型从三种策略中