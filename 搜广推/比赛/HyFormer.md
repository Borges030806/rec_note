
对Onetrans的进一步反思

现有大多数架构都采用一种**解耦式流水线**：先使用基于 query token 的序列压缩器（如 LONGER）对长序列进行压缩，再通过 RankMixer 这类 token-mixing 模块与稠密特征进行融合。这种方式限制了模型的表示能力和交互灵活性。

**HyFormer**，一种统一的混合 Transformer 架构，将长序列建模与特征交互紧密整合到同一个骨干网络中。

传统范式采取的是一种**压缩式、晚融合、单向交互**的模式，限制了模型的表达能力和可扩展性

现在的工业界大多采用：
1. 长序列建模（先压缩序列）
2. 异构特征交互（再和其他特征混合）

通过堆叠式设计，HyFormer 在两种轻量但表达力强的机制之间**交替执行**：
HyFormer的两个核心模块：
1. Query Decoding：利用全局 query token 对长行为序列逐层的 key-value 表征进行注意力计算，使全局上下文能够直接塑造序列表征；
2. Query Boosting：通过高效的 token mixing，进一步增强 query 与 query 之间、以及序列与序列之间的交互，从而在各层中逐步丰富语义表征。

### Query Generation
将非序列特征加工为一些query，然后用这些query去序列里做attention

##### 语义分组
不同类型的特征分开处理

##### query generation
先将所有非序列特征向量和行为序列的平均池化结果进行拼接，得到一个global info

接着，将global info输入到若干个前馈网络里，生成N个query向量，不同的query向量其实就是关注的不同语义方向


接下来，用query向量去和长行为序列做cross attention，从序列中提取有用信息

feature selection：不是所有输入特征都必须参与 query 生成，可以挑最有用的
query compression：可以对 query 做压缩，避免 query 数量太多

在更深层里，query不再通过MLP重新生成，而是复用上一层的query，也就是说，query是在逐层进化的

**Query Decoding（查询解码）**
将行为序列编码成每一层可以使用的Key和Value，之前从非序列特征中生成的query和他们进行组合

具体的编码方式有三种：
1. 完整transformer
2. **不是让整段长历史内部全面互相看，而是让一个压缩后的短表示去读取整个历史。**
3. 不使用attention，直接用前馈网络变换序列表示。

接下来，有了query、key和value之后，做一个cross attention，从而得到和当前任务更相关的序列信息

**Query Boosting（查询增强）**







HyFormer 模块由多层堆叠构成，每一层都包含一个 **Query Decoding（查询解码）** 模块和一个 **Query Boosting（查询增强）** 模块，且前者在前、后者在后。
![[Pasted image 20260409161501.png]]

**每条序列先各自被编码成 K/V，然后全局 query 去每条序列里做 cross-attention 读取信息，读出来之后再和 NS 特征一起做 boosting，生成下一层要用的 global tokens。**
