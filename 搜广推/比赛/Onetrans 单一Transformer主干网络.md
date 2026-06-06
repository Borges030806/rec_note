
![[Pasted image 20260309184541.png]]

![[Pasted image 20260309184708.png]]

OneTrans block 采用了类似 HiFormer 的混合参数化方式
所有序列 token（来自序列特征）共享同一组 Q/K/V 和 FFN 参数，而每个非序列 token（来自非序列特征）则拥有其专属参数，以保留其独特语义。不同于传统的“先编码后交互”框架，OneTrans 通过统一的因果 Transformer 主干消除了序列特征与非序列特征之间的架构壁垒。

所有 **S-tokens 共享一套参数**
每个 **NS-token 有自己专属的参数**

注意力采用标准的**因果 mask**，并且 NS-tokens 被放在 S-tokens 之后

S侧：每个S-token只能关注它之前的S-token
NS侧：每个NS-token都可以关注完整的S-token历史；同时，它还可以关注它之前的NS-tokens
支持金字塔结构：无论在 S 侧还是 NS 侧，因果 mask 都会逐步将信息集中到后部位置，这天然适合后续介绍的金字塔式调度策略，即逐层裁剪 token

加了因果 mask 以后，**第** i **个位置只能关注它前面的 token，不能关注后面的 token**

因果mask会使信息逐渐向后部位置集中，即“越靠后越聚合信息”，可以采用一种金字塔式调度策略：
在每一层 OneTrans block 中，只有最近的一部分 S-tokens 会发出 query，而 key 和 value 仍然基于完整序列计算；并且随着网络层数加深，参与发出 query 的 token 数量会逐渐减少。
