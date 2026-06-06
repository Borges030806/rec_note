**环境：**
pytorch>=1.12
numpy
pandas
matplotlib
pyyaml
torchinfo

原数据集已经跑通（配置完成）

**模型当前仅使用三个输入通道**


**fnf 数据集**：     traffic（单特征）✅       
			weather/electricity（电力需求）（原来为多特征，只保留其一）✅    
			exchange(纯时间)    
			bitcoin(纯时间）)

**chatTime 数据集**： CGTSF （有三个子数据集：MSPG、LEU、PTF）均为时序+文本 多模态数据集
每一天的数据列有hist、pred、text，hist和pred是两个窗口数据，pred里面是当天的若干次采样（例如MSPG是半小时采样一次，PTF是一小时采样一次） 需要做原始时间序列恢复 没有空间信息（数据本身时间上是连续的，每一天的pred是第二天的hist的末尾数据）


**按照日期顺序排列，每一个样本保留pred和text即可；记得补充第一天的hist（加五个日期即可）**
**（做全日预测即可）**
源数据集是做零样本预测，没有划分数据集
1. 舍去text；
2. 去除Hist列，只保留Pred列。在此之前，每个数据集第一个样本的

**BjTT数据集**： 等回复
