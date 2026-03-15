启动时，build_agent() 会创建 WorkspaceContext，加载 .env，选择 provider，恢复或新建 session。随后 Pico 构造工具注册表、稳定 prefix、上下文管理器、记忆层和 run store。

每次 ask() 会先把用户请求写入 session，并创建 .pico/runs/<run_id>/。每一轮都会重新构建 prompt，调用模型，解析模型输出。模型被约束为只能返回一个 <tool> 或 <final>。工具执行统一走 run_tool()，包括工具存在性、参数校验、重复调用拦截、审批策略、路径逃逸防护、workspace diff、记忆更新和 trace 记录。

记忆分三层：session history 保存完整过程；working memory 保存任务摘要、最近文件、文件摘要；durable memory 保存用户明确要求持久化的项目约定、决策、依赖事实和偏好。上下文预算由 ContextManager 控制，优先保留当前请求和近期历史。

