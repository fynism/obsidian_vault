- Q : 这个项目的流式聊天里，为什么不能让 `search_knowledge_base` 工具直接向前端推送 RAG 步骤？项目是如何解决“同步工具在线程池中执行，但 SSE 输出在 asyncio 事件循环中”的跨线程通信问题的？

- 参考答案: `search_knowledge_base` 作为同步工具可能在线程池中执行，不能直接操作主事件循环里的 SSE 输出。项目在流式函数里创建统一 `asyncio.Queue`，调用 `set_rag_step_queue()` 捕获当前 running loop；工具线程里通过 `emit_rag_step()` 使用 `loop.call_soon_threadsafe(queue.put_nowait, step)` 把 RAG 步骤安全投递回主循环。


