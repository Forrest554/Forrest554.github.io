---
title: ubuntu安装rabbitMQ
date: 2021-06-15 16:09:47
tags:
---
1. 安装erlang

   ```bash
   sudo apt-get install erlang-nox
   ```

2. 安装Rabbitmq

   ```bash
   sudo apt-get install rabbitmq-server
   ```

3. 添加admin，并赋予administrator权限

   ```bash
   # 添加admin用户，密码设置为admin
   sudo rabbitmqctl add_user  admin  admin  
   
   # 赋予权限
   sudo rabbitmqctl set_user_tags admin administrator
   
   # 赋予virtual host中所有资源的配置、写、读权限以便管理其中的资源
   sudo rabbitmqctl  set_permissions -p / admin '.*' '.*' '.*'
   ```

4. RabbitMQ GUID使用

   安装了Rabbitmq后，默认也安装了该管理工具，执行命令即可启动

   ```bash
   sudo rabbitmq-plugins enable rabbitmq_management
   ```

5. 启动、停止、重启、状态rabbitMq命令

   ```bash
   sudo rabbitmq-server start
   sudo rabbitmq-server stop
   sudo rabbitmq-server restart
   sudo rabbitmqctl status
   ```