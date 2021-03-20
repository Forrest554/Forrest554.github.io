---
title: 使用dpkg安装teamviewer后无法打开解决方法
date: 2021-03-20 19:32:59
tags: Teamviewer
---



- dpkg安装teamviewer

  ```bash
  dkpg -i teamviewer_15.2.2756_amd64.deb
  ```

- 会遇到打不开的情况，详细观察会发现在安装时就告诉缺少相关依赖

  ```bash
  sudo apt-get install -f
  ```

  