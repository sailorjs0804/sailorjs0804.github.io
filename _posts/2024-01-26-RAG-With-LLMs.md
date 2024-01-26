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
| 特征比较           | RAG                                                | 微调 (Fine-tuning)                                      |
|----------------------|-----------------------------------------------------|---------------------------------------------------------|
| 知识更新             | 直接更新检索知识库，确保信息持续更新，无需频繁重新训练，非常适合动态变化的数据环境。| 存储静态数据，需要重新训练用于知识和数据的更新。                   |
| 外部知识             | 擅长利用外部资源，特别适合处理文档或其他结构化/非结构化数据库。       | 可用于将预训练中外部学习到的知识与大型语言模型保持一致，但对于频繁变化的数据源可能不太实用。|
| 数据处理             | 对数据的处理和操作要求极低。                                | 依赖于构建高质量的数据集，有限的数据集可能无法显著提高性能。             |
| 模型定制             | 侧重于信息检索和融合外部知识，但可能无法充分定制模型行为或写作风格。 | 允许根据特定风格或术语调整 LLM 行为、写作风格或特定领域知识。         |
| 可解释性             | 答案能够追溯到具体的数据来源，提供更高的可解释性和可追踪性。         | 就像一个黑盒子，并不总是清楚模型为什么会做出某种反应，可解释性相对较低。 |
| 计算资源             | 需要计算资源来支持检索策略和数据库相关技术。外部数据源的整合和更新需保持维护。| 有必要准备和整理高质量的训练数据集，确定微调目标，并提供相应的计算资源。 |
| 延迟要求             | 因涉及数据检索，可能带来较高的延迟。                         | 经过微调的大语言模型 (LLM) 可以不通过检索直接回应，降低延迟。           |
| 降低幻觉             | 由于每个回答都基于检索到的实际证据，因此本质上更不容易产生错误或虚构的回答。| 根据特定领域的数据训练模型，有助于减少幻觉，但面对未训练过的输入时仍可能出现幻觉。|
| 伦理和隐私问题        | 从外部数据库存储和检索文本可能引起伦理和隐私方面的担忧。            | 训练数据中的敏感内容可能会引起伦理和隐私方面的问题。                 |

## Reference
- https://zhuanlan.zhihu.com/p/674733670
- https://luxiangdong.com/2023/09/25/ragone/#/RAG%E6%9E%B6%E6%9E%84
- https://zhuanlan.zhihu.com/p/634015951
- https://medium.com/@ahmedbehairy/rag-vector-databases-vs-knowledge-graphs-f22697b1a940
- [RAG](https://arxiv.org/abs/2005.11401v4)
