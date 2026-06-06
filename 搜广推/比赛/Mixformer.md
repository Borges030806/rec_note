**MixFormer**，一种面向推荐系统的统一 Transformer 风格架构，它能够在单一主干网络中同时建模**序列行为**和**特征交互**

序列建模和稠密特征交互，当这两部分被分别参数化和独立优化时，它们就会竞争同一份计算和参数预算，从而产生彼此冲突的扩展激励
要想在大规模推荐系统中实现有效的协同扩展，就需要一种根本不同的架构原则：**序列建模和特征交互必须在单一参数空间中统一起来，并在整个网络中进行联合优化**
作者提出了 **MixFormer**，一种完全统一的 Transformer 风格大规模推荐模型（Large Recommender Model）。它使用一组共享参数，同时建模序列行为与稠密特征交互

**用户—物品解耦策略**，利用**请求级批处理（request-level batching）**技术来复用计算，从而显著提升计算效率

非序列特征作为整个网络的query输入；将 embedding 空间划分为多个 head


### MixFormer block

三个模块：**Query Mixer、Cross Attention 和 Output Fusion**

##### Query Mixer
替代自注意力
与多头自注意力模块类似，Query Mixer 能够在不同 head 之间进行信息交换，但不依赖于基于相似度的注意力机制，因此更适合在严格效率约束下进行异构特征建模

HeadMixing:
pre-head FFN:HeadMixing 做完以后，还会接一个每个 head 独立的 FFN;**每个 head 的 FFN 不是共享的，而是各自独立。**

##### Cross Attention
Query Mixer 输出的 N 个 head 会被直接视为交叉注意力中的 N 个 head，其中每个 head 都充当一个语义上专门化的子 query，关注非序列特征的某个特定子空间


##### 输出融合

**把“非序列特征”和“从序列里聚合来的行为信息”做最后一轮深层融合**

**按 head 独立的、带 SwiGLU 激活的前馈网络（per-head SwiGLUFFN）**

作者认为，不同的head代表的是不同的特征子空间，所以这里使用per-head FFN，而不是强行共用一个FFN，不然不同的信息可能会互相干扰


Query Mixer：处理非序列特征之间的关系
Cross Attention：再用这些非序列特征去行为序列里取相关信息
Output-fusion：按每个head自己的方式融合这两类特征

![[Pasted image 20260316143457.png]]

请求级批处理（RLB）
