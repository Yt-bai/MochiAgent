# 如何选择大模型

需要考虑性能、成本、延迟、上下文窗口Token处理数量限制、部署是否便捷、是否开源、适配的生态与工具链、安全与伦理等等。

---

闭源模型：

国外：**OPEN AI ChatGPT、Google Gemini、Anthropic Claude**

国内：**百度文心一言(ERNIE Bot)、腾讯混元(Hunyuan)、华为盘古(Pangu-α)、科大讯飞星火(SparkDesk)和月之暗面(Moonshot AI)等**

开源模型：

国外：**Meta Llama、Mistral AI**

国内：**阿里Qwen、Deepseek、智谱AI ChatGLM**

---

一些帖子可供参考：

知乎：https://zhuanlan.zhihu.com/p/1944817122900412165?utm_source=chatgpt.com

Hugging Face：https://huggingface.co/open-llm-leaderboard?utm_source=chatgpt.com

Letta官网的排名（更新于2026年2月）：https://leaderboard.letta.com/

<img width="1162" height="875" alt="image" src="https://github.com/user-attachments/assets/c5d08680-8558-4bb9-bd82-da2512b6bbe9" />

<img width="1182" height="931" alt="image" src="https://github.com/user-attachments/assets/6fe081e8-510d-4a3c-a309-681f2497d1a9" />

---

# 如何下载开源大模型

Hugging Face Model Hub提供了很多预训练好的开源大模型下载的接口。

先安装必要的库：
```text
pip install transformers torch
```
接下来从Hugging Face上下载模型权重和Tokenizer：
```text
import torch
from transformers import AutoModelForCausalLM, AutoTokenizer

# 指定模型ID
model_id = "Qwen/Qwen1.5-0.5B-Chat"

# 设置设备，优先使用GPU
device = "cuda" if torch.cuda.is_available() else "cpu"
print(f"Using device: {device}")

# 加载分词器
tokenizer = AutoTokenizer.from_pretrained(model_id)

# 加载模型，并将其移动到指定设备
model = AutoModelForCausalLM.from_pretrained(model_id).to(device)

print("模型和分词器加载完成！")
```
接下来就准备对话的输入，Tokenizer编码输入文本，让LLM generate回答，再通过decode解码成自然语言，输出回答。

---

# 如何使用闭源大模型

从官网付费调用API。
