# Agent核心工作流

<img width="1494" height="655" alt="image" src="https://github.com/user-attachments/assets/7ddfc1dc-d42d-4da2-99b7-b16858ee49a8" />



<img width="1497" height="637" alt="image" src="https://github.com/user-attachments/assets/19ad6840-2355-4340-8699-84e0b93cd00b" />




**这种模块化的协同机制与持续的迭代循环，构成了LLM驱动智能体解决复杂问题的核心工作流。**

---


# Agent技术栈

下图为Agent技术栈概览：

<img width="5100" height="6122" alt="image" src="https://github.com/user-attachments/assets/7db548d2-0eb7-4054-a4f6-d46a756c3fcc" />

该AI Agent技术栈图由Letta公司于2024年11月发布，并说明AI Agent技术栈的三个关键层：**Agent Hosting & Serving**、**Agent Frameworks**、**Model Serving & Storage**。

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


