
只需要一个脚本，在进行测试推理的时候指标的计算经过这个脚本，那么最终结果就可以经过横向对比，训练过程的指标无需强行统一。


作为论文的对比实验，只在测试集用统一评估协议（MAE/RMSE/MAPE）重算指标，是学术上常见且可接受的做法。

为确保规范与可复现，建议在文中明确：

- 统一评估协议：逆标准化、掩码与阈值 eps、MAPE 是否百分数、按步长与整体统计口径。

- 检查点选择：说明使用各方法原训练流程得到的“最佳权重”，并在统一协议下评测；可在附录补充“用统一指标在验证集重选最佳 epoch”的结果。

- 数据与设置：数据集划分（官方/固定划分）、随机种子、运行环境；报告均值±标准差（多种子）。

- 透明性：主文给统一指标表，附录保留各仓库原口径指标；开源评估脚本与配置。




HimNet 五个数据集训练参数
- LEU（`HimNet-LEU-2025-09-27-21-03-14.log`）
  - num_nodes=16, in_steps=144, out_steps=48
  - loss=MaskedMAELoss, lr=1e-3, weight_decay=5e-4, eps=1e-3
  - milestones=[30,50], clip_grad=5, batch_size=128, max_epochs=200, early_stop=20
  - model_args: hidden_dim=64, num_layers=1, cheb_k=2, ycov_dim=2, tod_emb=8, dow_emb=8, node_emb=16, st_emb=16, tf_decay_steps=6000, teacher_forcing=True
  - seed=4920

- PTF（`HimNet-PTF-2025-09-27-16-31-01.log`）
  - num_nodes=32, in_steps=48, out_steps=24
  - 同上优化器设置；batch_size=64
  - model_args 同 LEU；tf_decay_steps=6000, teacher_forcing=True
  - seed=1806

- MSPG（`HimNet-MSPG-2025-09-28-09-31-03.log`）
  - num_nodes=27, in_steps=192, out_steps=96
  - 同上优化器设置；batch_size=64
  - model_args 同 LEU；tf_decay_steps=6000, teacher_forcing=True
  - seed=3429

- Traffic_hourly（`HimNet-TRAFFIC_HOURLY-2025-09-26-19-36-10.log`）
  - num_nodes=61, in_steps=12, out_steps=12
  - loss=MaskedMAELoss, lr=1e-3, weight_decay=5e-4, eps=1e-3
  - milestones=[25,45], clip_grad=5, batch_size=64, max_epochs=200, early_stop=20
  - model_args: hidden_dim=96, cheb_k=3，其余同上；tf_decay_steps=6000, teacher_forcing=True
  - seed=2882

- BjTT（`HimNet-BJTT-2025-10-01-11-28-03.log`）
  - num_nodes=1296, in_steps=6, out_steps=6
  - loss=MaskedMAELoss, lr=1e-3, weight_decay=5e-4, eps=1e-3
  - milestones=[30,50], clip_grad=5, batch_size=16, max_epochs=200, early_stop=20
  - model_args: hidden_dim=64, num_layers=1, cheb_k=2, ycov_dim=2, tod_emb=8, dow_emb=8, node_emb=16, st_emb=16, tf_decay_steps=6000, teacher_forcing=True
  - seed=2062

UniST 五个数据集训练参数（从代码默认与日志语义还原）
- 统一默认（见 `src/main.py`）
  - task='short', his_len=6, pred_len=6, few_ratio=1.0
  - size='middle' → embed_dim=128, depth=6, decoder_depth=4, num_heads=8
  - patch_size=2, t_patch_size=2, pos_emb='SinCos', no_qkv_bias=False
  - lr=1e-3, min_lr=1e-5, weight_decay=1e-6, batch_size=256（日志文件名如 bs128/bs64 可能表示你当时覆盖了该值）
  - early_stop=5, total_epoches=200, clip_grad=0.05, lr_anneal_steps=200
  - 预训练阶段（prompt_ST=0）：mask_strategy_random='batch'，mask_strategy='random'，mask_ratio=0.5
  - Prompt 阶段（prompt_ST=1）：mask_strategy='temporal'，mask_ratio=pred_len/(his_len+pred_len)

- LEU_DC（`train_leu_bs128.log` 预训练；`train_leu_prompt.log` Prompt）
  - 预训练：按默认（或你命令行设定的 batch_size=128）；prompt 阶段：prompt_ST=1，mask_strategy='temporal'，其余同默认
- PTF_DC（`train_ptf_bs128.log`）
  - 预训练：同默认；batch_size≈你命名中的 128
- MSPG_DC（`train_mspg_bs128.log`）
  - 预训练：同默认；batch_size≈128
- BjTT_DC（`train_bjtt_bs32.log` 与 `train_bjtt_prompt.log`）
  - 预训练：同默认；batch_size≈32（由文件名推断）
  - Prompt：prompt_ST=1，mask_strategy='temporal'
- TrafficDC（`train_trafficdc_bs64.log`）
  - 预训练：同默认；batch_size≈64（由文件名推断）


LEU\PTF\MSPG\Traffic已经统一；
MSPG： UniST 窗口10-2 HimNet6-6 需要重新跑
UniST跑统一脚本会出现参数不匹配问题，正在重新跑
BjTT正在重新跑



MTGNN环境已经配置好，项目代码已经导入，数据集已经生成npz 6-6，测试脚本已经对接统一测试脚本，等待训练
