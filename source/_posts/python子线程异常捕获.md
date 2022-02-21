---
title: python子线程异常捕获
date: 2021-08-16 17:24:16
tags:
---

## 背景

最近做项目的时候，使用了多线程，但是在主线程（比如main函数）里面不能捕获异常，查阅资料发现：

>
>
>使用start()方法启动子线程时，解释器会为子线程开辟独立的栈空间，主线程自然就无法获取子线程栈的信息。当线程异常中止时，会自行退出而不会将此异常raise到主线程。那么得知了原因，就可以找到解决的办法了。思路是继承标准库的Thread类，进行一些小的改写封装。



## 方法

1. 继承threading.Thread

   主要思路就是通过给类加入新的变量```exitcode```，通过判断是0还是1来判断是否有异常，同时通过```exception```来保存子线程的异常。
   由于项目需求，所以在继承的时候，对于```__init__```函数尽可能和原先一样，然后模仿原来的``run()``函数进程重写。

   ```python
   # 类名自己取 EulerThread
   class EulerThread(threading.Thread):
       def __init__(self, group=None, target=None, name=None, args=(), kwargs=None, daemon=None):
           super(EulerThread, self).__init__(group=group, target=target, name=name, args=args, kwargs=kwargs, daemon=daemon)
           self.exitcode = 0
           self.exception = None
           self.exc_traceback = ''
   
       def run(self):
           try:
               if self._target:
                   self._target(*self._args, **self._kwargs)
           except Exception as e:
               self.exception = e
               self.exitcode = 1  # 如果线程异常退出，将该标志位设置为1，正常退出为0
               self.exc_traceback = ''.join(traceback.format_exception(*sys.exc_info()))  # 在改成员变量中记录异常信息
           finally:
               del self._target, self._args, self._kwargs
   ```

   

2. 调用方法以及封装
   由于项目需求是多个子线程，使用子线程调动类方法进行多线程，对线程进行重复的``setDemo``，``start``，``join``方法，因此可以统一封装。

   **注：**

   - **子线程必须使用join方法，否则内存里面只有``EulerThread``类的初始值，后续的变化不会加入内存，也就是说```exitcode```一直是0**
   - **``start``方法会调用``run``方法，所以重写``run``就可以。**

   ```python
   def start_thread(target):
       thread_service = EulerThread(target=target)
       thread_service.setDaemon(True)
       thread_service.start()
       thread_service.join()
       return thread_service
   
   
   def raise_from_thread(euler_thread):
       if euler_thread.exitcode == 1:
           raise euler_thread.exception
           
   # main 函数操作
   
   # CoverManager是项目中的一个类，里面的是参数无需理会
   cover_manager = CoverManager(MQ_send,connection_send, task_id, task_type, aim, lm_path, coco_path, class_name, test_size)
   # 创建一个类
   thread_train_service = start_thread(target=cover_manager.cover)
   raise_from_thread(thread_train_service)
   ```

   

## Reference

[Python子线程异常捕获 & Python自定义优化线程池](https://blog.csdn.net/ywq935/article/details/86577801)