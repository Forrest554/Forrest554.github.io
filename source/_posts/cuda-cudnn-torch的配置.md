---
title: cuda cudnn torch的配置
date: 2021-03-17 14:21:25
tags: MMDET安装
---
1. 安装cuda

- 安装

  ```bash
   sudo sh cuda_10.0.130_410.48_linux.run
  # 按z快进
  # 选项
  Do you accept the previously read EULA?
  accept/decline/quit: accept
  Install NVIDIA Accelerated Graphics Driver for Linux-x86_64 410.48?
  (y)es/(n)o/(q)uit: n
  
  Install the CUDA 10.0 Toolkit?
  (y)es/(n)o/(q)uit: y
  
  Enter Toolkit Location
   [ default is /usr/local/cuda-10.0 ]:
  
  Do you want to install a symbolic link at /usr/local/cuda?
  (y)es/(n)o/(q)uit: y
  
  Install the CUDA 10.0 Samples?
  (y)es/(n)o/(q)uit: y
  
  Enter CUDA Samples Location
   [ default is /home/hycx ]:
  ```

  

- 添加环境变量

  ```bash
  sudo vim ~/.bashrc
  # 在最下面插入
  # cuda env
  export CUDA_HOME=/usr/local/cuda
  export PATH=$PATH:$CUDA_HOME/bin
  export LD_LIBRARY_PATH=/usr/local/cuda/lib64${LD_LIBRARY_PATH:+:${LD_LIBRARY_PATH}}
  
  # 保存并激活环境
  source ~/.bashrc
  
  # 检查是否安装成功
  nvcc -V
  # 输出
  nvcc: NVIDIA (R) Cuda compiler driver
  Copyright (c) 2005-2018 NVIDIA Corporation
  Built on Sat_Aug_25_21:08:01_CDT_2018
  Cuda compilation tools, release 10.0, V10.0.130
  ```

2. 安装cudnn

   - 解压cudnn

     ```bash
     tar zxvf cudnn-10.0-linux-x64-v7.6.5.32.tgz
     ```

   - 将文件放入cuda文件夹

     ```bash
     sudo cp cuda/include/cudnn.h /usr/local/cuda/include/ 
     sudo cp cuda/lib64/libcudnn* /usr/local/cuda/lib64/ 
     sudo chmod a+r /usr/local/cuda/include/cudnn.h 
     sudo chmod a+r /usr/local/cuda/lib64/libcudnn*
     ```
   
   - 查看cudnn版本
   
     ```bash
     cat /usr/local/cuda/include/cudnn.h | grep CUDNN_MAJOR -A 2
     # 出现以下信息
     
     
     #define CUDNN_MAJOR 7
     #define CUDNN_MINOR 6
     #define CUDNN_PATCHLEVEL 5
     --
     #define CUDNN_VERSION (CUDNN_MAJOR * 1000 + CUDNN_MINOR * 100 + CUDNN_PATCHLEVEL)
     
     #include "driver_types.h"
     ```
   
     

3. 安装conda

   - 安装

     ```bash
     sh ./Miniconda3-latest-Linux-x86_64.sh
     # init选择yes
     by running conda init? [yes|no]
     [no] >>> yes
     # 重启终端查看是否安装成功
     conda env list
     ```

   - 换源（参考[清华源](https://mirrors.tuna.tsinghua.edu.cn/help/anaconda/)）

     ```bash
     # 生成config文件
     conda config --set show_channel_urls yes
     # 修改 .condarc文件
     vim ~/.condarc
     # 内容修改为以下内容
     channels:
       - defaults
     show_channel_urls: true
     default_channels:
       - https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/main
       - https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/r
       - https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/msys2
     custom_channels:
       conda-forge: https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud
       msys2: https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud
       bioconda: https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud
       menpo: https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud
       pytorch: https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud
       simpleitk: https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud
       # 清除索引缓存，保证用的是镜像站提供的索引
       conda clean -i 
     ```

   - 创建新环境

     ```bash
     # 创建torch的python 3.7环境
     conda create -n torch_1.4 python==3.7
     # 激活环境
     ```

   - 更改pip源为豆瓣源

     ```bash
     # 升级 pip 到最新的版本 (>=10.0.0) 后进行配置：
     pip install pip -U
     pip config set global.index-url https://pypi.doubanio.com/simple
     ```

4. 安装torch和torchvision

   ```bash
   # 离线下载后本地安装
   pip install torch-1.4.0+cu100-cp37-cp37m-linux_x86_64.whl
   pip install torchvision-0.5.0+cu100-cp37-cp37m-linux_x86_64.whl
   # 检查是否成功
   (torch_1.4) hycx@hycx:~/下载$ python
   Python 3.7.0 (default, Oct  9 2018, 10:31:47)
   [GCC 7.3.0] :: Anaconda, Inc. on linux
   Type "help", "copyright", "credits" or "license" for more information.
   >>> import torch
   >>> torch.cuda.is_available()
   True
   >>> exit()
   ```

5. 克隆一个环境到mmdet

   ```bash
   conda create -n mmdet_2.6 --clone torch_1.4
   ```
