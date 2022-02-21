---
title: 在lxc容器中使用docker遇到的问题
date: 2021-04-15 10:46:17
tags:
      - LXD 
      - Docker
---

### 运行hello-world镜像出错

```bash
$ sudo docker run hello-world

Unable to find image 'hello-world:latest' locally
latest: Pulling from library/hello-world

c04b14da8d14: Extracting    974 B/974 B
docker: failed to register layer: ApplyLayer exit status 1 stdout:  stderr: permission denied.
See 'docker run --help'.
```

解决方法：在宿主机配置如下命令

```bash
lxc config set your-lxc-name security.nesting true
lxc config set your-lxc-name security.privileged true
```


### 添加GPU调用出错

```bash
$ curl -s -L https://nvidia.github.io/nvidia-docker/gpgkey | sudo apt-key add -

gpg: no valid OpenPGP data found
```

解决方法：配置host

```bash
# 编辑host文件
sudo vim /etc/hosts
# 在文件末尾添加如下内容
185.199.108.153 nvidia.github.io
185.199.109.153 nvidia.github.io
185.199.110.153 nvidia.github.io
185.199.111.153 nvidia.github.io
```
