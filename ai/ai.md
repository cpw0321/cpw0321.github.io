# ai

## 术语解释
+ 大模型（Large Models）：

大模型通常指的是参数数量庞大、计算能力强大的深度学习模型。这些模型可以有数十亿到数百亿个参数，通常用于处理复杂的自然语言理解、机器翻译、图像处理等任务。这些模型的训练需要大量的计算资源和数据，并且能够捕捉更复杂的数据模式和语义信息。


参数：
6B=60亿

主要能力：
+ 推理
+ 知识量--> 被编码为模型中的权重和参数



+ 微调（Fine-tuning）：

微调是指在已经预训练好的大模型的基础上，使用少量目标领域特定的数据进行进一步训练，以适应特定任务或应用场景。通常，预训练模型是在大规模通用数据上进行训练的，而微调则是为了使模型能够更好地适应特定任务的数据分布和特征。

+ 量化（Quantization）：

量化是指将深度学习模型中的浮点参数和计算转换为更低精度的表示形式，例如整数或定点数。目的是通过减少模型中参数和计算的精度来减少计算和存储的需求，从而提高模型的效率和运行速度。量化可以在训练后应用于模型，也可以在训练期间用于优化计算过程


---------------------------
## agent
做决策使用
可以理解为大模型上开发的各种插件和应用

大模型+插件+执行流程=agent

字节的coze 配置都在云上，无法私有嵌入你的本地，自己玩一玩可以
autogpt/metagpt/langchain 程序员开发框架
autogenStudio 微软的开源的适合业务人员不懂代码的构建自己的知识库


+ sora 生成视频

---------------------------
## RAG 检索增强
Retrieval Augmented Generation ，意为检索增强生成

通过引入外部知识库检索机制，提升生成内容的准确性、相关性和时效性

## GraphRAG
知识图谱

比如：获取技术部们去年的成果
海量数据--> llm --> 知识图谱 --> 社区挖掘 --> 总结汇总


## Reranker
参考视频：https://www.bilibili.com/video/BV1Fs421T7Xm/?spm_id_from=333.337.search-card.all.click&vd_source=9a6776332c8894a5253390cd88bdf876

用于rag检索后在分类

+ 使用Cross-Encoder
+ RAG使用Bi-Encoder 双向的

langchain提供的库：
+ FlashRank



----------------------
## LLM

### 微调
+ 全量微调：全部指令改动
+ PEFT： 
	- LoRA：去掉冗余的指令，对精简后的指令微调

大模型输入问题的token会猜测下一个单词，从当前单词中选一个继续推测下一个单词，所以是一点点输出

### 参数
+ top K 假如k=3，取概率最大的前三个token,筛选掉大部分token
+ top P 一个概率的阈值，当选出来的token加起来的概率小于p值可以继续增加token
+ beans 集束采样，先生成一个token根据该token在生成相关的token，第一个token和第二次生成token中的概率加起来取最大的


机器性能：
GPU：最少 A100-40G才能进行推理，最强的是H100
		 A100-80G才能进行训练



----------------------


## 1. 基础

### 1.1. transfermer（神经网络中的一个分支, 训练模型）
Transformer是一种革命性的深度学习模型架构，通过注意力机制和多层堆叠的方式，显著提高了处理序列数据任务的能力和效率

提出RNN更好的一种架构

+ Encoder/Decoder
+ attention

 
### 1.2. 大模型
#### 1.2.1 BERT
使用深度的transfermer

+ 对上下文理解能力强

#### 1.2.2 GPT

+ 根据上文给出下文

GPT1 --> 增加了多个transfermer
GPT2 --> 喂更多数据
GPT3 --> 喂更多数据，增加爬虫获取网络数据，数据加载需要更多的GPU所以使用in-context learning上下文，使用了prompt
in-context learning其实就是给一个示例让参考执行
GPT3.5 --> 与人对话，人类的反馈
GPT4 --> 

为什么优秀
+ 预训练pre-trained
+ 指令微调instruction-tuning 针对特定任务标注数据进行微调



#### 1.2.3 预训练语言模型三种网络架构
+ Encoders --> BERT
+ Decoders -->GPT
+ Encoders-Decoders --> T5/BART  t5是文本到文本
	都是transfermer模型
	- GPT 自回归--每个词只能看到左边的词
	- T5（BART） 自回归+自编码--meta--GPT+BERT结合起来
	- BERT 自编码--google--每个词能看到左边和右边的词
	

![img.png](images/预训练语言模型.png)

### 1.3. prompt
提示词，优化提示词可以使用大模型时收费少

