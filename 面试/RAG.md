1. *这个项目的流式聊天里，为什么不能让 `search_knowledge_base` 工具直接向前端推送 RAG 步骤？项目是如何解决“同步工具在线程池中执行，但 SSE 输出在 asyncio 事件循环中”的跨线程通信问题的？*
- 参考答案: `search_knowledge_base` 作为同步工具可能在线程池中执行，不能直接操作主事件循环里的 SSE 输出。项目在流式函数里创建统一 `asyncio.Queue`，调用 `set_rag_step_queue()` 捕获当前 running loop；工具线程里通过 `emit_rag_step()` 使用 `loop.call_soon_threadsafe(queue.put_nowait, step)` 把 RAG 步骤安全投递回主循环。

2. *项目的 RAG 检索链路不是一次简单的向量搜索，而是一个 LangGraph 流程。请你说明： `retrieve_initial → grade_documents → rewrite_question → retrieve_expanded` 这几个阶段各自负责什么？以及在什么情况下会进入问题重写和扩展检索？*
- 参考答案 : `retrieve_initial` 做初始混合检索：dense + BM25 + RRF + rerank + auto-merge。`grade_documents` 用 grader 模型判断召回结果是否相关。若相关则结束；若不相关，则进入 `rewrite_question`，选择 Step-Back、HyDE 或 complex 策略生成扩展查询/假设文档。`retrieve_expanded` 用扩展后的内容再次检索，并和初始结果合并去重。

3. *这个项目做了三级分块，并且采用“只把 L3 叶子块写入 Milvus，L1/L2 父块写入 PostgreSQL”的设计。*
	*请你解释：*
	*为什么不直接把完整大块都写进 Milvus 检索？这种 `L3 检索 → L2/L1 Auto-merging` 的设计，分别解决了检索准确性和回答上下文完整性上的什么问题？*
 - 参考答案 : 大块直接进向量库会降低语义匹配精度，因为一个大块里可能混有多个主题；小块 L 3 更容易精确命中用户问题。但 L 3 可能上下文不足，所以命中多个兄弟 L 3 后，Auto-merging 可以合并回 L 2/L 1，让回答拿到更完整的语义上下文。

4. *项目里 BM 25 稀疏向量不是由 Milvus 自动维护，而是在 `EmbeddingService` 里自己维护词表、文档频次、总文档数等状态，并持久化到 `data/bm25_state.json`。*
	
	*请你解释：*
	
	*为什么上传覆盖文档或删除文档时，必须先分页拉取旧 chunk 文本并执行 `increment_remove`，再删除 Milvus 数据？如果顺序反过来，系统可能出现什么问题？*

- 参考答案 : BM 25 状态依赖旧 chunk 文本来扣减 `doc_freq`、`total_docs`、平均长度等统计。如果先删 Milvus，旧 chunk 文本就无法分页拉取，BM 25 状态会残留旧文档统计，导致稀疏向量分数失真，后续混合检索结果变差。

5. *这个项目的混合检索使用了稠密向量和 BM 25 稀疏向量，并用 RRF 做融合。*
	*请你解释：*
	*稠密向量检索和 BM 25 稀疏检索各自擅长什么场景？为什么需要 RRF 融合，而不是简单把两个分数直接相加？*

- 参考答案 : 稠密向量擅长语义相似，比如同义改写、概念相关；BM 25 擅长关键词、专有名词、代码符号、精确短语。两者分数尺度不同，不能直接相加。RRF 按排名融合，避免某一路分数范围压倒另一路，更稳健。
   
6. *项目里 `embedding_service` 是模块级全局单例，`api.py` 写入文档和 `rag_utils.py` 检索文档都使用同一个实例。*
	*请你解释：*
	*为什么这里要强调“写入与检索共用同一份 BM 25 状态”？如果上传文档和检索文档分别 new 出两个 `EmbeddingService` 实例，可能会导致什么问题？*
- 参考答案 : BM 25 稀疏向量的维度来自词表下标，分数来自同一套文档频次统计。写入和检索如果用不同实例或不同状态文件，query 生成的稀疏维度可能和 Milvus 里存的维度语义不一致，BM 25 检索会失效或严重偏移。

7. *这个项目对 LangChain 做了 monkey patch，要求在创建 agent 之前调用 `apply_patches()`。*
	*请你解释：*
	*这个 patch 主要是为了解决 DeepSeek thinking 模型的什么兼容性问题？如果没有在 agent 创建前应用 patch，多轮对话或 tool-call 循环中可能会出现什么现象？*
- 参考答案:DeepSeek thinking 模型会返回 `reasoning_content`。LangChain/OpenAI 兼容层可能在解析响应或序列化历史消息时丢掉这个字段，影响多轮对话和 tool-call 循环。Patch 的作用是保留并正确处理 `reasoning_content`，所以必须在创建 agent 前应用。

7. *前端收到 SSE 事件时，有一个“Thinking State Machine”：先创建 `isThinking: true` 的气泡，收到 `rag_step` 时更新步骤，收到第一个 `content` 时切换为正文渲染。*
	*请你解释：*
	*为什么这个状态机要把 RAG 步骤和最终回答放在同一个消息气泡里？这样相比“先显示一个检索日志，再单独创建回答消息”，在用户体验和状态管理上有什么好处？*

- 参考答案:同一个气泡可以让“检索中 → 生成中 → 正文输出”成为一个连续状态，用户不会看到割裂的日志消息和回答消息。状态管理上也更简单：同一个 assistant message 持有 `isThinking`、`ragSteps`、`content`、错误/中止状态，避免多条消息之间同步。
7. 
*项目里上传同名文档时，会执行覆盖流程：先同步移除旧文档的 BM 25 统计，再删除 Milvus 旧向量和 ParentChunkStore 缓存，最后重新分块、写父块、写叶子向量。*

*请你解释：*

*为什么 ParentChunkStore 需要 PostgreSQL + Redis 两层？在覆盖或删除文档时，如果只删 Milvus 向量、不清理父块存储或缓存，可能会导致什么问题？*

- 参考答案:PostgreSQL 提供父块持久化，Redis 提供热点父块缓存。Auto-merging 时根据 L 3 的 parent/root id 读取 L 2/L 1 父块。如果覆盖或删除文档时只删 Milvus，不清父块和缓存，后续可能读到旧父块，出现上下文脏读、旧内容混入回答、缓存与真实数据不一致。
10. 
*这个项目的 `StreamingResponse` 里专门处理了 `GeneratorExit`，在客户端中断请求时会显式取消后台的 agent task。*
	*请你解释：*
	*如果用户点击前端的停止按钮，前端会发生什么？后端为什么不能只依赖连接断开来自然结束，而要显式 `agent_task.cancel()`？如果不这么做，可能造成哪些资源或行为问题？*

- 参考答案:前端停止按钮会触发 `AbortController.abort()`，fetch 抛出 `AbortError`，气泡显示“已终止回答”。后端如果只依赖连接断开，后台 agent task 可能仍在跑，继续调用 LLM、工具和检索，浪费 token 与资源，还可能继续向队列写入无人消费的数据。显式 `agent_task.cancel()` 可以确定性回收后台任务。

