# RAG With LLMS
## Introduction
- RAG refers to Retrieval-Augmented Generation,which is an AI framework for improving the quality of LLM-generated responses by grounding the model on external sources of knowledge to supplement the LLM’s internal representation of information to enhance the accuracy and reliability
- RAG takes an input and retrieves a set of relevant/supporting documents given a source (e.g., Wikipedia). The documents are concatenated as context with the original input prompt and fed to the text generator which produces the final output
## Principle
  <img width="676" alt="image" src="https://github.com/sailorjs0804/sailorjs0804.github.io/assets/147221910/ce19ff77-c944-4c2c-bea4-59e9b9577ca6" align="center">

检索增强生成的工作流程：
1. 索引：从数据来源处获取数据并建立索引的过程
  a. 数据索引： 包括清理和提取原始数据，将 PDF、HTML、Word、Markdown 等不同格式的文件转换成纯文本。
  b. 分块： 将加载的文本分割成更小的片段。由于语言模型处理上下文的能力有限，因此需要将文本划分为尽可能小的块。
  c. 嵌入和创建索引： 这一阶段涉及通过语言模型将文本编码为向量的过程。所产生的向量将在后续的检索过程中用来计算其与问题向量之间的相似度。由于需要对大量文本进行编码，并在用户提问时实时编码问题，因此嵌入模型要求具有高速的推理能力，同时模型的参数规模不宜过大。完成嵌入之后，下一步是创建索引，将原始语料块和嵌入以键值对形式存储，以便于未来进行快速且频繁的搜索。
2. 检索：根据用户的输入，采用与第一阶段相同的编码模型将查询内容转换为向量。系统会计算问题向量与语料库中文档块向量之间的相似性，并根据相似度水平选出最相关的前 K 个文档块作为当前问题的补充背景信息。
3. 生成：最后将给定的问题与相关文档合并为一个新的提示信息。随后，大语言模型（LLM）被赋予根据提供的信息来回答问题的任务。根据不同任务的需求，可以选择让模型依赖自身的知识库或仅基于给定信息来回答问题

## Why RAG
1. 模型幻觉问题：LLM文本生成的底层原理是基于概率进行生成的，在没有已知事实作为支撑的情况下，不可避免的会出现一本正经的胡说八道的情况。
2. 时效性问题：LLM的参数量越大，训练的成本也就越高，时间也越长。因此具有一定时效性的数据就可能无法及时参与 训练，造成模型无法直接回答与时效性相关的问题。
3. 数据安全问题：开源的LLM是没有企业内部数据和用户数据的，如果企业想在保证数据安全的前提下使用LLM，一种比较好的解决办法就是把数据放在本地，企业数据的业务计算全部放在本地完成。而在线的LLM只是完成一个归纳总结的作用。

## Retrieve
<img width="471" alt="image" src="https://github.com/sailorjs0804/sailorjs0804.github.io/assets/147221910/84c2ce07-93b9-4847-9b2c-ee6752347205" align="center">

### Vector Database
- 向量数据库是表示实体或概念(如单词、短语或文档)的高维向量的集合。数据库可用于测量不同实体或概念之间的相似性或相关性，基于它们的向量表示
- 查询向量数据库通常涉及搜索相似的向量或 检索基于特定标准的向量
### Knowledge Graph
- 知识图谱是表示实体或概念及其关系(如事实、属性或类别)的节点和边的集合。基于它们的节点和边缘属性，可用于查询或推断关于不同实体或概念的事实信息
### Elastic
- 语义相似性

## RAG VS Fine-Tuning
<img width="777" alt="image" src="https://github.com/sailorjs0804/sailorjs0804.github.io/assets/147221910/a0fc54a9-fc04-4554-bb65-f3968464c0b4">


## Reference
- https://zhuanlan.zhihu.com/p/674733670
- https://luxiangdong.com/2023/09/25/ragone/#/RAG%E6%9E%B6%E6%9E%84
- https://zhuanlan.zhihu.com/p/634015951
- https://medium.com/@ahmedbehairy/rag-vector-databases-vs-knowledge-graphs-f22697b1a940
- [RAG](https://arxiv.org/abs/2005.11401v4)
