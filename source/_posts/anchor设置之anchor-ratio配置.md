---
title: anchor设置之anchor ratio配置
date: 2021-03-22 18:05:50
tags: 
    - 数据可视化
    - 参数调节
---

## reference:
- [目标检测数据可视化，分析anchor_ratio的设置问题](https://zhuanlan.zhihu.com/p/108885033)
---
1. 使用脚本对coco格式的json进行数据分析

   ```python
   import pandas as pd
   import seaborn as sns
   import numpy as np
   import json
   import matplotlib.pyplot as plt
   plt.rcParams['font.sans-serif'] = ['SimHei']
   plt.rcParams['font.family']='sans-serif'
   plt.rcParams['figure.figsize'] = (10.0, 10.0)
   
   
   # 读取数据
   ann_json = '/home/ubuntu/shareDisk/Datasets/PANDA/dataset/COCO_FORMAT/vehicle_train_val.json'
   with open(ann_json) as f:
       ann=json.load(f)
   
   #################################################################################################
   #创建类别标签字典
   category_dic=dict([(i['id'],i['name']) for i in ann['categories']])
   counts_label=dict([(i['name'],0) for i in ann['categories']])
   for i in ann['annotations']:
       counts_label[category_dic[i['category_id']]]+=1
   
   # 标注长宽高比例
   box_w = []
   box_h = []
   box_wh = []
   categorys_wh = [[] for j in range(10)]
   for a in ann['annotations']:
       if a['category_id'] != 0:
           box_w.append(round(a['bbox'][2],2))
           box_h.append(round(a['bbox'][3],2))
           wh = round(a['bbox'][2]/a['bbox'][3],0)
           if wh <1 :
               wh = round(a['bbox'][3]/a['bbox'][2],0)
           box_wh.append(wh)
   
           categorys_wh[a['category_id']-1].append(wh)
   
   
   # 所有标签的长宽高比例
   box_wh_unique = list(set(box_wh))
   box_wh_count=[box_wh.count(i) for i in box_wh_unique]
   
   # 绘图
   wh_df = pd.DataFrame(box_wh_count,index=box_wh_unique,columns=['宽高比数量'])
   wh_df.plot(kind='bar',color="#55aacc")
   plt.show()
   ```

2. 结果如下，例如对faster rcnn的配置文件```configs/_base_/models/faster_rcnn_r50_fpn.py```进行设置，```ratios=[0.5, 1.0, 2.0],```就可以设置成```ratios=[0.1, 0.25, 0.5, 1.0, 2.0, 4.0, 10.0],```

   - 说明

     - 这里的配置参数都是成对出现的，例如(0.1,10),(0.25,4.0)，两个相乘为1。

       > 选取时得保证一个原则：不能选择极端比例。意思就是，不是有什么比例就选择什么比例，而是用一个近似比例代替其他的比例。就好像3.0可以近似的看做2.0，4.0、6.0、7.0可以近似的看为5.0，后面许多大的可以近似的看为10.0。

   ![结果图像](/img/article/result_anchor_ratio.png)