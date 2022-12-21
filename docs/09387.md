# ps 命令简介

> 原文：<https://dev.to/ajayatgit/intro-to-ps-command-fh0>

这篇文章的目的是给出一个关于 ps 命令和它用来运行的各种选项的概念。
我见过有人运行类似

`ps -ef`

这样的命令，却不理解添加参数的原因(他们这样做是因为他们在 StackOverflow 的某个地方看到过，或者看到他们的同事在调试某个问题时使用它)。

首先， **ps** 命令的用途:**显示当前进程**的选择信息。

ps 接受多种选项，如下所示

1 UNIX 选项，这些选项可以分组，并且必须以破折号开头。
2 个 BSD 选项，可以分组，不得与破折号一起使用。
3 个 GNU 长选项，前面有两个破折号。

ps 命令通过读取 proc 文件系统中的文件来工作。目录 **/proc/PID** 包含提供进程 PID 信息的各种文件。这些文件的内容是由内核在进程读取它们时动态生成的。

**ps**

它显示**进程 ID (pid=PID)** 、与进程相关的终端(tname = TTY)【DD-】hh:mm:ss 格式的累计 CPU 时间(time=TIME)和可执行文件名称(ucmd=CMD)。默认情况下，输出是未排序的。

```
[ajays@ip-10-40-19-132 ~]$ ps
  PID TTY          TIME CMD
20244 pts/0    00:00:00 bash
20342 pts/0    00:00:00 ps
[ajays@ip-10-40-19-132 ~]$ who
ajays    pts/0        2019-08-06 20:38 (ip-10-19-12-119.ca-central-1.compute.internal)
[ajays@ip-10-40-19-132 ~]$ 
```

因此，根据解释，ps 命令将只显示与 invoker 相同的用户和相同的终端相关联的流程。下一个命令列出了 invoker ( ajays)和 terminal ( pts/0)。所以 ps 命令的输出是与 ajays 关联的进程，并附加到终端 pts/0。

ps 使用的选项可以主要分为以下几类。

*   过程选择(简单或通过列表)
*   输出格式控制和修改器
*   线程显示

上述每一类都有很多选项，我们无法在本文中涵盖，但会尝试涵盖一些常见的选项。

**ps -f**

**-f** 选项是 UNIX 选项。它被用来做全格式列表。
显示以下详细信息 UID，PID，PPID，C，STIME，TTY，时间，CMD。

*UID* -启动该进程的用户的名称。

*PID* -作为内存中正在运行的进程的标识号。

*PPID* -它是父进程 ID。这个 id 是进程的 pid，因为
启动了这些进程。

*C*——处理器利用率信息，单位为%。

*时间* -这是流程的开始时间。

*TTY*——这是流程开始的终端。

*时间* -进程使用 cpu 的总时间。

*CMD* -执行的命令和参数。

```
[ajays@ip-10-40-19-132 ~]$ ps -f
UID        PID  PPID  C STIME TTY          TIME CMD
ajays    20244 20213  0 20:38 pts/0    00:00:00 -bash
ajays    29906 20244  0 22:12 pts/0    00:00:00 ps -f
[ajays@ip-10-40-19-132 ~]$ 
```

**ps -ef**

现在我们又附加了一个参数 **-e** (这是一个基于 UNIX 的选项)。
此选项为精选选项。选择所有进程。
该命令的一个片段如下所示

```
5984      6011  5341  0 Jul31 ?        00:00:00 /opt/couchdb/bin/couchjs -S 268435456 /opt/couchdb/share/server/ma
5984      6012  5341  0 Jul31 ?        00:00:01 /opt/couchdb/bin/couchjs -S 268435456 /opt/couchdb/share/server/ma
5984      6013  5341  0 Jul31 ?        00:00:00 /opt/couchdb/bin/couchjs -S 268435456 /opt/couchdb/share/server/ma
5984      6014  5341  0 Jul31 ?        00:00:03 /opt/couchdb/bin/couchjs -S 268435456 /opt/couchdb/share/server/ma
5984      6015  5341  0 Jul31 ?        00:00:09 /opt/couchdb/bin/couchjs -S 268435456 /opt/couchdb/share/server/ma
5984      6016  5341  0 Jul31 ?        00:00:07 /opt/couchdb/bin/couchjs -S 268435456 /opt/couchdb/share/server/ma
5984      6017  5341  0 Jul31 ?        00:00:08 /opt/couchdb/bin/couchjs -S 268435456 /opt/couchdb/share/server/ma
ajays    23552 20244  0 23:07 pts/0    00:00:00 ps -ef
postfix  24642  3429  0 22:01 ?        00:00:00 pickup -l -t unix -u 
```

**ps 之于**

现在我们已经附加了 **-a** (这是基于 UNIX 的选项)。
选择除会话领导和与终端无关的流程之外的所有流程。
所以 **-a** 和 **-e** 的区别是很明显的，-e 也会返回与一个终端无关的进程，而-a 只会返回与一个终端相关的进程。

会话领导是 PID=SID 的过程。