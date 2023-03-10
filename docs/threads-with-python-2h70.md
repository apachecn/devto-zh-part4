# Python 线程

> 原文：<https://dev.to/petercour/threads-with-python-2h70>

[![](img/1d2ad17c00dec34b55dadf12389d66e1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--4OiTkVKJ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://media1.tenor.cimg/fbc7738df493bb67d902d714a420746e/tenor.gif)

线程是操作系统中可以调度的最小单位。在实践中，当多件事情需要“同时”发生时，就使用线程。

例如，当你在浏览器中点击“下载”时，你应该能够在下载的同时继续网上冲浪。它需要“同时”发生。

实际上，计算机使用调度程序，给每个线程分配很少的时间。

### Python 线程

Python 可以使用线程吗？是的，请！
在 Python 中创建线程

```
#!/usr/bin/python3
# threading example, https://pythonbasics.org/
import threading

# function that prints hello world and current thread
def task():
    print("Hello World: {}".format(threading.current_thread()))

# create a thread and start it
thread1 = threading.Thread(target=task)
thread1.start() 
```

因为线程马上结束，所以只显示“Hello world”。你可以在一个线程中放一个循环，来创建“并行运行的进程”。

```
#!/usr/bin/python3
# threading example, https://pythonbasics.org/

import threading
import time

# function that prints hello world and current thread
def task():
    for i in range(0,10):
        print(" thread " + format(threading.current_thread()), end=''),
        print(" = " + str(i))
        time.sleep(1)

# create a thread and start it
thread1 = threading.Thread(target=task)
thread1.start()

# create a thread and start it
thread2 = threading.Thread(target=task)
thread2.start() 
```

然后，这似乎同时运行*线程 1* 和*线程 2* 。两个线程都只向上计数。

```
thread <Thread(Thread-1, started 140453722031872)> = 0
thread <Thread(Thread-2, started 140453713639168)> = 0
thread <Thread(Thread-1, started 140453722031872)> = 1
thread <Thread(Thread-2, started 140453713639168)> = 1
thread <Thread(Thread-1, started 140453722031872)> = 2
thread <Thread(Thread-2, started 140453713639168)> = 2
... 
```

线程可能会互相等待并无限期阻塞。这就是所谓的死锁

[![](img/a03661c665afd22d4487c2d8fcff3929.png)](https://i.giphy.com/media/z4mhcGKzg1fb2/giphy.gif)

学习 Python:

*   [https://pythonbasics.org](https://pythonbasics.org)
*   [https://pythonprogramminglanguage.com/](https://pythonprogramminglanguage.com/)
*   [https://python.org](https://python.org)