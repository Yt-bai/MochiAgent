# RAG

要讲RAG，就要先提到**模型幻觉**

模型幻觉不仅有事实性错误幻觉，还有输出素质低下语句、输出未经考证或者根本不存在的内容、输出自相矛盾的内容等幻觉。

---

#### 降低模型幻觉的方式：

#### 1. 数据层面：将模型训练时用的数据进行高质量清洗，多引入事实性知识，后期进行强化学习RLHF等，从源头减小幻觉

#### 2.  模型层面：探索新的模型架构，或引入Prompt让模型能够在输出不确定性内容时主动标明

#### 3.  推理层面：

**检索增强生成（Retrieval-Augmented-Generation，RAG）**

**引导模型进行ReAct、Plan-and-Solve、Reflection等推理范式，学会自我反省或外部验证**

**工具调用，允许模型调用外部工具（如搜索引擎、计算器、代码解释器）来获取实时信息或进行精确计算**

---

# 什么是RAG？

Retrieval-Augmented-Generation，在生成回答前，先从外部知识库中检索相关信息，然后把检索到的知识拼成Prompt上下文喂给大模型，让大模型能够生成更靠谱的回答。

> **RAG分三个阶段，检索，召回增强（把检索到的知识进行筛选，按一定规则地拼成上下文），生成。**

---

# RAG 的发展历程


<img width="1371" height="883" alt="image" src="https://github.com/user-attachments/assets/954c7635-2a02-479b-a092-ed3ba08be196" />


---

# RAG 的核心工作流

> **目前，RAG通常被封装成“RAG Tool”，以工具调用的方式嵌入Agent工作流。这样的好处在于，Agent要想添加RAG，只需要接入RAG的API接口，方便把RAG作为单独的外挂模块进行管理。**

数据准备阶段（离线）+ 检索生成阶段（在线）


<img width="1505" height="487" alt="image" src="https://github.com/user-attachments/assets/3b9a1e36-9970-4e99-9662-b37246a31c1c" 


---

# 各种召回增强策略

