
单值离散特征，embedding后投影
数组离散特征 ID列表 -> 多个Embedding -> 平均池化 -> Linear(D)
数组离散特征是怎么token化的，先把数组中每个元素进行embedding，然后对这个embedding数组进行平均池化，之后投影？

连续特征直接做线性映射

时间戳特征是连续值，直接做线性投影

投影后得到统一token，接下来构建attention mask

attention mask（不可学习，可缓存）：
对非序列特征，彼此之间双向可见
对序列特征，使用因果mask，而且，附带窗口约束，意思是说，对于序列特征的每个位置，会看这条序列特征的前缀部分以及该位置向前的临近部分（global window和local window，参考HSTU2.0）
special token（intrest token/target token）全部可见


经典attention - > SiLuAttention 不使用softmax，而是用SiLu，不做归一化，而是保留次数的意义，且拥有一个门控分支


MoT（**Mixture of Transducers**）：把异构行为信号拆成多条序列，各自用不同 transducer 编码，再做融合，可以产出一个global摘要，作为额外特征



对线性层权重来说，muon更合适；其他的可以用AdamW；也就是用一个混合的优化器





EnhancedSiLUAttention：在SiLUAttention基础上使用 **RoPE** 和AttMatch
在得到qkvu四段之后，会对q和k使用RoPE编码，参考Kunlun的预处理；然后计算注意力分数，随后，对这个注意力分数施加conversion偏置（通AttMatch），之后和SiLUAttention一致。

**RoPE 就是让不同位置的 token 带着不同“角度”进入 attention，这样模型就知道谁前谁后。**

ATTMatch：根据历史序列特征来判断哪些位置发生了转化事件，从而 获得一个mask，这个mask决定哪些位置要加上这个偏置，然后ATTMatch会给这些位置的注意力分数加上一个可学习的偏置




### 模型

把堆叠的一堆block区分成了全序列层和部分序列层，也就是使用全部token的部分和使用部分token的部分，为了减少开销的同时保证模型的效果