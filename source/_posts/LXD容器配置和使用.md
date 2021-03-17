---
title: LXD容器配置和使用
date: 2021-03-17 22:30:15
tags: 实验室文档
---

## reference:

- [实验室GPU服务器的LXD虚拟化](https://github.com/shenuiuin/LXD_GPU_SERVER/blob/master/README.md)
- [使用 LXD 搭建多人使用的 GPU 服务器](https://xungejiang.com/2019/05/31/lxd-setting/)
- [LXD 2.15 中的存储管理](http://www.voidcn.com/article/p-pqkfttud-bmu.html)
- [Ubuntu上使用ZFS（Zettabyte File System）](https://my.oschina.net/u/2306127/blog/2250662)
- [华元主页](http://172.18.0.30:8888/)

## 配置

### 1. lxd的安装与初始化

- ### 安装lxd

  - 说明

    - LXD 实现虚拟容器
    - ZFS 用于管理物理磁盘，支持LXD高级功能
    - bridge-utils 用于搭建网桥

  - 安装LXD、ZFS和bridge-utils

    ```bash
    sudo apt-get install lxd zfsutils-linux bridge-utils
    ```

  - 配置zfs

    1. 首先，我们运行sudo fdisk -l列出服务器上的可用磁盘和分区，将列表中还有最大存储/dev/nvme0n1p5分出需要使用的空间，作为容器的存储卷

       ```bash
       # 使用dd命令在home目录下创建400G大小的存储池
       sudo dd if=/dev/nvme0n1p8 of=~/lxdImg.img bs=1M count=409600
       # 创建存储池
       sudo zpool create lxd-pool  ~/lxdImg.img
       # 查看存储池
       sudo zpool status
        pool: lxd-pool
        state: ONLINE
         scan: none requested
       config:
       
       	NAME                     STATE     READ WRITE CKSUM
       	lxd-pool                 ONLINE       0     0     0
       	  /home/hycx/lxdImg.img  ONLINE       0     0     0
       
       ```

    2. 创建存储池

       ```bash
       # 配置lxd存储池为该存储池
       sudo lxc storage create zfs-pool zfs source=lxd-pool
       ```

      3. 查看lxd存储池

         ```bash
           sudo lxc storage list
           +----------+-------------+--------+----------+---------+
           |   NAME   | DESCRIPTION | DRIVER |  SOURCE  | USED BY |
           +----------+-------------+--------+----------+---------+
           | zfs-pool |             | zfs    | lxd-pool | 0       |
           +----------+-------------+--------+----------+---------+ 
         ```
         
      4. lxd初始化

         ```bash
         # lxd 初始化
         sudo lxd init
         
         Would you like to use LXD clustering? (yes/no) [default=no]:
         Do you want to configure a new storage pool? (yes/no) [default=yes]: no
         Would you like to connect to a MAAS server? (yes/no) [default=no]:
         Would you like to create a new local network bridge? (yes/no) [default=yes]:
         What should the new bridge be called? [default=lxdbr0]:
         What IPv4 address should be used? (CIDR subnet notation, “auto” or “none”) [default=auto]:
         What IPv6 address should be used? (CIDR subnet notation, “auto” or “none”) [default=auto]:
         Would you like LXD to be available over the network? (yes/no) [default=no]:
         Would you like stale cached images to be updated automatically? (yes/no) [default=yes]
         Would you like a YAML "lxd init" preseed to be printed? (yes/no) [default=no]:
         # lxd 默认配置
         sudo lxc profile edit default
         
           GNU nano 2.9.3                                        /tmp/lxd_editor_658170891.yaml
         
         ### This is a yaml representation of the profile.
         ### Any line starting with a '# will be ignored.
         ###
         ### A profile consists of a set of configuration items followed by a set of
         ### devices.
         ###
         ### An example would look like:
         ### name: onenic
         ### config:
         ###   raw.lxc: lxc.aa_profile=unconfined
         ### devices:
         ###   eth0:
         ###     nictype: bridged
         ###     parent: lxdbr0
         ###     type: nic
         ###
         ### Note that the name is shown but cannot be changed
         
         config: {}
         description: Default LXD profile
         devices:
           eth0:
             name: eth0
             nictype: bridged
             parent: lxdbr0
             type: nic
           root:
             path: /
             pool: zfs-pool
             size: 400GB
             type: disk
         name: default
         used_by: []
         ```

### 2. 建立容器

  - 新建容器

    - 使用清华的镜像源（加速创建）

      ```bash
      sudo lxc remote add tuna-images https://mirrors.tuna.tsinghua.edu.cn/lxc-images/ --protocol=simplestreams --public
      ```

    - 列出可用镜像

      ```bash
      sudo lxc image list tuna-images:
      ```

    - 创建容器

      ```bash
      # 选择ubuntu 18.04版本，版本名是Bionic Beaver，FINGERPRINT为4da26fe8e234
      sudo lxc launch tuna-images:4da26fe8e234 containerOri
      ```

    - 进入容器

      ```bash
      sudo lxc exec containerOri bash
      # 进入的是root账户，在这个容器中已经存在了一个叫ubuntu的用户
      # 修改密码
      passwd root
      passwd ubuntu
      # 安装ssh
      apt install ssh
      ```

      

  - 在宿主机上做端口转发
    
      ```bash
      # 查看本机ip 192.168.3.171
      ifconfig
      # 查看容器ip  10.19.231.69
      sudo lxc list
      # 60601是我们定的端口号，通过宿主机的60601端口号监听容器中22端口号（SSH默认端口号
      sudo lxc config device add test proxy0 proxy listen=tcp:192.168.3.171:60601 connect=tcp:10.19.231.69:22 bind=host
      sudo lxc config device add containerOri proxy0 proxy listen=tcp:192.168.3.171:60601 connect=tcp:10.19.231.69:22 bind=host
      # 删除代理
      sudo lxc config device remove  containerOri proxy1
      # 测试ssh连接容器
      ssh ubuntu@192.168.3.171 -p 60601
      -------- 使用iptables --------- 有问题
      # 开放端口 60601
      sudo iptables -I INPUT -p tcp --dport 60601 -j ACCEPT
      # 做端口转发 其中Hostip为宿主机的ip，Hostport为宿主机的端口号，Containerip为容器的ip
      # iptables –t nat –A PREROUTING –d Hostip –p tcp –dport Hostport –j DNAT –to Containerip:22
    sudo iptables -t nat -A PREROUTING -d 192.168.3.171 -p tcp --dport 60605 -j DNAT --to 10.19.231.192:22
    sudo iptables -t nat -A PREROUTING -d 192.168.3.171 -p tcp --dport 60615 -j DNAT --to 10.19.231.192:3389
    # 保存规则
    sudo sh -c "iptables-save > /etc/iptables.rules"
    ```
    
  - 为容器添加显卡
    
      ```bash
      # 添加所有显卡
      sudo lxc config device add containerOri gpu gpu
      # 添加指定GPU
    sudo lxc config device add yourContainerName gpu0 gpu id=0
    ```

  - 创建共享目录
    
      ```bash
      # 创建共享文件夹
      # 其中 path 为容器路径，source 为宿主机路径。device-name 随意取名字即可。
      sudo lxc config set <container> security.privileged true
      sudo lxc config device add <container> <device-name> disk path=/home/xxx/share source=/home/xxx/share
      # 移除
    sudo lxc config device remove <container> <device-name>
    ```

  - 安装桌面环境、xrdp
    
      ```bash
      sudo apt install --no-install-recommends ubuntu-desktop
      sudo apt-get install xrdp
      # 为xrdp添加端口转发
    sudo lxc config device add containerOri proxy1 proxy listen=tcp:192.168.3.171:60611 connect=tcp:10.19.231.69:3389 bind=host
      
    ```
    

## 使用

1. 复制容器

   ```bash
   sudo lxc cp ljh new
   ```

2. 端口转发

   ```bash
   sudo iptables -t nat -A PREROUTING -d 192.168.3.171 -p tcp --dport 60605 -j DNAT --to 10.19.231.192:22
   sudo iptables -t nat -A PREROUTING -d 192.168.3.171 -p tcp --dport 60615 -j DNAT --to 10.19.231.192:3389
   # 保存规则
   sudo sh -c "iptables-save > /etc/iptables.rules"
   ```

3. 查看规则

   ```bash
   sudo iptables -t nat -nL --line
   ```

4. 删除规则

   ```bash
   sudo iptables -t nat -D PREROUTING 1
   ```

5. 删除容器

   ```bash
   sudo lxc delete container-name
   ```