
默认配置(run.sh)：NS tokenizer 用 rankmixer，user_ns_tokens=5，item_ns_tokens=2，num_queries=2
其余默认参数(train.py)：d_model=64，num_hyformer_blocks=2，num_heads=4，seq_encoder_type=transformer，rank_mixer_mode=full，use_rope=False

前向过程：model_input包含 user/item 稀疏特征、dense 特征、多路序列和长度/time_bucket
非序列特征->NS tokens     序列特征->序列tokens
NS + 各序列池化信息生成query tokens
经过多层hyformer block迭代更新
拼接所有query后做输出投影和分类头，得到CVR logit

NS tokenizer：非序列特征编码；默认是RankMixerNSTokenizer：先按组做 embedding，再拼成大向量，切块后投影为固定数量 NS token。
序列特征编码：


sparse prams 和 dense prams进行显示分离？如何干预不同参数空间的收敛节奏
1. 梯度裁剪的角度是否可行，现在默认所有的优化器都是统一进行梯度裁剪


序列侧，transformer块内部，ffn-》pffn-〉kunlun优化的pffn



分组语义化+**把固定 RankMixer 替换成 UniMixing-Lite 风格的可学习 mixer**