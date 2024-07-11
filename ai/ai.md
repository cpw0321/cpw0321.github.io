# ai

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


为什么优秀
+ 预训练pre-trained
+ 指令微调instruction-tuning 针对特定任务标注数据进行微调



#### 1.2.3 预训练语言模型三种网络架构
+ Encoders --> BERT
+ Decoders -->GPT
+ Encoders-Decoders --> T5/BART

![img.png](images/预训练语言模型.png)

### 1.3. prompt
提示词，优化提示词可以使用大模型时收费少

+ Cot 思维链chain-of-thought prompting --> 允许模型将多步问题分解为中间步骤
+ self-consistency 多路径推理，提升Cot性能 --> 三个60%加起来效果可能超过一个80%
+ ToT 思维树 Tree-of-Thoughts


---------------------------

### RAG 检索增强
Retrieval Augmented Generation ，意为检索增强生成


### agent
做决策使用



----------------------

## 2. 开发
### 2.1. dify
是一个开源 LLM 应用开发平台。使用 RAG 引擎编排从代理到复杂 AI 工作流的 LLM 应用

### 2.2. longchain
大语言的开发框架,是一个middle层

### 2.3. ollama
go开发本地大语言模型运行框架



资料：
+ 个人简单笔记 https://wiki.eryajf.net/pages/97047e/#%E5%89%8D%E8%A8%80

+ 一个基于grop+Llama3生成书籍的 https://github.com/Bklieger/groqbook   