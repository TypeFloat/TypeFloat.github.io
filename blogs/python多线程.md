# Python中的多线程

## 一、多线程与多进程

多线程与多进程是操作系统中的概念，这里仅简单介绍一下。

现在使用的主流操作系统都是多任务操作系统，一个程序运行起来就被称为一个进程，既然是多任务操作系统，那系统中就可以同时运行多个程序，即多进程。操作系统负责对各个进程进行调度，决定哪个进程占用CPU进行计算。根据操作系统中的定义，进程是资源分配的最小单位。而线程包含在进程中，它是调度的最小单位。在多数情况下，并不严格区分线程与进程，也称线程是轻量级进程。

无论是多线程编程还是多进程编程，在对于当前多核心多线程的CPU来说，都可以实现计算的并行化处理，可以提高计算速度或某些需要同时执行的业务逻辑。CPU在进行调度时需要消耗一定的资源，而线程切换的资源消耗要低于进程切换的资源消耗，这也是线程被称为轻量级进程的原因。刚才说到，进程是资源分配的最小单位，所以不同的进程之间一般不含共享资源，而是通过消息传递机制等进行通信，有利于对资源的隔离与保护；线程正好相反，一个进程的多个线程共享所有资源，方便了线程之间的信息传递，但需要进行加锁等资源保护行为。

## 二、Python多线程

Python提供了多线程编程的库——threading，下面对多线程常用的操作进行一个简单的介绍。

```python
import threading # 引入多线程库

# 定义需要在不同线程中执行的任务
def task_one():
  while True:
  	print("I'm thread one.")
  
def task_two():
  while True:
  	print("I'm thread two.")
    
thread1 = threading.Thread(target=task_one)
thread2 = threading.Thread(target=task_two)
thread1.start()
thread2.start()
```

执行上述代码，可以得到两个语句按照不定的顺序被打印到屏幕上，说明`task_one`、`task_two`同时在执行。

若在执行的函数中需要传递参数，可以指定args参数。

```python
import threading # 引入多线程库

# 定义需要在不同线程中执行的任务
def task_one(a):
  while True:
  	print(a)
  
def task_two(b):
  while True:
  	print(b)
    
thread1 = threading.Thread(target=task_one, args=(1,))
thread2 = threading.Thread(target=task_two, args=(2,))
thread1.start()
thread2.start()
```

程序在多个线程上执行时，资源是共享的，因此最常见的问题是资源的互斥访问。例如线程一访问变量A，而线程二对变量A执行自加操作，线程一二同时执行自然会产生意想不到的效果，因此需要保证在线程一执行时，线程二等待，线程二执行时线程一等待，即线程一二要互斥的访问变量A，最直接的办法是加锁。

```python
import threading # 引入多线程库

a = 0
lock = threading.Lock()
# 定义需要在不同线程中执行的任务
def task_one():
  while True:
    lock.acquire()
  	print(a)
    lock,release()
  
def task_two():
  while True:
    lock.acquire()
  	a += 1
    lock.release()
    
thread1 = threading.Thread(target=task_one)
thread2 = threading.Thread(target=task_two)
thread1.start()
thread2.start()
```
[返回目录](../content.html)