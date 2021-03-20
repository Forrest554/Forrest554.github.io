---
title: yolo V1 损失函数的理解
date: 2021-03-20 19:38:12
math: true
tags: 
    - 目标检测
    - YOLO
---

## YOLO V1

### 损失函数的理解

![yolo_loss_fun](/img/article/yolo_lossfun.png)

1. 每一项说明
   - 第一项：遍历每个网格（Grid）中的每一个边界框（Bounding BOX，BBOX），其中网格有$S^2$个，预测框有B个。
   - $\mathbb{I}^{obj}_{ij}$ 表示若第i个网格中第j个边界框含有对象（OBJ）则该值为1 ，否则为0。
   - $[(x_i-\bar{x}_i)^2+(y_i-\bar{y}_i)^2]$表示中心坐标的平方差损失。
   - 第二项：用开方的原因是对于大物体和小物体来说，在相同的IOU情况下，不至于相差太大。
   - 第三项和第四项都是分别是对含有OBJ和不含有OBJ的BBOX的置信度（confidence）的预测 
   - 第四项：$\mathbb{I}^{obj}_{i}$表示判断是都有OBJ落在网格i中。进行类别预测。
2. 超参数说明
   - $\lambda_{coord}$表示需要BBOX负责预测OBJ的超参数，数目较多，设置5
   - $\lambda_{noobj}$表示不负责预测OBJ的BBOX的超参数，数目较少，设置0.5
   - 用来抵输入的样本的不平衡
