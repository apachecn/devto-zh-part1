# Python 中的多线程与多处理🐍

> 原文：<https://dev.to/nbosco/multithreading-vs-multiprocessing-in-python--63j>

tldr

Python 线程模块使用线程而不是进程。线程唯一地运行在同一个唯一的内存堆中。而进程运行在单独的内存堆中。这使得与流程和对象实例共享信息变得更加困难。因为线程使用同一个内存堆，所以出现了一个问题，多个线程可以写入内存堆中的同一个位置，这就是为什么 CPython 中的全局解释器锁(GIL)被创建为互斥锁以防止这种情况发生。

什么是多线程？

多线程库是轻量级的，共享内存，负责响应用户界面，并很好地用于 I/O 绑定的应用程序。然而，该模块是不可杀死的，并且受 Python 中的 GIL
线程库的约束
多个线程生活在同一个空间中的同一个进程中，每个线程将执行特定的任务，拥有自己的代码、自己的堆栈内存、指令指针和共享堆内存。如果一个线程有内存泄漏，它会损坏其他线程和父进程。

```
import threading

def calc_square(number):
    print('Square': , number * number)
def calc_quad():
    print('Quad': , number * number * number * number)
if __name__ == "__main__":
    number = 7
    thread1 = threading.Thread(target=calc_square, args=(number,))
    thread2 = threading.Thread(target=calc_quad, args=(number,))
    # Will execute both in parallel
    thread1.start()
    thread2.start()
    # Joins threads back to the parent process, which is this
    # program
    thread1.join()
    thread2.join()

# This program reduces the time of execution by running tasks in parallel 
```

Enter fullscreen mode Exit fullscreen mode

什么是多重处理？

多处理库使用独立的内存空间、多个 CPU 内核、绕过 CPython 中的 GIL 限制、子进程是可杀死的(例如程序中的函数调用)并且更易于使用。该模块的一些警告是内存占用更大，IPC 更复杂，开销更大。
签出 Python 文档中的多处理库

```
import multiprocessing

def calc_square(number):
    print('Square': , number * number)
    result = number * number
    print(result)
def calc_quad():
    print('Quad': , number * number * number * number)
if __name__ == "__main__":
    number = 7
    result = None
    p1 = multiprocessing.Process(target=calc_square, args=(number,))
    p2 = multiprocessing.Process(target=calc_quad, args=(number,))
    p1.start()
    p2.start()
    p1.join()
    p2.join()

    # Wont print because processes run using their own memory location                     
    print(result) 
```

Enter fullscreen mode Exit fullscreen mode

一个练习，执行这些程序并测量线程之间、进程和线程之间的增量，相对于从不使用任何一个库。

这是我的第一篇技术博客，如果你觉得读起来有趣，请告诉我。

原帖在此:[https://medium . com/@ nbosco/multi threading-vs-multi processing-in-python-c 7 DC 88 b 50 b 5b](https://medium.com/@nbosco/multithreading-vs-multiprocessing-in-python-c7dc88b50b5b)