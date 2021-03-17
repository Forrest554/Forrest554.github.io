---
title: MMDET的安装和使用
date: 2021-03-17 14:25:39
tags: 
      - MMDET安装 
      - 实验室文档
---

### 一、链接

#### [GIthub地址](https://github.com/open-mmlab/mmdetection)

####  [文档地址]( https://mmdetection.readthedocs.io/)

---

### 二、安装

#### 环境依赖

- Linux 或者macOS系统

- Python 3.6+

- PyTorch 1.3+

- GCC 5+

- mmcv（商汤的一个cv库）

#### 开始安装

- 1. 创建conda环境并激活

     ```bash
     conda create -n open-mmlab python=3.7 -y
     conda activate open-mmlab
     ```

- 2. 安装PyTorch和Torchvision，参考[pytorch官网](https://pytorch.org/get-started/locally/)和cuda版本安装，示例:

  - 方法一
    conda 或者 pip 在线安装

    ```bash
    conda install pytorch torchvision cudatoolkit=10.1 -c pytorch
    ```

  - 方法二
    通过[torch 离线镜像网址](https://download.pytorch.org/whl/torch_stable.html)下载whl后 pip离线安装

    ```bash
    pip install torchvision-0.8.1+cu110-cp37-cp37m-linux_x86_64.whl
    pip install torch-1.7.0+cu110-cp37-cp37m-linux_x86_64.whl
    ```

- 3. 安装mmcv(1.2)

  - 方法一

    直接安装

    ```
    eg:
    	pip install mmcv-full === 1.0.5
    ```

  - 方法二

    根据pytorch、cuda，见下表：

    <table class="docutils">
      <tbody>
        <tr><th width="80"> CUDA </th><th valign="bottom" align="left" width="100">torch 1.6</th><th valign="bottom" align="left" width="100">torch 1.5</th><th valign="bottom" align="left" width="100">torch 1.4</th><th valign="bottom" align="left" width="100">torch 1.3</th></tr>
        <tr>
          <td align="left">10.2</td>
          <td align="left"><details><summary> install </summary><pre><code>pip install mmcv-full==latest+torch1.6.0+cu102 -f https://openmmlab.oss-accelerate.aliyuncs.com/mmcv/dist/index.html</code></pre> </details> </td>
          <td align="left"><details><summary> install </summary><pre><code>pip install mmcv-full==latest+torch1.5.0+cu102 -f https://openmmlab.oss-accelerate.aliyuncs.com/mmcv/dist/index.html</code></pre> </details> </td>
          <td align="left"> </td>
          <td align="left"> </td>
        </tr>
        <tr>
          <td align="left">10.1</td>
          <td align="left"><details><summary> install </summary><pre><code> pip install mmcv-full==latest+torch1.6.0+cu101 -f https://openmmlab.oss-accelerate.aliyuncs.com/mmcv/dist/index.html</code></pre> </details> </td>
          <td align="left"><details><summary> install </summary><pre><code> pip install mmcv-full==latest+torch1.5.0+cu101 -f https://openmmlab.oss-accelerate.aliyuncs.com/mmcv/dist/index.html</code></pre> </details> </td>
          <td align="left"><details><summary> install </summary><pre><code>pip install mmcv-full==latest+torch1.4.0+cu101 -f https://openmmlab.oss-accelerate.aliyuncs.com/mmcv/dist/index.html</code></pre> </details> </td>
          <td align="left"><details><summary> install </summary><pre><code>pip install mmcv-full==latest+torch1.3.0+cu101 -f https://openmmlab.oss-accelerate.aliyuncs.com/mmcv/dist/index.html</code></pre> </details> </td>
        </tr>
        <tr>
          <td align="left">9.2</td>
          <td align="left"><details><summary> install </summary><pre><code> pip install mmcv-full==latest+torch1.6.0+cu92 -f https://openmmlab.oss-accelerate.aliyuncs.com/mmcv/dist/index.html</code></pre> </details> </td>
          <td align="left"><details><summary> install </summary><pre><code> pip install mmcv-full==latest+torch1.5.0+cu92 -f https://openmmlab.oss-accelerate.aliyuncs.com/mmcv/dist/index.html</code></pre> </details> </td>
          <td align="left"><details><summary> install </summary><pre><code>pip install mmcv-full==latest+torch1.4.0+cu92 -f https://openmmlab.oss-accelerate.aliyuncs.com/mmcv/dist/index.html</code></pre> </details> </td>
          <td align="left"><details><summary> install </summary><pre><code>pip install mmcv-full==latest+torch1.3.0+cu92 -f https://openmmlab.oss-accelerate.aliyuncs.com/mmcv/dist/index.html</code></pre> </details> </td>
        </tr>
        <tr>
          <td align="left">cpu</td>
          <td align="left"><details><summary> install </summary><pre><code> pip install mmcv-full==latest+torch1.6.0+cpu -f https://openmmlab.oss-accelerate.aliyuncs.com/mmcv/dist/index.html</code>
    </pre> </details> </td>
          <td align="left"><details><summary> install </summary><pre><code> pip install mmcv-full==latest+torch1.5.0+cpu -f https://openmmlab.oss-accelerate.aliyuncs.com/mmcv/dist/index.html</code></pre> </details> </td>
          <td align="left"><details><summary> install </summary><pre><code>pip install mmcv-full==latest+torch1.4.0+cpu -f https://openmmlab.oss-accelerate.aliyuncs.com/mmcv/dist/index.html</code></pre> </details> </td>
          <td align="left"><details><summary> install </summary><pre><code>pip install mmcv-full==latest+torch1.3.0+cpu -f https://openmmlab.oss-accelerate.aliyuncs.com/mmcv/dist/index.html</code></pre> </details> </td>
        </tr>
      </tbody>
    </table>


    运行如下指令下载

    ```
    pip install mmcv-full==latest+torch1.6.0+cu101 -f https://openmmlab.oss-accelerate.aliyuncs.com/mmcv/dist/index.html
    ```

    Tips：若不在表中，可尝试魔改命令下载

    ```
    pip install mmcv-full==latest+torch1.4.0+cu100 -f https://openmmlab.oss-accelerate.aliyuncs.com/mmcv/dist/index.html
    pip install mmcv-full -f https://download.openmmlab.com/mmcv/dist/cu110/torch1.7.0/index.html
    pip install mmcv-full==1.2 -f https://download.openmmlab.com/mmcv/dist/cu100/torch1.4.0/index.html
    ```

  - 方法三

    git 下载mmcv(2.6)源码后，通过git checkout命令修改版本，然后编译mmcv

- 4. 在home的用户目录创建MMD父目录并克隆mmdetection存储库

     ```bash
     cd /home/USER
     mkdir MMDET
     git clone https://github.com/open-mmlab/mmdetection.git
     # 换一个镜像比较快
     git clone https://github.com.cnpmjs.org/open-mmlab/mmdetection.git
     cd mmdetection
     # 用git checkout 更换版本（github的tags）,考虑2.6
     git checkout bd3306f
     # 查看是否更换成功
     cat mmdet/version.py
     # 安装
     pip install -r requirements/build.txt
     pip install -v -e .  # 或  python setup.py develop
     ```


- 5. 测试是否安装成功

     可以直接运行demo文件，查看输出是不是报缺少参数的错误

     ```bash
     python ./demo/image_demo.py
     ```

     报错：

     ```
     usage: image_demo.py [-h] [--device DEVICE] [--score-thr SCORE_THR]
                          img config checkpoint
     image_demo.py: error: the following arguments are required: img, config, checkpoint
     ```

     也可以通过下载checkpoint并且指定配置文件，其中checkpoin可以在github的[model zoo](https://github.com/open-mmlab/mmdetection/blob/master/docs/model_zoo.md)里面下载，然后选择对应的模型，例如[faster RCNN](http://download.openmmlab.com/mmdetection/v2.0/faster_rcnn/faster_rcnn_r50_fpn_1x_coco/faster_rcnn_r50_fpn_1x_coco_20200130-047c8118.pth)，下载后保存到本地

     ```python
     from mmdet.apis import init_detector, inference_detector, show_result_pyplot
     import mmcv
     config_file = '../configs/faster_rcnn/faster_rcnn_r50_fpn_1x_coco.py'
     # download the checkpoint from model zoo and put it in `checkpoints/`
     checkpoint_file = '../checkpoints/faster_rcnn_r50_fpn_1x_20181010-3d1b3351.pth'
     # build the model from a config file and a checkpoint file
     model = init_detector(config_file, checkpoint_file, device='cuda:0')
     # test a single image
     img = 'demo.jpg'
     result = inference_detector(model, img)
     # show the results
     show_result_pyplot(model, img, result)
     ```

     

### 三、使用

#### 使用labelme标注数据（待补充）

- 下载后，在终端输入labelme即可使用

#### 转换成COCO数据集

- 使用脚本convert2coco.py将labelme的标准文件转换成coco格式，该脚本接受参数如下所示：

  ```
  python convert2coco.py --lp path/to/labemefile --sp path/to/savepath
  # --lp 后面是labelme标注好的
  # --sp 后面是保存coco格式的数据的地址
  ```

#### 配置文件设置

1. 新建一个task_config文件夹，里面放置配置文件

2. 在task_config文件夹里新建\_base_文件夹，workdir文件夹

3. 在mmdet/configs里面选择相应的模型，例如faster_rcnn，将faster_rcnn_r50_fpn_1x_coco.py复制到task_config文件夹下

4. 在mmdet/configs/\_base_文件夹下，找到datasets/coco_detection.py，default_runtime.py，models/fast_rcnn_r50_fpn.py，schedules/schedule_1x.py。最终目录如下所示

   ```
   ├── _base_
   │   ├── datasets
   │   │   └── coco_detection.py
   │   ├── default_runtime.py
   │   ├── models
   │   │   └── faster_rcnn_r50_fpn.py
   │   └── schedules
   │       └── schedule_1x.py
   ├── faster_rcnn_r50_fpn_1x_coco.py
   └── workdir
   ```

5. 修改faster_rcnn_r50_fpn_1x_coco.py，将所有../改成./

6. 修改coco_detection.py

   - Line2: data_root 修改成训练数据的coco地址

   - 加入一行class种类

     ```
     eg:
     classes = ('normal','unmormal')
     ```

   - 修改data字典，加入classes属性，如下所示

     ```python
     data = dict(
         samples_per_gpu=1,
         workers_per_gpu=1,
         train=dict(
             type=dataset_type,
             classes=classes,
             ann_file=data_root + 'annotations/instances_train2017.json',
             img_prefix=data_root + 'train2017/',
             pipeline=train_pipeline),
         val=dict(
             type=dataset_type,
             classes=classes,
             ann_file=data_root + 'annotations/instances_val2017.json',
             img_prefix=data_root + 'val2017/',
             pipeline=test_pipeline),
         test=dict(
             type=dataset_type,
             classes=classes,
             ann_file=data_root + 'annotations/instances_val2017.json',
             img_prefix=data_root + 'val2017/',
             pipeline=test_pipeline))
     ```

   - data字典的各个路径查看是否匹配

7. 修改faster_rcnn_r50_fpn.py

   - 如果已经有faster RCNN的预训练模型，可以把pretrained='torchvision://resnet50'注释掉

     ```python
     # pretrained='torchvision://resnet50',
     ```

   - 修改46行的num_classes为需要识别的种类数目，不包括背景，一类就是1

8. 修改schedule_1x.py

   - 考虑修改lr和totla_epochs，lr可以考虑修改成0.002

9. 修改default_runtime.py

   - 如果有fater RCNN预训练模型，可以把load_from修改成checkpoint的路径

10. 训练

    - 进入mmdet目录 设置config文件路径 和 workdir路径

      ```bash
      cd mmdet
      python tools/train.py ~/PPAI_test_M/ppai_config/faster_rcnn_r50_fpn_1x_coco.py --work-dir ~/PPAI_test_M/ppai_config/workdir/
      ```

    ```
    
    ```

11. 

#### 训练

#### 推理


