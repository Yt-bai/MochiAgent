# Agent核心工作流

<img width="1494" height="655" alt="image" src="https://github.com/user-attachments/assets/7ddfc1dc-d42d-4da2-99b7-b16858ee49a8" />



<img width="1497" height="637" alt="image" src="https://github.com/user-attachments/assets/19ad6840-2355-4340-8699-84e0b93cd00b" />




**这种模块化的协同机制与持续的迭代循环，构成了LLM驱动智能体解决复杂问题的核心工作流。**

---


# Agent技术栈

下图为Agent技术栈概览：

<img width="5100" height="6122" alt="image" src="https://github.com/user-attachments/assets/7db548d2-0eb7-4054-a4f6-d46a756c3fcc" />

该AI Agent技术栈图由Letta公司于2024年11月发布，并说明AI Agent技术栈的三个关键层：**Agent Hosting & Serving**、**Agent Frameworks**、**Model Serving & Storage**。

---

# Model Serving模型托管
### Model Serving并不是LLM本身，而是把模型跑起来并对外提供推理能力的“托管服务 / 推理平台 / 推理引擎 / 本地运行工具”。

<img width="1180" height="488" alt="image" src="https://github.com/user-attachments/assets/71dc556b-c747-4ef7-ba55-1f37482b89bb" />

**1. 提供闭源模型+云端API：OpenAI / Anthropic / Gemini**

提供部署在厂商平台上的模型的API（托管），用户拿不到权重，但可以上传数据，由厂商在他们平台上帮用户训练微调（如GPT-4o）

**2. 提供开源权重模型部署发API服务商：Together.ai / Fireworks AI / Groq**

帮忙把网上已有的开源大模型部署在他们平台上，无需用户自身电脑有过硬的算力和硬件设施，主要是提供强大的推理算力平台，卖API托管服务。

**3. 免费让你把开源权重的大模型在自己电脑上跑起来的工具：vLLM / SGLang、Ollama / LM Studio**

免费，只是一种快捷工具，但是在本地部署大模型意味着你的电脑需要有足够强大的硬件和算力，自己运维，自己配环境（比如自己租云GPU，AutoDL）。

• vLLM / SGLang：更偏生产级自托管（高并发、吞吐、服务化），要GPU，公司用
• Ollama / LM Studio：更偏个人电脑本地运行（上手快、体验友好），可以CPU,模型体量小

---

# Storage存储

<img width="1160" height="592" alt="image" src="https://github.com/user-attachments/assets/a7dbbf16-f2ee-410e-a4d0-4e7753c1d35e" />

存储，即向量数据库（Vector DB），存{主键id、嵌入向量embedding、元数据metadata、原文text}

可以存**Agent的持久状态**（比如对话历史、记忆、用户偏好、任务进度、工具调用记录及返回结果等）和**RAG的外部知识库**，方便随时更新知识库。

常用数据库：

Chroma：开发者友好，轻量RAG、本地原型

Qdrant：强调性能与过滤，自托管/云托管

Milvus：大规模、工程化、企业

Pinecone：商业化的“托管向量数据库”代表，主要买它的云服务

Weaviate：语义检索/知识库类

---

# Libraries各种功能库

<img width="1193" height="223" alt="image" src="https://github.com/user-attachments/assets/f144a347-5298-4cbe-b525-4e656dc05328" />

**1. Memory代码库：MemGPT/Letta、Zep / LangMem / mem0**

图示的几种是代码库，提供写入策略 + 检索策略 + 生命周期治理 + 与Agent编排框架集成的统一的函数模块。

```text

1. 收到用户输入 u
2. 从 memory 检索相关记忆 M = retrieve(u, user_id, context_budget)
3. 拼 prompt：system + tool specs + M + recent conversation + u
4. LLM 生成回复/或 tool call
5. 执行工具（如需要），得到结果 t
6. 输出最终回复 a
7. 把重要信息写入 memory write(u, a, t, user_id)（抽取事实/偏好/摘要）
8. 下次循环

```
MemGPT/Letta：偏“框架/运行时”——你可能直接用它来搭 专门做记忆范式的agent，把记忆管理作为核心机制。

Zep / LangMem / mem0：偏“记忆层（memory layer）”——更常作为你现有系统（LangChain、CrewAI、自研 agent）的一个组件插进去。

尤其**LangMem**：Langgragh和langchain的自研框架。

**2. Tool Libraries工具提供方：Composio、Browserbase、Exa**

在 agent 体系里，“工具（Tool）”在工程实现上通常就是一个可调用的函数/接口（function / RPC / HTTP endpoint），LLM 通过结构化输出选择它并填参数，**调用工具就是执行函数**。

```text

工具调用 = LLM 生成JSON结构化调用意图；系统识别并把它映射到真实可执行的函数/接口并执行；再把结果返回给 LLM

```

**Composio**：更像“通用工具库 + 授权管理（OAuth）”。帮你一口气接很多 SaaS（Google、Slack、GitHub…），并处理鉴权、token 管理等。

**Browserbase**：专门做“受控的浏览器工具”（远程浏览器/自动化浏览），让 agent 能稳定地“上网操作”。

**Exa**：专门做“网页搜索/检索”的工具（search API），给 agent 提供高质量的 web search 能力。

**3. Sandboxes沙箱：被隔离的安全执行环境**

由于上面写了：

```text
工具调用 = LLM 生成JSON结构化调用意图；系统识别并把它映射到真实可执行的函数/接口并执行；再把结果返回给 LLM

```

这表明，LLM只负责选择用哪个工具，但是调用工具的具体行动并不由LLM去执行，而是由用户自己的系统去执行，因此必须隔离执行环境，防止Agent随意访问任意资源。**沙箱就是安全隔离层，让Agent在受限的安全环境下执行工具**。

```text

1. （读取有Memory和用户输入和环境状态等整合的Prompt以后）LLM 生成：{"tool": "...", "args": {...}}（选择工具和参数）
2. 运行时，你自己的后端系统在 Sandbox 内执行工具（安全隔离）
3. 工具调用结果回传给 LLM
4. 重要信息写入 Memory（长期记忆）
5. 下次提问时从 Memory 检索相关信息回填 prompt

```

---

# Agent Frameworks：

<img width="1210" height="261" alt="image" src="https://github.com/user-attachments/assets/805866b7-ad69-4cce-ba7c-d5c34a8745c0" />

