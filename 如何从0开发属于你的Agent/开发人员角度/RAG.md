# RAG 的发展历程


<img width="1371" height="883" alt="image" src="https://github.com/user-attachments/assets/954c7635-2a02-479b-a092-ed3ba08be196" />


---

# RAG 的核心工作流

> **目前，RAG通常被封装成“RAG Tool”，以工具调用的方式嵌入Agent工作流。这样的好处在于，Agent要想添加RAG，只需要接入RAG的API接口，方便把RAG作为单独的外挂模块进行管理。**

数据准备阶段（离线）+ 检索生成阶段（在线）

## 离线数据准备阶段核心工作流：

<img width="1820" height="567" alt="image" src="https://github.com/user-attachments/assets/4b01bcb7-89a3-4310-9ac3-f4225f93d2c7" />

---

## 在线检索生成阶段的核心策略：5种召回增强策略 + 1种重排策略

> 在RAG中，在线召回（=检索）增强是重要的一环。在工程上，通常会用“5+1”的策略链条完成召回增强。接下来我分别介绍这5种召回增强策略和1种重排策略。

---
5种召回增强策略
---

### 1. 动态路由 & 动态K Query-Aware Hybrid（召回动作发生前的规划）
> 根据Query类型，让路由动态地调整不同检索策略
  - 有独立识别码/ID/字段名/专有名词的Query：词面精确匹配最关键 → 提高 BM25 权重或增大𝐾𝑠（dense 可能把相近但不对的也捞进来）
  - 口语化问句/同义改写空间大/抽象问题的Query：提高Dense权重，语义匹配更关键 → 增大𝐾𝑑，必要时 Multi-Query/HyDE
  - 短 query：信息不足、容易漏召 → 增大 K 或触发 HyDE/Multi-Query
  - 长 query：包含多意图、容易稀释关键词 → 抽关键子句分解检索再融合
  - 其他不同Query特征：具体情况具体分析

---

### 2. 多路并行召回 Parallel Retrieval（召回的主要步 & 第一步）
> 把不同检索器的结果并联，直接提高覆盖面，**是召回的主要步骤**。
  
  - BM25 TopKₛ + Dense TopK𝒹 并行取候选 → Union + 去重
  - 多粒度、多语种、多字段倒排索引并行

**Hybrid最常见，ROI最高的召回方式**

---

### 3. Query侧扩展召回（也是召回的第一步，但不主要：在并行召回以外，触发一个“额外召回”）
> 从用户输入Query侧发力，让Sparse Retrieval和Dense Retrieval都容易召回正确的结果，但是主要用在Dense Retrieval，对Hybrid效果特别好！

  - Multi-Query MQE 多查询扩展：把用户输入的Query改写成多个问题问法 → Dense → 与BM25融合 (RRF)
  - HyDE假设文档生成：先让LLM生成假设答案，再根据假设答案 → Dense → 与BM25融合 (RRF)
  - 关键词抽取/结构化约束：从自然语言提取实体/版本/时间/产品名

• 关键词部分喂给 BM25（精确命中）

• 完整语义句喂给 dense（语义召回）

> **这个策略主要用在短Query以及同义语句容易改写/口语化Query/抽象Query中。**

---

### 4. 融合增强 Fusion （并行召回后去重作用）
> 把多路检索结果融合成一个更好的候选集，起到**去重**作用。
  
  - RRF（Reciprocal Rank Fusion）：只看总排名，不看分数。
  - 加权融合：归一化后 **wₛ * bm25 + w𝒹 * dense** 只看分数，不看排名，更可控但需要校准
  - 分桶融合：每路先保底取 N 个（quota），避免某一路“碾压”另一边
  - 多路 RRF：BM25、dense、HyDE、多查询等多列表一起 RRF

---

### 5. 全程过滤与约束（越早做越好，可以做多层，贯穿整个检索召回过程，提高有效召回）
> 这一步不提高召回候选的数量，但**会显著提升“有效召回率”**，减少垃圾候选挤占Top-K
  - 权限过滤（必须早做，否则召回质量和安全都出问题）
  - 时间/版本/语言/内容类型过滤
  - 黑名单/噪声源过滤（低质量文档、过时页面）
  - doc-level去重：避免某一文档多 chunk 抢占候选位

---
1种重排策略
---

### Rerank 重排/精排
> Rerank 不负责“从全库找”，它只在一个较小候选集里做精排，然后选出Top-N放入Prompt上下文
  - A. **Cross-Encoder Reranker 最经典**：把 query + candidate chunk 拼在一起喂给同一个 Transformer，让模型在注意力里做充分交互，再输出一个相关性分数。
  - B. **LLM Reranker**：让 LLM 读 query 和每个候选（或候选列表）给相关性评分/排序。


<img width="1465" height="630" alt="image" src="https://github.com/user-attachments/assets/f85a977c-c8bc-4324-8292-bc8ecab4dfd7" />



