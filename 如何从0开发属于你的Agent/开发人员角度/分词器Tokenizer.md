# Tokenizer

在解析Transformer架构时，提到输入文本要先经过Tokenization变成Token，再Embedding。

不同LLM用不同的算法进行Tokenization，这些不同的算法就是不同类型的Tokenizer。

常见的Tokenizer有BPE算法、WordPiece算法和SentencePiece算法，也有其他的。

---

### 为啥Tokenizer的选择如此重要？

**1. LLM API调用成本：调用LLM是按照Token付费，要知道你调用的大模型怎么分词。**

**2. 上下文管理限制：避免输入Prompt过多超出限制。**

**3. 有的时候不同Tokenization的方式会影响模型对输入的理解。**

---

### 如何得知Tokenization的方式？

1. 在本地用目标模型使用的Tokenizer把构造的prompt先跑一遍，**count_tokens()**。

  你可以用 transformers.AutoTokenizer.from_pretrained("模型名") 在本地复现“这段文本会被怎么切 token”。

2. 在线计算平台

    比如按照模型分类的Hugging Face Space的The Tokenizer Playground：https://huggingface.co/spaces/Xenova/the-tokenizer-playground?utm_source=chatgpt.com

    按照算法分类的Tokenizer Playground：https://www.pythonalchemist.com/tools/tokenizer-playground?utm_source=chatgpt.com

*Hugging Face 是目前全球最火、最常用的 AI 模型开源社区 + 工具平台，你可以把它理解成 AI 界的 GitHub。

*其中常用的几块：Model Hub、Datasets、Transformers Hub、Tokenizer Hub、模型demo托管、Inference等等。
