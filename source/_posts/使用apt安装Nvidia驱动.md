---
title: 使用apt安装Nvidia驱动
date: 2021-03-17 22:34:58
tags: 一些小tips
---

## 命令行安装驱动

```bash
# 查看显卡信息
ubuntu-drivers devices
# 安装驱动
sudo ubuntu-drivers autoinstall # 自动
sudo apt install nvidia-340 # 根据信息指定版本
```
