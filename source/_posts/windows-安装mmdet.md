---
title: windows 安装mmdet
date: 2021-04-01 16:33:23
tags:
      - MMDET 
      - 实验室文档
---
由于甲方的项目必须在Windows上运行，但是mmdet对window支持不太友好，所以写了一个文档
---
## 参考

- [windows10安装cuda10.2](https://blog.csdn.net/bingo_liu/article/details/103224730)
- [WINDOWS 下 MMCV | MMCV-full 的安装](https://zhuanlan.zhihu.com/p/308281195)
---
## 202106更新
mmcv(1.2.4)可以直接使用预编译包，通过pip安装，跳过二三四步，直接编译mmdet(2.10)

mmcv(1.2.7)和mmdet(2.13)也可以

```bash
pip install mmcv-full==1.2.4 -f https://download.openmmlab.com/mmcv/dist/cu110/torch1.7.0/index.html
```

## 依赖安装


[百度网盘](https://pan.baidu.com/s/13rnuoTin-FydurQlzf4y2g)    

密码：fctv

依赖如图所示：

![依赖](/img/article/依赖.PNG)

### 一、cuda和cudnn安装

1. 安装cuda11

   - 双击后运行，选择ok
   ![启动界面](/img/article/p1.PNG)

   - 进入自定义安装

   - 在CUDA里取消VIsual Studio Intergration的选择

     至于驱动，NCIDIA GeForce，可以先桌面右键打开NVIDIA控制面板，帮助->系统信息->组件，查看显卡驱动版本。
     ![驱动信息](/img/article/p2.PNG)

     > 如果本机的驱动版本(当前版本)小于cuda对应的版本（新版本），则选择，否则不选。如果当前版本小于新版本，并且不覆盖安装，之后电脑会频繁蓝屏或死机

2. 安装cudnn8.1

   - 解压文件后，将对应的文件拷贝到cuda的安装目录```C:\Program Files\NVIDIA GPU Computing Toolkit\CUDA\v11.0```

3. 在终端输入```nvcc -V```查看是否有输出

### 二、vs_BuildTools安装

- 注：选择2019的16.8.6版本，16.9版本会出现问题，选择C++生成工具即可
- 为cl添加环境变量，找到安装路径，并添加到PATH。例如添加 `C:\Program Files (x86)\Microsoft Visual Studio\2019\BUildTools\VC\Tools\MSVC\14.27.29110\bin\Hostx86\x64` 到 `PATH`

![BUildTools界面](/img/article/build.PNG)

### 三、torch和torchvision安装

 1. conda创建新环境

    ```bash
    conda create --n mmdet python=3.7
    ```

2. pip install离线安装两个包

### 四、mmcv的编译

1. git克隆并进入mmcv

   ```bash
   git clone https://github.com/open-mmlab/mmcv.git  # 可以用国内源加速
   git checkout v1.2.7  # 更换为1.2.7版本
   cd mmcv
   pip install -r requirements.txt  #安装所需 Python 依赖包
   ```

2. 修改torch源代码防止utf-8的问题

   在conda安装环境```envs\mmdet\Lib\site-packages\torch\utils\cpp_extension.py```将所有decode替换成decode('utf-8','ignore')

2. 添加环境变量并本地编译
   注：使用powershell，若没有conda actite可以在里面输入conda init powershell

   ```bash
   $env:MMCV_WITH_OPS = 1
   $env:MAX_JOBS = 8 # 根据你可用CPU以及内存量进行设置
   $env:CUDA_HOME = $env:CUDA_PATH_V11_0
   $env:TORCH_CUDA_ARCH_LIST="6.1" # 设置算力 GTX 1080为6.1
   ```

   - 也可以直接用```pip install mmcv-full -f https://download.openmmlab.com/mmcv/dist/cu110/torch1.7.0/index.html ```

### 五、mmdetection的安装

- 同之前linux系统一样，选择2.10版本