+ Cot 思维链chain-of-thought prompting --> 允许模型将多步问题分解为中间步骤
+ self-consistency 多路径推理，提升Cot性能 --> 三个60%加起来效果可能超过一个80%
+ ToT 思维树 Tree-of-Thoughts


### 1.4. Enbedding 嵌入
向量

+ representation learning 表示学习
  - DNN 深度神经网络

直观的理解：
 text --> embedding model --> text as vaector (比如 0.000 0.006...)


### 1.5. 向量数据库
苹果-->各种特征-->[11, 22, ...]数组， 向量数据

传统数据库--> 行列分析
向量数据库--> 非结构化数据， 根据权重输出结果


### 1.6. openAI开发
### 1.6.1. openAI提供的大模型
+ Embeddings
+ GPT-4
+ GPT-3.5
+ GPT-3
+ Moderation 分类模型
+ Deprecated 探测文本是不敏感违规

### 1.6.2. Token大小与计费
计算一段话中包含多少个token，比如750个单词中有35个Token, Token视为单词的组成部分

+ tiktoken openai提供的token的包，一种BPE分词器

接口调用返回体会描述token数量
"usage":{
	"completion_tokens": 17, // output返回结果中token
	"prompt_tokens": 57, // input输入中token
	"total_tokens": 74 // 总共的token数量
}


### 1.6.3. api
以http接口提供

查看有那些模型
GET https://api.openai.com/v1/models   查看有哪些模型


### 1.6.4. message(其实就是prompt)
GPT-3.5之后是对话的方式，所以为message

角色：
+ system 设定一个身份，比如你是一个有用的助手
+ user 你，提问题的人
+ assistant 回答你问题的

### 1.6.5. 上下文
就是上面回答问题放在一个数组中，即历史消息也知道


### 1.6.6. tiktoken 
openai提供的token的包，一种BPE分词器


编码，不同的编码会有不同的token,有以下三种编码
+ cl100k_base
+ p50k_base
+ r50k_base/gpt2

获取模型的编码
encoding= tiktoken.encoding_for_modle('gpt-3.5-turbo')


+ openAI模型实践：https://platform.openai.com/playground/chat?models=gpt-3.5-turbo-0125


### 1.6.7. function calling
调用外部的一些功能


### 1.6.7. 实战技巧
+ 角色设定
+ 指令注入： system设置比如"主题创作"
+ 问题拆解：复杂问题拆分多个子问题
+ 分层设计：创作长篇内容，先章节在补充细节，如：小说生成
+ 编程思维：prompt当成编程语言，主动设计模板和正文， 如：评估模型输出质量
+ Few-shot: 基于样例设计prompt，规范推理路径和输出样式，如：构造训练数据


### 1.6.8. 实战项目
1、translator 翻译



## 2. 开发
### 2.1. dify
是一个开源 LLM 应用开发平台。使用 RAG 引擎编排从代理到复杂 AI 工作流的 LLM 应用

### 2.2. longchain
大语言的开发框架,是一个middle层



#### 2.2.1. langchain实战
```python
# 安装
pip3 install langchain

```

+ langchain 官方文档 https://python.langchain.com/v0.2/docs/integrations/retrievers/


### 2.3. ollama

+ xinference 与ollama类似


go开发本地大语言模型运行框架

```bash
# 命令
ollama run llama3

ollama show
ollama help
ollama list
ollama rm

```

加载指定的模型的配置
```text
FROM "/Users/zhigui/Downloads/ggml-model-q2_k.gguf"

TEMPLATE """{{- if .System }}
<|im_start|>system {{ .System }}<|im_end|>
{{- end }}
<|im_start|>user
{{ .Prompt }}<|im_end|>
<|im_start|>assistant
"""

SYSTEM """"""

PARAMETER stop <|im_start|>
PARAMETER stop <|im_end|>
```

```bash
# 创建
$ ollama create llama3-cn -f ./config.txt

# 运行
ollama run llama3-cn


# 调用
curl http://localhost:11434/api/generate -d '{
	"model": "llama3-cn:latest",
	"prompt": "什么是docker",
	"stream": true
}'

```


### 2.4. 自己实战微调
+ huggingface上下载别人微调好的模型
+ peft huggingface可调用
+ lora 出来要进行合并
+ 量化 -- llama.cpp
+ 部署 ollama/lmstudio


-----------------------

# NLP (Natural Language Processing)

NLP（自然语言处理）是计算机科学的一个分支，专注于计算机与人类（自然）语言之间的交互。它是人工智能（AI）的重要组成部分，旨在使计算机能够理解、解释和生成人类语言。

## 主要任务

