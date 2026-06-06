用rope可能会降（默认没有使用rope）

rankmixer应该会优于group；现在默认就是rankmixer



损失函数：默认bce


优化器：默认稀疏参数用adagrad，稠密参数AdamW


1. 优化器修改，线性权重用muon，其他用adamW
2. 损失函数bce->focal

平均池化？

### muon的调参：
降低muon的动量系数到0.9

![[Pasted image 20260430145755.png]]

尝试换moolight变体？



分离ue（正在试，由于d_model的限制，所以把d_model从64提升到68）

12维时间特征（序列侧？）


