---
title: MMDET中logger思考
date: 2021-08-16 17:45:42
tags:
---

## 背景

最近项目对MMDET进行二次开发时，发现在使用mmdet中的logger时候，发现在同一个进程（主进程），指向的logger对象只有一个根logger，也就是说，即使传给函数不用的输出打印位置，最后都只在一个文件里有显示。
而项目中，需要对不同的训练任务，指定不同的位置log。通过阅读mmdet、mmcv、logging源码，做出如下修改。

## 方法

1. 原函数查看对mmdet的```mmdetection/tools/train.py```文件中的```get_root_logger```进行查看，发现最后是```mmcv/utils/logging.py```文件的```get_logger```函数，可以看到原函数通过判断name是否存在，返回相应的logger对象，因为每次训练都是“mmdet”，所以返回的logger都是同一个。因此把这一个判断删除，冰球修改句柄中的File句柄就可以了。

   ```python
   # mmdetection/tools/train.py 训练调用的函数
   from mmdet.utils import get_root_logger
   
   logger = get_root_logger(log_file=log_file, log_level=cfg.log_level)
   ```

   ```python
   # mmdet的 中的get_root_logger原型
   
   from mmcv.utils import get_logger
   
   def get_root_logger(log_file=None, log_level=logging.INFO):
       """Get root logger.
   
       Args:
           log_file (str, optional): File path of log. Defaults to None.
           log_level (int, optional): The level of logger.
               Defaults to logging.INFO.
   
       Returns:
           :obj:`logging.Logger`: The obtained logger
       """
       logger = get_logger(name='mmdet', log_file=log_file, log_level=log_level)
   
       return logger
   ```

   ```python
   # mmcv/utils/logging.py
   
   def get_logger(name, log_file=None, log_level=logging.INFO, file_mode='w'):
       """Initialize and get a logger by name.
   
       If the logger has not been initialized, this method will initialize the
       logger by adding one or two handlers, otherwise the initialized logger will
       be directly returned. During initialization, a StreamHandler will always be
       added. If `log_file` is specified and the process rank is 0, a FileHandler
       will also be added.
   
       Args:
           name (str): Logger name.
           log_file (str | None): The log filename. If specified, a FileHandler
               will be added to the logger.
           log_level (int): The logger level. Note that only the process of
               rank 0 is affected, and other processes will set the level to
               "Error" thus be silent most of the time.
           file_mode (str): The file mode used in opening log file.
               Defaults to 'w'.
   
       Returns:
           logging.Logger: The expected logger.
       """
       logger = logging.getLogger(name)
       if name in logger_initialized:
           return logger
       # handle hierarchical names
       # e.g., logger "a" is initialized, then logger "a.b" will skip the
       # initialization since it is a child of "a".
       for logger_name in logger_initialized:
           if name.startswith(logger_name):
               return logger
   
       stream_handler = logging.StreamHandler()
       handlers = [stream_handler]
   
       if dist.is_available() and dist.is_initialized():
           rank = dist.get_rank()
       else:
           rank = 0
   
       # only rank 0 will add a FileHandler
       if rank == 0 and log_file is not None:
           # Here, the default behaviour of the official logger is 'a'. Thus, we
           # provide an interface to change the file mode to the default
           # behaviour.
           file_handler = logging.FileHandler(log_file, file_mode)
           handlers.append(file_handler)
   
       formatter = logging.Formatter(
           '%(asctime)s - %(name)s - %(levelname)s - %(message)s')
       for handler in handlers:
           handler.setFormatter(formatter)
           handler.setLevel(log_level)
           logger.addHandler(handler)
   
       if rank == 0:
           logger.setLevel(log_level)
       else:
           logger.setLevel(logging.ERROR)
   
       logger_initialized[name] = True
   
       return logger
   ```

2. 重写自己的```get_root_logger```，由于项目用不到分布式训练，因此可以对其进行简化

   ```python
   import logging
   
   def euler_get_logger(name, log_file=None, log_level=logging.INFO, file_mode='w'):
   
       logger = logging.getLogger(name)
       logger.handlers = []
       stream_handler = logging.StreamHandler()
       handlers = [stream_handler]
       if log_file is not None:
           file_handler = logging.FileHandler(log_file, file_mode)
           handlers.append(file_handler)
   
       formatter = logging.Formatter(
           '%(asctime)s - %(name)s - %(levelname)s - %(message)s')
       for handler in handlers:
           handler.setFormatter(formatter)
           handler.setLevel(log_level)
           logger.addHandler(handler)
   
       logger.setLevel(log_level)
       return logger
     
   # main函数使用
   logger = euler_get_logger(name="mmdet", log_file=self.log_file)
   ```

   



