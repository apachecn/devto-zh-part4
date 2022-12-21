# 在 Linux 中运行分离的进程

> 原文：<https://dev.to/pete_speth/running-a-detached-process-in-linux-44kk>

#### 动机

我当时在做一个项目，涉及到从我的树莓 Pi 上的称重传感器读取数据。我想确认重量测量值会保持一致，即使称重传感器一次加载数周。为了做到这一点，我需要找到一种方法来继续记录测量，而不需要在我的笔记本电脑上一直打开 ssh 会话。

#### 让我们来搞清楚

对于这个例子来说，假设我们想要运行一个 python 脚本(称之为`test.py`)来持续地将当前时间打印到一个文件中(有点无意义，但是...🤷 ).

```
import datetime,time

with open('output.txt','w+') as outfile:
  while True:
    outfile.write(str(datetime.datetime.now())+'\n')
    time.sleep(1) 
```

为了测试它，我们运行`python test.py`。我们让脚本运行一段时间，然后用`CTRL+C`中断它。如果我们检查文件，我们会看到每个时间戳输出在单独的一行。

现在，我们可以通过在命令末尾添加一个&符号`&`来在后台运行这个过程。当我们运行它时，我们得到一个输出，告诉我们进程 id (PID)。

```
$ python test.py &
[1] 4424 
```

使用`ps`命令，我们可以看到当前正在运行的进程列表。

```
$ ps

  PID TTY          TIME CMD
 4376 pts/1    00:00:00 bash
 4424 pts/1    00:00:00 python
 4425 pts/1    00:00:00 ps 
```

我们可以使用`kill -INT <PID>`命令结束这个过程。

> 如果我们发送默认信号(`kill 4424`)，程序将没有机会清理，我们写入文件缓冲区的所有内容都将丢失。在我们的例子中，这就是一切，因为我们从不调用 flush()。如果我们检查我们的 output.txt，它将是空的。我们需要发出一个信号，优雅地杀死程序。当进程在前台运行时，发送`kill -INT 4424`将具有与按下`CTRL+C`相同的效果。更多关于 kill 的信息可以通过运行`man kill`或者在 linuxcommand.org[的](http://linuxcommand.org/lc3_man_pages/kill1.html)找到

我们仍然没有实现我们的目标。当我们注销时，这个后台进程将被自动终止。我们需要使用`disown`将流程从会话中分离出来。从头开始:

```
$ python test.py &
[1] 4432

$ disown 4432 
```

现在，我们注销并重新登录。当我们运行`ps`时，我们不再看到我们的进程。不过不用担心，它还在！默认情况下，我们只看到附加到当前会话的进程。我们可以使用`ps -U <username>`来查看给定用户创建的所有进程(在我的例子中， **pi** )。`-f`选项提供了更多的信息和更好的格式。

```
$ ps -U pi -f

UID        PID  PPID  C STIME TTY          TIME CMD
pi        1793     1  0 Aug07 ?        00:00:00 /lib/systemd/systemd --user
pi        1796  1793  0 Aug07 ?        00:00:00 (sd-pam)
pi        4452  4441  0 22:12 ?        00:00:00 sshd: pi@pts/2
pi        4455  4452  0 22:12 pts/2    00:00:00 -bash
pi        4432     1  0 22:15 pts/2    00:00:00 python test.py
pi        4485  4455  0 22:16 pts/2    00:00:00 ps -U pi -f 
```

我们可以让这个过程运行多久就运行多久。几天，几周，几个月-没有规则！(除了在这种情况下，我们最终会耗尽文件缓冲区中的内存，但您会明白这一点)。每当我们完成时，我们可以使用`kill -INT 4432`优雅地终止进程，并带着我们超级有用的数据离开。