1. **文本分类**
   - 情感分析
   - 主题分类

2. **命名实体识别 (NER)**
   - 识别文本中的人名、地名、组织机构等

3. **词性标注 (POS Tagging)**
   - 为文本中的每个单词标记语法属性

4. **句法分析**
   - 分析句子结构，确定单词间的关系

5. **语义分析**
   - 理解文本深层含义及上下文关系

6. **机器翻译**
   - 自动翻译文本

7. **问答系统**
   - 回答用户提出的问题

8. **聊天机器人**
   - 进行自然语言对话

9. **语音识别**
   - 将语音信号转换为文本

10. **语音合成**
    - 将文本转换为语音输出

## 关键技术

1. **统计方法**
   - 使用概率模型和统计学习技术

2. **规则基础方法**
   - 依据语言学规则解析文本

3. **深度学习**
   - 利用神经网络，例如 RNN、LSTM、Transformer

4. **词嵌入**
   - 将词语表示为向量形式

5. **注意力机制**
   - 处理序列数据时突出输入的不同部分

6. **预训练模型**
   - 使用大规模语料库预训练模型后微调

## 应用

NLP 技术应用于多个领域，包括但不限于：

- **搜索引擎优化**
  - 提高搜索结果的相关性

- **社交媒体监控**
  - 分析用户情感倾向

- **客户服务**
  - 自动化客户支持

- **医疗健康**
  - 辅助诊断、患者记录分析

- **教育**
  - 个性化学习内容推荐

- **金融**
  - 风险评估、市场情绪分析

## 结论

NLP 是一个快速发展且充满挑战的学科。随着深度学习技术的进步，NLP 的应用领域不断扩大，正在改变我们与计算机交互的方式。


-----------------------

# 神经网络 neural network

## 神经元


## 词向量 word2vec
给一句话，遮挡某些词，进行预测

## 循环神经网络 RNN


## 门控循环单元 GRU


## 长短期记忆网络 LSTM

-----------------------

资料：

文档类：
+ [高] 模型评测排行榜 https://cevalbenchmark.com/index_zh.html#home_zh
+ [高] langchain使用指南，简单 https://www.tizi365.com/topic/2539.html
+ [高] 谷歌收费课件，了解ai+实战代码 https://github.com/DjangoPeng/openai-quickstart
+ [高] fastapi教程 https://fastapi.tiangolo.com/zh/tutorial/body/


+ [中] 个人开发者笔记，langchian教程，有一些使用例子 + langchain教程 https://liaokong.gitbook.io/llm-kai-fa-jiao-cheng#chain-lian
+ [中] langchain总结 https://langchain114.com/docs/use_cases/summarization/
+ [中] langchain英文教程：https://www.python-engineer.com/posts/langchain-crash-course/
+ [中]-[51cto博客] 理解llama3整个原理过程 https://www.51cto.com/article/788872.html

+ [低]个人开发者langchain学习笔记 https://juejin.cn/user/3125246096841600/posts
+ [低]-[科普类] 讲解数据的重要性 https://www.53ai.com/news/qianyanjishu/1237.html


代码类：
+ [高] nextjs写的开源的ai前端聊天界面 https://github.com/ChatGPTNextWeb/ChatGPT-Next-Web


+ [低]参考类项目，一个基于grop+Llama3生成书籍的 https://github.com/Bklieger/groqbook 


视频类：
+ [高]-[知识普及]-[已学] ai老兵，简单的话普及ai知识: https://www.bilibili.com/video/BV1tthPeFEWb/?p=3&spm_id_from=pageDriver 
+ [高]-[代码实战博主] 讲解一些实战，效率高 https://www.bilibili.com/video/BV11Z42127zx/?spm_id_from=pageDriver&vd_source=9a6776332c8894a5253390cd88bdf876


+ [中]-[实战教程] 迪哥-搬运别人教程，langchain rag https://www.bilibili.com/video/BV1Sb421n7to/?spm_id_from=333.337.search-card.all.click&vd_source=9a6776332c8894a5253390cd88bdf876
+ [中]-[解释原理]-[未看-低] 详细的解释什么是RAG: https://www.bilibili.com/video/BV1Vj421Z7UP/?spm_id_from=pageDriver&vd_source=9a6776332c8894a5253390cd88bdf876



待看：
大模型介绍：https://www.bilibili.com/video/BV1AU421o7ob/?spm_id_from=333.337.search-card.all.click&vd_source=9a6776332c8894a5253390cd88bdf876


需要看的技术点：
+ 文生视频：https://www.jiqizhixin.com/articles/2024-07-01-6


