---
title: clone github使用国内镜像加速
date: 2021-03-17 22:34:06
tags: 一些小tips
---

## GitHub clone加速

[参考:Github国内mirror加速](https://blog.csdn.net/networken/article/details/105122778)

```bash
# 这是我们要clone的
git clone https://github.com/Hackergeek/architecture-samples
# 使用镜像[用不了了]
git clone https://github.com.cnpmjs.org/open-mmlab/mmdetection.git
git clone https://github.com.cnpmjs.org/Hackergeek/architecture-sample
# 或者 使用镜像
git clone https://git.sdut.me/Hackergeek/architecture-samples
# 或者 使用镜像[目前可能]
git clone https://hub.fastgit.org/open-mmlab/mmdetection.git
#或者
git clone https://gitclone.com/github.com/kubernetes/kubernetes.git
```

## release下载加速
```bash
#原地址
wget https://github.com/goharbor/harbor/releases/download/v2.0.2/harbor-offline-installer-v2.0.2.tgz
#改为
wget https://hub.fastgit.org/goharbor/harbor/releases/download/v2.0.2/harbor-offline-installer-v2.0.2.tgz

```
