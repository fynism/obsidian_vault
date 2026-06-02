- Q : *这个项目的流式聊天里，为什么不能让 `search_knowledge_base` 工具直接向前端推送 RAG 步骤？项目是如何解决“同步工具在线程池中执行，但 SSE 输出在 asyncio 事件循环中”的跨线程通信问题的？*

- 参考答案: `search_knowledge_base` 作为同步工具可能在线程池中执行，不能直接操作主事件循环里的 SSE 输出。项目在流式函数里创建统一 `asyncio.Queue`，调用 `set_rag_step_queue()` 捕获当前 running loop；工具线程里通过 `emit_rag_step()` 使用 `loop.call_soon_threadsafe(queue.put_nowait, step)` 把 RAG 步骤安全投递回主循环。

- Q : *项目的 RAG 检索链路不是一次简单的向量搜索，而是一个 LangGraph 流程。请你说明： `retrieve_initial → grade_documents → rewrite_question → retrieve_expanded` 这几个阶段各自负责什么？以及在什么情况下会进入问题重写和扩展检索？*
- 参考答案 : `retrieve_initial` 做初始混合检索：dense + BM25 + RRF + rerank + auto-merge。`grade_documents` 用 grader 模型判断召回结果是否相关。若相关则结束；若不相关，则进入 `rewrite_question`，选择 Step-Back、HyDE 或 complex 策略生成扩展查询/假设文档。`retrieve_expanded` 用扩展后的内容再次检索，并和初始结果合并去重。

- *这个项目做了三级分块，并且采用“只把 L3 叶子块写入 Milvus，L1/L2 父块写入 PostgreSQL”的设计。*
*请你解释：*
*为什么不直接把完整大块都写进 Milvus 检索？这种 `L3 检索 → L2/L1 Auto-merging` 的设计，分别解决了检索准确性和回答上下文完整性上的什么问题？*