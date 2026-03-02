# 如何从0搭建一个Agent？
2023年，Lilian Weng，前 OpenAI 研究与安全副总裁，Thinking Machines 联合创始人，发布了一篇著名博客，*LLM Powered Autonomous Agents*。

*LLM Powered Autonomous Agents* 提出**Agent = LLM + 规划 + 记忆 + 工具 的经典框架**，成为 AI Agent 领域奠基性文献。

----------------------------------------------------------------------------------------------------------------

### 因此，在概念上

**Agent = 四块核心（规划、记忆、工具、行动） + 一个控制循环。**

<img width="1332" height="509" alt="image" src="https://github.com/user-attachments/assets/ab39f6d7-c5dd-428b-9250-0b8cc51d95bc" />

这四块核心分别是

#### 1. Planning（规划/决策）
• 任务分解、下一步选择、必要时反思/自检
#### 2. Memory（记忆）
• Short-term（任务工作区 / 当前状态摘要）

• Long-term（跨任务偏好/知识库/向量检索）
#### 3. Tools（工具）
• 搜索、计算、代码执行、数据库、日历、HTTP API 等外部能力
#### 4. Action（行动/执行）
• 调用工具、写入系统、生成文件、触发工作流

• Control Loop（控制循环 / Orchestrator）
把上面四个串起来反复运行：Decide → Act → Observe → Update → Stop/Continue

----------------------------------------------------------------------------------------------------------------------

### 但在实际工程中，我们还需再考虑另外4个模块：

#### 5.State / Blackboard（状态管理）
• 明确区分：事实（tool result） vs 推断（model guess）

• 保存：进度、预算、错误次数、权限上下文
#### 6. Guardrails / Policy（权限与安全策略）
• 工具白名单、读写分离、敏感动作二次确认

• 防 prompt injection / 数据泄露防 即时注射 /
#### 7. Evaluation / Verification（验证与质量控制）
• Schema 校验、引用工具证据、单测/规则检查

• 不把“reflection 自省”当成唯一保障
#### 8. Observability（可观测性/Trace）
• 全链路日志：每轮决策、工具参数、工具结果、计划变化

• 可回放、可调试、可监控

