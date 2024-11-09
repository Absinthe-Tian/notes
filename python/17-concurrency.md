---
title: 17.多进程与多线程
date: 2022-10-17 00:00:00
categories: Python语言
tags:
  - 并发
  - 多进程
  - 多线程
---

# 概念

线程：是计算机中可以被CPU调度的最小单元。
进程：是计算机中资源分配的最小单元。

一个程序，至少有一个进程，一个进程至少有一个主线程，线程是真正的工作单位。

一个进程可以有多个线程，且同一进程下的线程共享该进程资源。

通过多进程和多线程可以提高程序的运行效率，从而达到高并发。

# 多线程

## 常用方法

* `t.start()`：启动一个线程，代表该线程准备就绪，等待CPU调度，具体开始执行时间有CPU决定。
* `t.join(timeout)`：加入到优先执行队列，会阻塞调用它的线程，直到该线程执行完毕。
* `t.setDaemon(bool)`：是否设置为守护线程，守护线程即主线程执行完毕就会关闭子线程。
* `t.setName()`：设置线程名。
* `t.is_alive()`：检查线程是否处于活动状态。
* `threading.current_thread()`：获取当前正在执行的线程对象。
* `threading.Thread.run(self)`：自定义线程类执行的方法，可直接通过`self._args`获取参数。
* `threading.Lock()`：获取线程锁，使用线程锁以确保是线程安全的，否则可能导致数据混乱，是非递归锁，不支持同一线程多次获取锁，对于一些复杂的线程逻辑可使用`RLock` (可重入锁)。
* `lock.acquire()`和`lock.release()`：加锁和解锁，注意规范使用勿导致死锁，也可使用`with lock:`替代。

## 多线程

```python
import threading
import time

def worker(num):
    for _ in range(1000):
        print(f"Worker {num} is working")


start_time = time.time()

thread1 = threading.Thread(target=worker, args=(1,))
thread2 = threading.Thread(target=worker, args=(2,))

thread1.start()
thread2.start()

thread1.join()
thread2.join()

print(time.time() - start_time)
```

## 自定义线程类

```python
import threading
import time

class MyThread(threading.Thread):
    def __init__(self, num):
        super().__init__()
        self.num = num

    # 重写run方法即可
    def run(self):
        for _ in range(1000):
            print(f"Worker {self.num} is working")

start_time = time.time()

thread1 = MyThread(1)
thread2 = MyThread(2)

thread1.start()
thread2.start()

thread1.join()
thread2.join()

print(time.time() - start_time)
```

## 线程池

```python
import concurrent.futures
import time

def worker(num):
    for _ in range(1000):
        print(f"Worker {num} is working")
    
    return "Accomplish"

start_time = time.time()

# 创建线程池
with concurrent.futures.ThreadPoolExecutor(max_workers=2) as executor:
    # 提交任务并获取Future对象
    futures = [executor.submit(worker, args=i) for i in range(2)]

    # 获取结果
    for future in concurrent.futures.as_completed(futures):
        result = future.result()

print(time.time() - start_time)
```

# 多进程

多进程的常用方法与多线程类似，只是将`threading`替换为`multiprocessing`即可。

## 多进程

```python
import multiprocessing
import time

def worker(num):
    for _ in range(1000):
        print(f"Worker {num} is working")

if __name__ == "__main__":
    start_time = time.time()
    for i in range(2):
        p = multiprocessing.Process(target=worker, args=(i,))
        p.start()
        p.join()
    
    print(time.time() - start_time)
```

## 进程池

```python
import multiprocessing
import time

def worker(num):
    for _ in range(1000):
        print(f"Worker {num} is working")

if __name__ == '__main__':
    start_time = time.time()

    # 创建进程池
    with multiprocessing.Pool(processes=2) as pool:
        pool.map(worker, [(1,), (2,)])

    print(time.time() - start_time)
```

# Python中的GIL

## 介绍

GIL（Global Interpreter Lock），全局解释器锁，是CPython（Python的标准实现）中的一个重要特性。它是一种互斥锁，用于确保在任何给定时刻==只有一个线程==可以执行Python字节码。

## 影响

GIL对于*IO密集型任务*的影响较小，因为在这种情况下，大部分时间线程都在等待IO操作完成。然而，在*计算密集型任务*中，GIL可能成为性能瓶颈。

不要以为GIL的存在就一味的使用多进程，多进程会消耗更多的资源，不利于程序的运行和维护。

不要使用过多线程，因为CPU在线程间进行切换也是需要消耗时间和资源的。

## 解决

1. IO密集型：使用多线程或异步编程，如文件的读写、资源下载等等。
2. 计算密集型：使用多进程，如大量的数据计算等等。