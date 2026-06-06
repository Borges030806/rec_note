**环境：**
joblib==1.3.2
numpy==1.26.4
scikit_learn==1.3.2
setproctitle==1.3.3
timm==0.9.2
torch==2.0.0+cu117



watch -n 1 nvidia-smi

预训练 + 指令微调

没有MAPE（已添加）

**traffic** 已完成 6-6      batchsize 64   few_ratio 1

>   --file_load_path ./experiments/Pretrain_Dataset_TrafficDC_Task_short_FewRatio_1.0/model_save/model_best \
>   --his_len 6 --pred_len 6 --t_patch_size 2 --patch_size 1 \
>   --batch_size_1 128 --lr 3e-4 --weight_decay 1e-6 --few_ratio 1.0 \
>   --process_name unist_trafficdc_prompt > ../train_trafficdc_prompt.log 2>&1 &
[1] 3947



pgrep -fl unist_trafficdc 查看进程
pkill -f unist_trafficdc 杀进程
        


**LEU**      6-6   batchsize 128  few_ratio 1  lr: 6e-4   忽略MAPE 因为数据中有很多0  已完成


**MSPG**  进行中

**PTF**  已完成

BjTT 进行中   6-6 batchsize 16
