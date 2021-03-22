---
title: MMDET加入数据增强
date: 2021-03-22 18:10:10
tags: 
    - 数据增强
    - MMDET
---

## 长期更新。。。（ing）

---

## Ablu数据增强

## reference:

- [MMDetection自带数据增强](https://linzhenyuyuchen.github.io/2020/02/14/MMDetection-%E6%95%B0%E6%8D%AE%E5%A2%9E%E5%BC%BA/)
- [目标检测tricks：Ablu数据库增强](https://zhuanlan.zhihu.com/p/125036517)

### 说明

- 若MMDET版本太靠前，需要自己修改```/mmdet/datasets/pipelines/transforms.py```和```/mmdet/datasets/pipelines/__init__.py```文件，并重新```python setup.py develop```进行编译，具体见参考文献1。

- MMDET2.9、2.10版本（或许更靠前）都已经写好了Ablu数据增强，直接修改配置文件就行，不需要编译。

  在```configs/cascade_rcnn/cascade_rcnn_r50_fpn_1x_coco.py```的文件下添加Albu参数，注释内的可以自行添加。

```python
_base_ = [
    './_base_/models/cascade_rcnn_r50_fpn.py',
    './_base_/datasets/coco_detection.py',
    './_base_/schedules/schedule_1x.py', './_base_/default_runtime.py'
]
# add Albu
albu_train_transforms = [
    # dict(
    #     type='HorizontalFlip',
    #     p=0.5),
    # dict(
    #     type='VerticalFlip',
    #     p=0.5),

    dict(
        type='ShiftScaleRotate',
        shift_limit=0.0625,
        scale_limit=0.0,
        rotate_limit=180,
        interpolation=1,
        p=0.5),
    # dict(
    #     type='RandomBrightnessContrast',
    #     brightness_limit=[0.1, 0.3],
    #     contrast_limit=[0.1, 0.3],
    #     p=0.2),
    # dict(
    #     type='OneOf',
    #     transforms=[
    #         dict(
    #             type='RGBShift',
    #             r_shift_limit=10,
    #             g_shift_limit=10,
    #             b_shift_limit=10,
    #             p=1.0),
    #         dict(
    #             type='HueSaturationValue',
    #             hue_shift_limit=20,
    #             sat_shift_limit=30,
    #             val_shift_limit=20,
    #             p=1.0)
    #     ],
    #     p=0.1),
    # # dict(type='JpegCompression', quality_lower=85, quality_upper=95, p=0.2),
    #
    # dict(type='ChannelShuffle', p=0.1),
    # dict(
    #     type='OneOf',
    #     transforms=[
    #         dict(type='Blur', blur_limit=3, p=1.0),
    #         dict(type='MedianBlur', blur_limit=3, p=1.0)
    #     ],
    #     p=0.1),
]
img_norm_cfg = dict(
    mean=[123.675, 116.28, 103.53], std=[58.395, 57.12, 57.375], to_rgb=True)
train_pipeline = [
    dict(type='LoadImageFromFile'),
    dict(type='LoadAnnotations', with_bbox=True),
    dict(type='Resize', img_scale=[(4096, 800), (4096, 1200)], keep_ratio=True),
    dict(type='RandomFlip', flip_ratio=0.5),

    dict(type='Pad', size_divisor=32),
    dict(
        type='Albu',
        transforms=albu_train_transforms,
        bbox_params=dict(
            type='BboxParams',
            format='pascal_voc',
            label_fields=['gt_labels'],
            min_visibility=0.0,
            filter_lost_elements=True),
        keymap={
            'img': 'image',
            'gt_bboxes': 'bboxes'
        },
        update_pad_shape=False,
        skip_img_without_anno=True),
    dict(type='Normalize', **img_norm_cfg),
    dict(type='DefaultFormatBundle'),
    dict(
        type='Collect',
        keys=['img', 'gt_bboxes', 'gt_labels'],
        meta_keys=('filename', 'ori_shape', 'img_shape', 'img_norm_cfg',
                   'pad_shape', 'scale_factor')
    )
]
```