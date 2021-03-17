---
title: cuda多版本的切换
date: 2021-03-17 22:33:05
tags: 一些小tips
---

## cuda版本切换

```
sudo rm -rf /usr/local/cuda  #删除之前生成的软链接
sudo ln -s /usr/local/cuda-10.0 /usr/local/cuda #生成新的软链接
nvcc -V #查看当前cuda版本
```
