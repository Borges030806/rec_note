LONGER: Scaling Up Long Sequence Modeling in  Industrial Recommenders

论文提出的 LONGER (Long‑sequence Optimized traNsformer for GPU‑Efficient Recommenders) 是一种**端到端**的**长序列建模**框架，其核心在于**全局 Token 与序列 Token 联合输入、Token 压缩和混合注意力机制**。

全局token放在长序列的开头位置，作用如下：
1. 作为全局信息锚点，可聚合上下文信号并促进候选商品与用户历史的交互；
2. 放在序列开头可稳定长序列的注意力分布，缓解“**注意力沉降**”现象。

**注意力沉降**：在很深的 attention 层里，经常会出现一种现象：很多 token 的 attention 权重都会集中到序列**开头**的少数 token 上。


为了表达时序信息，序列 Token 采用绝对时间差（行为时间与请求时间的差）和绝对位置编码，并通过 MLP 映射到统一维度。


长序列，为减少计算，LONGER 按照时间顺序将 K 个相邻行为合并成一个组（Token Merge），序列长度从 L 降至 L/K。为了避免简单拼接造成的信息损失，每个组内部加入一个轻量级 Transformer 子模块 InnerTrans，在组内建模依赖关系。

**混合注意力机制**：一层cross causal attention 和 N层self causal attention
**cross causal attention**：q是global token + 从长序列中抽样的k个序列token（一般是recent）
k和v是原始长序列token（global token + 长序列token）
**self causall attention**：经过cross attention的global token + recent k seq token做自注意力

**高层特征融合**：**经过混合注意力后，模型将输出通过多个 MLP 层和拼接层与其他特征融合，最后得到点击或转换概率。**
