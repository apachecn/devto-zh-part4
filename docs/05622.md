# redis:fork–无法分配内存、Linux、虚拟内存和 vm.overcommit_memory

> 原文：<https://dev.to/setevoy/redis-fork-cannot-allocate-memory-linux-virtual-memory-and-vm-overcommitmemory-1k25>

[![](img/f6d45197b0b18a1a39702233dbf5afe7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--rqSEDlVy--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://rtfm.co.ua/wp-content/uploads/2018/05/redislogo.png) 目前，我正在为我们的应用程序配置一个 Redis 作为缓存服务，在此期间，我面临一个问题:我是否需要将`vm.overcommit_memory`设置为值 1，也就是说，是否禁用它？

这个问题对我来说太老了，见 *[故事](https://rtfm.co.ua/en/?p=21521#The_story)* ，但直到现在我才有时间找到问题的真正根源，把所有的东西放在一起，写下了下面的帖子。

它最初是用俄语发布的[，这是我自己翻译的副本。因为真的有很多文本，我希望我在翻译过程中没有混淆任何东西。如果有的话——请随意用鼠标选择一个文本，然后按 Shift+Enter 向我发送通知。](https://rtfm.co.ua/redis-linux-i-vm-overcommit_memory/)

所以，问题本身是 Redis 文档和几乎所有关于 Redis 性能的 HowTo/guide 都漫不经心地告诉我们，通过将`vm.overcommit_memory`设置为 1 来禁用 Linux `overcommit_memory`机制，尤其是作为“*分支—无法分配内存*”错误的解决方案。

在这篇文章中，我们将试图弄清楚`overcommit_memory`到底是什么，在哪里以及如何使用它，在我目前的情况下，我们真的需要改变它吗，也就是说，Redis 何时将仅用于缓存。

本帖涵盖的主题:

*   为什么过度承诺不好？
*   瑞斯坚持不懈
*   Redis save，save bgsave-重复储存，储存 bgs save，储存 bgs，储存 bgs，储存 bgs，储存 bgs，储存 bgs，储存 bgs，储存 bgs，储存 bgs，储存 bgs，储存 bgs，储存 bgs
*   Redis rdbSave()和 rdbSaveBackground()函数
*   分叉()vs 分叉()vs 克隆()
*   redis–fork:无法分配内存–原因
*   过量使用内存值
*   著名的“启发式算法”(启发式过量使用处理)
*   正在检查 vm.overcommit_memory
*   结论
*   故事

### 为什么承诺过多不好？

由于这篇文章原本是俄语的，这部分已经被翻译了——我将在这里复制粘贴原文的一部分。阅读全文—[什么是过度承诺？为什么不好？](https://www.etalabs.net/overcommit.html)T3】

**过量使用指的是在不保证物理存储存在的情况下分配虚拟内存的做法。**打个比方，这就像使用信用卡却不记录你的购物记录。一个执行过量使用的系统只是不断地释放虚拟内存，直到收债人来调用——也就是说，直到某个程序接触到一个以前没有接触过的页面，而内核找不到任何物理内存来实例化它——然后东西开始崩溃。

当“东西开始倒塌”时会发生什么？它可以有所不同，但 Linux 的方法是在内核中设计一个精心设计的启发式“OOM 杀手”,它判断每个进程的行为，并决定谁是造成机器内存不足的“罪魁祸首”,然后杀死有罪的一方。在实践中，从避免杀死关键系统进程和杀死“霸占”内存的进程的角度来看，这种方法非常有效，但问题是没有一个进程真正“犯了”使用比可用内存更多的内存的错误，因为每个人都被(错误地)告知内存可用。

假设你不希望内存分配出现这种不确定性/危险？简单的解决方案是立即静态分配对应于所有虚拟内存的物理内存。延伸一下信用卡的比喻，这就像使用现金购买所有商品，或者使用借记卡。你从超支中获得了安全感，但也失去了很多流动性。谢天谢地，有更好的方法来管理内存。

当你想避免提交太多内存时，现实中采用的方法是*考虑*所有分配的内存。在我们的信用卡类比中，这相当于使用信用卡，但是记录所有的购物记录，并且永远不要购买超过你有能力偿还的金额。在管理虚拟内存时，这被证明是正确的，事实上，当您将 vm.overcommit_memory sysctl 参数设置为值 2 时，Linux 就是这样做的。在这种模式下，所有可能被修改(即具有读写权限)或缺乏支持(即磁盘或其他设备上的原始副本，如果需要丢弃，可以从该副本恢复)的虚拟内存都被视为“提交费用”，即内核作为*提交*/承诺给应用程序的内存量。当新的虚拟内存分配将导致提交费用超过可配置的限制(默认情况下，交换空间的大小加上物理 ram 大小的一半)时，分配将失败。

### 惯犯的坚持

Redis 使用两种机制来实现数据持久性——RDB 快照(*时间点快照*),它创建从内存到固态硬盘的数据副本；AOF，它不断地写日志，服务器在其工作期间执行的每一个操作都会记录日志。更多信息请参见文档—[Redis 持久性](https://redis.io/topics/persistence)。

Redis 从磁盘上的内存创建数据快照时，`overcommit_memory`会介入，特别是在执行 [`BGSAVE`](https://redis.io/commands/bgsave) 和 [`BGREWRITEAOF`](https://redis.io/commands/bgrewriteaof) 命令期间。

下面我们将集中讨论 [`BGSAVE`](https://redis.io/commands/bgsave) 命令，在此期间 Redis 创建一个子进程，将数据复制到磁盘。

### 再救，再救

有点令人困惑的可能是 Redis 本身:在其配置文件中,`save`选项负责`BGSAVE`操作。

然而，Redis 也有`SAVE`命令，但是它的工作方式不同:

*   `SAVE`是同步命令，在创建副本期间在内存上执行写块操作
*   `BGSAVE`它本身是一种异步机制——它与主服务器的进程并行工作，不会影响其操作和客户端连接，因此是创建备份的首选方式

但是在无法使用`BGSAVE`的情况下，例如，由于“ [*无法在后台保存:fork:无法分配内存*](https://rtfm.co.ua/en/?p=21521#Redis_fork_Cannot_allocate_memory_the_cause) ”错误，可以使用`SAVE`命令。

让我们使用`strace`工具进行检查。

创建一个测试配置文件`redis-testing.conf` :

```
save 1 1
port 7777 
```

使用以下配置运行`strace`和`redis-server`:

```
root@bttrm-dev-console:/home/admin# strace -o redis-trace.log redis-server redis-testing.conf 
```

`strace`将把它的输出写到 *redis-trace.log* 文件中，我们将检查该文件以找到 redis-server 在`SAVE`和`BGSAVE`操作期间使用的系统调用:

```
root@bttrm-dev-console:/home/admin# tail -f redis-trace.log | grep -v 'gettimeofday\|close\|open\|epoll\_wait\|getpid\|read\|write' 
```

这里用`grep -v`我们去掉了一个我们现在不需要的“垃圾”调用。

我们可以使用`-e trace=`只抓取必要的电话——但是我们还不知道我们到底在找什么。

在 Redis 配置文件中，我们设置了`port 777`和`save 1 1`，例如，如果至少有一个键被更改，每秒钟创建一个数据库副本到磁盘。

添加新密钥:

```
admin@bttrm-dev-console:~$ redis-cli -p 7777 set test test
OK 
```

并查看`strace`日志:

```
root@bttrm-dev-console:/home/admin# tail -f redis-trace.log | grep -v 'gettimeofday\|close\|open\|epoll\_wait\|getpid\|read\|write'
accept(5, {sa\_family=AF\_INET, sin\_port=htons(60816), sin_addr=inet_addr("127.0.0.1")}, [128->16]) = 6
...
stat("/etc/localtime", {st_mode=S_IFREG|0644, st_size=2097, ...}) = 0
clone(child_stack=NULL, flags=CLONE_CHILD_CLEARTID|CLONE_CHILD_SETTID|SIGCHLD, child_tidptr=0x7ff26beda190) = 1790
stat("/etc/localtime", {st_mode=S_IFREG|0644, st_size=2097, ...}) = 0
--- SIGCHLD {si_signo=SIGCHLD, si_code=CLD_EXITED, si_pid=1790, si_uid=0, si_status=0, si_utime=0, si_stime=0} ---
wait4(-1, [{WIFEXITED(s) && WEXITSTATUS(s) == 0}], WNOHANG, NULL) = 1790
stat("/etc/localtime", {st_mode=S_IFREG|0644, st_size=2097, ...}) = 0 
```

这里是`clone()`调用(为什么是`clone()`而不是`fork()`——我们稍后会讲到，在[*fork()vs fork()vs clone()*](https://rtfm.co.ua/en/?p=21521#fork_vs_fork_vs_clone))。这个`clone()`创建了一个新的子进程，该子进程将依次创建数据副本。

现在–运行`SAVE`命令:

```
admin@bttrm-dev-console:~$ redis-cli -p 7777 save
OK 
```

并查看日志:

```
accept(5, {sa_family=AF_INET, sin_port=htons(32870), sin_addr=inet_addr("127.0.0.1")}, [128->16]) = 6
...
rename("temp-1652.rdb", "dump.rdb")     = 0
stat("/etc/localtime", {st_mode=S_IFREG|0644, st_size=2097, ...}) = 0
epoll_ctl(3, EPOLL_CTL_DEL, 6, 0x7ffe6712430c) = 0 
```

此时没有`clone()`——转储由 Redis 主进程执行并保存到`dump.rdb`文件——检查`strace`输出中的*重命名(" temp-1652.rdb "，" dump.rdb")* (我们将很快看到该名称出现在那里—*temp-1652 . RDB)。*

现在调用`BGSAVE` :

```
admin@bttrm-dev-console:~$ redis-cli -p 7777 bgsave
Background saving started 
```

并再次检查日志:

```
accept(5, {sa_family=AF_INET, sin_port=htons(33030), sin_addr=inet_addr("127.0.0.1")}, [128->16]) = 6
...
clone(child_stack=NULL, flags=CLONE_CHILD_CLEARTID|CLONE_CHILD_SETTID|SIGCHLD, child_tidptr=0x7ff26beda190) = 1879
stat("/etc/localtime", {st_mode=S_IFREG|0644, st_size=2097, ...}) = 0
epoll_ctl(3, EPOLL_CTL_DEL, 6, 0x7ffe6712430c) = 0
--- SIGCHLD {si_signo=SIGCHLD, si_code=CLD_EXITED, si_pid=1879, si_uid=0, si_status=0, si_utime=0, si_stime=0} ---
wait4(-1, [{WIFEXITED(s) && WEXITSTATUS(s) == 0}], WNOHANG, NULL) = 1879
stat("/etc/localtime", {st_mode=S_IFREG|0644, st_size=2097, ...}) = 0 
```

我们的`clone()`也在这里，它产生了另一个 PID 为 1879 的子进程:

```
...
clone([...]) = 1879
... 
```

### Redis `rdbSave()`和`rdbSaveBackground()`功能

确切地说，转储本身是由唯一的 Redis 函数创建的—[`rdbSave()`](https://github.com/antirez/redis/blob/unstable/src/rdb.c#L1268):

```
...
/* Save the DB on disk. Return C_ERR on error, C_OK on success. */
int rdbSave(char *filename, rdbSaveInfo *rsi) {
    ...
    snprintf(tmpfile,256,"temp-%d.rdb", (int) getpid());
    fp = fopen(tmpfile,"w");
... 
```

该函数在执行`redis-cli -p 7777 SAVE`命令时被调用。

这是我们从上面的`strace`输出中得到的 *temp-1652.rdb* 文件名:

```
...
snprintf(tmpfile,256,"temp-%d.rdb", (int) getpid());
... 
```

其中*1652*–是 Redis 服务器的主进程 PID。

就其本身而言，在`BGSAVE`命令期间，另一个函数被调用-[`rdbSaveBackground()`](https://github.com/antirez/redis/blob/unstable/src/rdb.c#L1331):

```
...
int rdbSaveBackground(char *filename, rdbSaveInfo *rsi) {
    ...
    start = ustime();
    if ((childpid = fork()) == 0) {
        int retval;
        /* Child */
        closeListeningSockets(0);
        redisSetProcTitle("redis-rdb-bgsave");
        retval = rdbSave(filename,rsi);
... 
```

这又创建了一个新的子进程:

```
...
if ((childpid = fork()) == 0)
... 
```

并且该进程将依次执行`rdbSave()` :

```
...
retval = rdbSave(filename,rsi);
... 
```

### `fork()` vs `fork()` vs `clone()`

现在，让我们回到这个问题——为什么在`strace`的输出中我们看到的是`clone()`系统调用，而不是由`rdbSaveBackground()`函数调用的`fork()`?

嗯，那只是因为`fork()`！= `fork()`:

1.  有一个 Linux 内核系统调用
2.  还有一个`glibc`的`fork()`函数，它是 [`clone()`](https://linux.die.net/man/2/clone) 系统调用的包装器

尝试使用 [`apropos`](https://rtfm.co.ua/linux-apropos-poisk-po-man-stranicam/) 工具:
检查它们

```
[setevoy@setevoy-arch-work ~/Temp/redis] [unstable*] $ apropos fork
fork (2)             - create a child process
fork (3am)           - basic process management
fork (3p)            - create a new process 
```

所以，[`fork(2)`](https://linux.die.net/man/2/fork)——是系统调用，而`fork(3p)`——是`glibc`的函数——[https://github . com/BMI nor/glibc/blob/master/sysdeps/nptl/fork . c # L48](https://github.com/bminor/glibc/blob/master/sysdeps/nptl/fork.c#L48)。

现在，阅读以下手册–打开`man 2 fork` :

```
[setevoy@setevoy-arch-work ~/Temp/redis] [unstable*] $ man 2 fork | grep -A 5 NOTES

NOTES

Under Linux, fork() is implemented using copy-on-write pages, so the only penalty that it incurs is the time and memory required to duplicate the parent's page tables, and to create a unique task structure for the child.

C library/kernel differences

Since version 2.3.3, rather than invoking the kernel's fork() system call, the glibc fork() wrapper that is provided as part of the NPTL threading implementation invokes clone(2) with flags that provide the same effect as the
 traditional system call.  (A call to fork() is equivalent to a call to clone(2) specifying flags as just SIGCHLD.)  The glibc wrapper invokes any fork handlers that have been established using pthread_atfork(3). 
```

> glibc fork()包装器[…]调用 clone(2)，而不是调用内核的 fork()系统调用

因此，当`rdbSaveBackground()`执行`fork()`时，它使用的不是`fork(2)`，而是来自`glibc`的`fork(3p)`，而`fork(3p)`又被别名为 [`__libc_fork()`](https://github.com/bminor/glibc/blob/master/sysdeps/nptl/fork.c#L149) :

```
...
weak_alias (__libc_fork, __fork)
libc_hidden_def (__fork)
weak_alias (__libc_fork, fork) 
```

而在`__libc_fork()`——“魔法”本身就是通过调用 [`arch_fork()`](https://github.com/bminor/glibc/blob/master/sysdeps/unix/sysv/linux/arch-fork.h#L32) 宏:
而发生的

```
...
pid = arch_fork (&THREAD_SELF->tid);
... 
```

通过`grep`在`glibc`的源代码中找到它:

```
[setevoy@setevoy-arch-work ~/Temp/glibc] [master*] $ grep -r arch_fork .
./ChangeLog:    (arch_fork): Issue INLINE_CLONE_SYSCALL if defined.
./ChangeLog:    * sysdeps/nptl/fork.c (ARCH_FORK): Replace byarch_fork.
./ChangeLog:    * sysdeps/unix/sysv/linux/arch-fork.h (arch_fork): New function.
./sysdeps/unix/sysv/linux/arch-fork.h:/* arch_fork definition for Linux fork implementation.
./sysdeps/unix/sysv/linux/arch-fork.h:arch_fork (void *ctid)
./sysdeps/nptl/fork.c:  pid = arch_fork (&THREAD_SELF->tid); 
```

`arch_fork()`在 [`sysdeps/unix/sysv/linux/arch-fork.h`](https://github.com/bminor/glibc/blob/master/sysdeps/unix/sysv/linux/arch-fork.h#L32) 文件中描述，该文件将依次调用`clone()` :

```
...
ret = INLINE_SYSCALL_CALL (clone, flags, 0, NULL, 0, ctid);
... 
```

我们将在`strace`的日志中看到。

为了检查是否如此，我们真的使用了`glibc fork()`而不是系统调用——让我们通过使用 GNU 官方的[文档](https://www.gnu.org/software/libc/manual/html_node/Process-Creation-Example.html) :
来写一些小的 C 程序

```
#include <unistd.h>
#include <sys/wait.h>
#include <stdio.h>
int main () {
  pid_t pid;
  pid = fork ();
  if (pid == 0) {
      printf("Child created\n");
      sleep(100);
  }
} 
```

在这里的`pid = fork()`中，我们以同样的方式调用`fork()`，就像它被`rdbSaveBackground()`函数调用一样。

然后让我们使用`ltrace`来跟踪一个库函数(不像`strace`用于跟踪系统调用):

```
$ ltrace -C -f ./test_fork_lib
[pid 5530] fork( <unfinished ...>
[pid 5531] <... fork resumed> )                                                                                                                    = 0
[pid 5530] <... fork resumed> )                                                                                                                    = 5531
[pid 5531] puts("Child created" <no return ...>
[pid 5530] +++ exited (status 0) +++
Child created
[pid 5531] <... puts resumed> )                                                                                                                    = 14
[pid 5531] sleep(100)                                                                                                                              = 0 
```

通过使用`lsof`工具——找到我们的进程打开的所有文件:

```
[setevoy@setevoy-arch-work ~/Temp/glibc] [master*] $ lsof -p 5531
COMMAND    PID    USER   FD   TYPE DEVICE SIZE/OFF    NODE NAME
test_fork 5531 setevoy  cwd    DIR  254,3     4096 4854992 /home/setevoy/Temp/glibc
test_fork 5531 setevoy  rtd    DIR  254,2     4096       2 /
test_fork 5531 setevoy  txt    REG  254,3    16648 4855715 /home/setevoy/Temp/glibc/test_fork_lib
test_fork 5531 setevoy  mem    REG  254,2  2133648  396251 /usr/lib/libc-2.29.so
... 
```

或者通过使用`ldd`–检查哪些库将用于使代码工作:

```
[setevoy@setevoy-arch-work ~/Temp/glibc] [master*] $ ldd test_fork_lib
...
libc.so.6 => /usr/lib/libc.so.6 (0x00007f26ba77f000) 
```

`libc-2.29.so`取自`glibc`包:

```
[setevoy@setevoy-arch-work ~/Temp/glibc] [master*] $ pacman -Ql glibc | grep libc-2.29.so
glibc /usr/lib/libc-2.29.so 
```

检查库文件中函数的另一种方法是使用`objdump`工具:

```
[setevoy@setevoy-arch-work ~/Temp/linux] [master*] $ objdump -T /usr/lib/libc.so.6 | grep fork
00000000000c93c0 g    DF .text  00000000000001fe  GLIBC_PRIVATE __libc_fork 
```

`__libc_fork`–这是我们在`.text`部分的函数(详见[Linux:c—адресноепростиантвопроцесса](https://rtfm.co.ua/linux-c-adresnoe-prostranstvo-processa/)和[c:созданиеии](https://rtfm.co.ua/c-sozdanie-i-primenenie-shared-library-v-linux/)

### Redis–fork:无法分配内存–原因

*同样，由于下面的文字最初是为俄罗斯邮报翻译的，所以我只是将文档复制粘贴到这里。阅读完整的文档—[即使我有很多空闲内存](https://redis.io/topics/faq#background-saving-fails-with-a-fork-error-under-linux-even-if-i-have-a-lot-of-free-ram)，后台保存在 Linux 下也会失败，并出现 fork()错误。*

Redis 后台保存模式依赖于现代操作系统中 fork 的写时复制语义:Redis forks(创建子进程)是父进程的精确副本。子进程将数据库转储到磁盘上，并最终退出。理论上，子进程应该使用与作为副本的父进程一样多的内存，但实际上，由于大多数现代操作系统实现的写入时复制语义，父进程和子进程将共享公共内存页面。只有在子页面或父页面发生变化时，页面才会被复制。因为从理论上讲，当子进程保存内存时，所有的页面都可能改变，Linux 不能预先知道子进程将占用多少内存，所以如果 overcommit_memory 设置被设置为零，fork 将会失败，除非有足够的空闲 RAM 来复制所有的父内存页面，结果是如果您有一个 3 GB 的 Redis 数据集，而只有 2 GB 的空闲内存，那么它将会失败。

将 overcommit_memory 设置为 1 告诉 Linux 放松下来，以更乐观的分配方式执行 fork，这确实是您希望 Redis 实现的。

### `overcommit_memory`值

`vm.overcommit_memory`可以包含以下三个值之一:

*   **0** :内核将执行更多的虚拟内存分配，然后服务器执行，但将依靠“启发式算法”( [*启发式过量分配处理*](https://www.kernel.org/doc/Documentation/vm/overcommit-accounting) )来决定何时批准或拒绝进程的内存分配
*   **1** :内核总是会执行过量提交，这会导致更多的*内存不足*错误，但是对于那些正在使用内存的服务来说可能是好的
*   **2** :内核将执行过量提交，但在`overcommit_ratio`或`overcommit_kbytes`参数设置的范围内

### 著名的“启发式算法”(Heuristic Overcommit handling)

在大多数文档/指南/指南等中，只提到了这个算法，但是要找到它的详细描述来理解它是如何工作的并不容易。

像往常一样——*“只看出处！”*

过量使用检查由内存管理模块的补充函数 [`__vm_enough_memory()`](https://elixir.bootlin.com/linux/v5.0.7/source/mm/util.c#L639) 执行，并在内核的`mm/util.c`文件中描述。

此函数接受进程请求分配的页面数量，然后此函数将:

1.  如果 overcommit_memory == 1 ( `if (sysctl_overcommit_memory == OVERCOMMIT_ALWAYS)`):
    1.  返回 0 并允许超量承诺
2.  如果 OVERCOMMIT _ memory = = 0(`if (sysctl_overcommit_memory == OVERCOMMIT_GUESS)``sysctl_overcommit_memory`[默认设置为](https://elixir.bootlin.com/linux/v5.0.7/source/mm/util.c#L555)到 OVERCOMMIT_GUESS，而 [`linux/mman.h`](https://elixir.bootlin.com/linux/v5.0.7/source/include/uapi/linux/mman.h#L11) 文件中 OVERCOMMIT_GUESS 设置为 0):
    1.  现在统计所有空闲页面，并将它们保存到`free`变量:`free = global_zone_page_state(NR_FREE_PAGES)`
    2.  将`free`增加到[文件备份](https://en.wikipedia.org/wiki/Memory-mapped_file)(参见[文件备份和交换](https://techtalk.intersec.com/2013/07/memory-part-1-memory-types/#File-backed_and_Swap)、[内存映射文件](https://en.wikipedia.org/wiki/Memory-mapped_file))内存页的数量，即通过交换到磁盘可以释放的页面:`free += global_node_page_state(NR_FILE_PAGES)`
    3.  通过共享内存减少`free`(参见[共享内存](https://techtalk.intersec.com/2013/07/memory-part-1-memory-types/#Shared_Memory,)、[共享内存](https://en.wikipedia.org/wiki/Shared_memory))页数`free -= global_node_page_state(NR_SHMEM)`
    4.  通过添加交换页`free += get_nr_swap_pages()`来增加`free`
    5.  增加由 SReclaimable(参见 [`man 5 proc`](http://man7.org/linux/man-pages/man5/proc.5.html) *SReclaimable* )编号`free += global_node_page_state(NR_SLAB_RECLAIMABLE)`
    6.  增加由 KReclaimable(见[`man 5 proc`](http://man7.org/linux/man-pages/man5/proc.5.html)*)号`free += global_node_page_state(NR_KERNEL_MISC_RECLAIMABLE)`*
    **   通过最小保留页减少(参见 [`calculate_totalreserve_pages()`](https://elixir.bootlin.com/linux/v5.0.7/source/mm/page_alloc.c#L7394) 和[一个过承诺的增强 _ 猜测](https://lwn.net/Articles/178850/) ) `free -= totalreserve_pages`*   减少为根用户保留的内存(см。[T0](https://elixir.bootlin.com/linux/v5.0.7/source/mm/mmap.c#L3676))`free -= sysctl_admin_reserve_kbytes`*   最后一步是检查当前可用的内存——以及进程请求的内存——如果上面所有计算的`free`变量将包含足够的页面——它将返回 0 值:`if (free > pages) return 0;`*

 *参见【Linux 如何处理虚拟内存过量使用、[过量使用-会计](https://www.kernel.org/doc/Documentation/vm/overcommit-accounting)、[检查可用内存](https://www.kernel.org/doc/gorman/html/understand/understand016.html)。

### 检查`vm.overcommit_memory`

嗯——这只是一个理论——现在还不是时候真正看看所有这些在`vm.overcommit_memory`变化期间是如何工作的，内存一般是如何分配的。

让我们使用下一个简单的代码:

```
#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>
#include <string.h>
#include <fcntl.h>
int main() {

    printf("main() started\n");

    long int mem_size = 4096;

    void *mem_stack = malloc(mem_size);

    printf("Parent pid: %lu\n", getpid());

    sleep(1200);
} 
```

在`void *mem_stack = malloc(mem_size)`行中，我们被请求分配 4096 字节，这是在`mem_size`变量中设置的。

检查当前`overcommit_memory`值:

```
root@bttrm-dev-console:/home/admin# cat /proc/sys/vm/overcommit_memory 0 
```

运行我们的程序:

```
root@bttrm-dev-console:/home/admin# ./test_vm
main() started
Parent pid: 14353 
```

检查当前进程使用的内存:

```
root@bttrm-dev-console:/home/admin# ps aux | grep test_vm | grep -v grep
root     14353  0.0  0.0   4160   676 pts/4    S+   17:29   0:00 ./test_vm 
```

**4160** VSZ(虚拟尺寸)——正如我们在`malloc(mem_size)`电话中所要求的。

现在–将`mem_size`变量值从 4 _ 096 _ bytes–更改为*1099511627776*–**1tb**:

```
...
long int mem_size = 1099511627776;
... 
```

构建、运行并:

```
root@bttrm-dev-console:/home/admin# ./test_vm
main() started
Segmentation fault 
```

太好了！

用`strace` :
检查

```
root@bttrm-dev-console:/home/admin# strace -e trace=mmap ./test_vm
mmap(NULL, 47657, PROT_READ, MAP_PRIVATE, 3, 0) = 0x7fa268f24000
mmap(NULL, 8192, PROT_READ|PROT_WRITE, MAP_PRIVATE|MAP_ANONYMOUS, -1, 0) = 0x7fa268f22000
mmap(NULL, 3795296, PROT_READ|PROT_EXEC, MAP_PRIVATE|MAP_DENYWRITE, 3, 0) = 0x7fa26896e000
mmap(0x7fa268d03000, 24576, PROT_READ|PROT_WRITE, MAP_PRIVATE|MAP_FIXED|MAP_DENYWRITE, 3, 0x195000) = 0x7fa268d03000
mmap(0x7fa268d09000, 14688, PROT_READ|PROT_WRITE, MAP_PRIVATE|MAP_FIXED|MAP_ANONYMOUS, -1, 0) = 0x7fa268d09000
main() started
mmap(NULL, 1099511631872, PROT_READ|PROT_WRITE, MAP_PRIVATE|MAP_ANONYMOUS, -1, 0) = -1 ENOMEM (Cannot allocate memory)
mmap(NULL, 1099511762944, PROT_READ|PROT_WRITE, MAP_PRIVATE|MAP_ANONYMOUS, -1, 0) = -1 ENOMEM (Cannot allocate memory)
mmap(NULL, 134217728, PROT_NONE, MAP_PRIVATE|MAP_ANONYMOUS|MAP_NORESERVE, -1, 0) = 0x7fa26096e000
mmap(NULL, 1099511631872, PROT_READ|PROT_WRITE, MAP_PRIVATE|MAP_ANONYMOUS, -1, 0) = -1 ENOMEM (Cannot allocate memory)
--- SIGSEGV {si_signo=SIGSEGV, si_code=SEGV_MAPERR, si_addr=0xfffffffff8} ---
+++ killed by SIGSEGV +++
Segmentation fault 
```

而这里是我们可爱的 **-1 ENOMEM ( *无法分配内存* )** ，我们可以在 Redis 日志中看到其“**无法在后台保存:fork: *无法分配内存*”**错误信息。

所以，这里的 [`mmap()`](https://elixir.bootlin.com/linux/v5.0.21/source/mm/mmap.c#L2323) syscall 就是调用 [`security_vm_enough_memory_mm()`](https://elixir.bootlin.com/linux/v5.0.21/source/include/linux/security.h#L497) 函数的:

```
... 
if (security_vm_enough_memory_mm(mm, grow))
   return -ENOMEM;
... 
```

哪个`security.h`头文件中描述的 [`__vm_enough_memory()`](https://rtfm.co.ua/goto/https://elixir.bootlin.com/linux/v5.0.7/source/mm/util.c#L639) 在哪里叫做:

```
...
static inline int security_vm_enough_memory_mm(struct mm_struct *mm, long pages)
{
    return __vm_enough_memory(mm, pages, cap_vm_enough_memory(mm, pages));
}
... 
```

现在–禁用超量承诺限额检查:

```
root@bttrm-dev-console:/home/admin# echo 1 > /proc/sys/vm/overcommit_memory 
```

再次运行程序:

```
root@bttrm-dev-console:/home/admin# ./test_vm
main() started
Parent pid: 11337
Child pid: 11338
Child is running with PID 11338 
```

检查现在使用的 VSZ:

```
admin@bttrm-dev-console:~/redis-logs$ ps aux | grep -v grep | grep 11337
root     11337  0.0  0.0 1073745988 656 pts/4  S+   16:34   0:00 ./test_vm
VSZ == **1073745988** – just awesome: we just allocated 1 TERRABYTE of the virtual memory on the AWS [t2.medium](https://www.ec2instances.info/?selected=t2.medium) EC2 instance with the only 4 Gigabyte of the “real” memory! 
```

现在——猜猜会发生什么，一个子进程会开始积极地使用这个分配的虚拟内存？

添加 [`memset()`](http://man7.org/linux/man-pages/man3/memset.3.html) 系统调用，通过填充整个`mem_size`将 0 设置到我们的`mem_stack`中，例如 1 TB:

```
#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>
#include <string.h>
#include <fcntl.h>
int main() {

    printf("main() started\n");

    long int mem_size = 1099511627776;

    void *mem_stack = malloc(mem_size);

    printf("Parent pid: %lu\n", getpid());

    memset(mem_stack, 0, mem_size);
    sleep(120);
} 
```

运行它(**不要在生产环境中运行它！** ):

```
root@bttrm-dev-console:/home/admin# ./test_vm
main() started
Parent pid: 15219
Killed 
```

并检查操作系统的日志:

> 8 月 27 日 17:46:43 localhost 内核:[7974462.384723]内存不足:终止进程 15219 (test_vm)分数 818 或牺牲子进程
> 
> 8 月 27 日 17:46:43 localhost 内核:[7974462.393395]终止进程 15219 (test_vm)总数-vm:1073745988kB，anon-RSS:34111

OOM 黑仔来到这里，杀了所有人。这个时候。下次可能就来不及了。

注意，我们的进程有时间从给定的 1tb 虚拟内存中消耗掉整个 3.4 GB 的实际内存—*anon-RSS:3411676k*—*total-VM:1073745988 kb*。

### 结论

在我们当前的案例中，当 Redis 仅用于缓存，并且没有启用 RDB 或 AOF 备份时，不需要更改`overcommit_memory`，最好保留默认值 0。

如果你真的想自己设置界限，最好使用`overcommit_memory` == 2，通过设置`overcommit_ratio`或`overcommit_kbytes`参数来限制过度承诺。

### 故事

事实上，我的整个故事始于大约一年前。

当时我对 Redis 还不太熟悉，我刚刚来到已经使用 Redis 的新项目。

在完美的一天——我们的生产服务器(当我刚来这个项目时——整个后端都在唯一的 AWS EC2 上工作)有点累了，去休息了。

在通过 AWS 控制台进行神奇的 dropkick 之后——服务器重新上线，我开始寻找根本原因。

在它的日志中，我发现了一些关于 OOM 黑仔的记录，这些记录被放到了 Redis 或者 RabbitMQ 上——现在还不确定。但无论如何，在调查过程中，我发现`vm.overcommit_memory`被设置为 1，即完全禁用。

总之，这个故事首先给了我一个理由，为我们的后端基础设施创建一个更可靠和容错的架构，其次，教会我不要盲目相信任何文档。

### 有用的链接

*   在 Linux 下，即使我有很多空闲内存，后台保存也会失败，并出现 fork()错误！
*   惯犯坚持
*   [使用克隆启动 Linux 线程和进程](https://eli.thegreenplace.net/2018/launching-linux-threads-and-processes-with-clone/)
*   [叉子去哪了？](https://thorstenball.com/blog/2014/06/13/where-did-fork-go/)
*   什么是过度承诺？为什么不好？
*   [Linux–fork 系统调用及其陷阱](https://devarea.com/linux-fork-system-call-and-its-pitfalls/#.XWVUanVfg3F)
*   [面向 x86 和 x86_64 的可搜索 Linux 系统调用表](https://filippo.io/linux-syscall-table/)
*   [Linux 奶牛](http://eastrivervillage.com/The-Linux-COW/)
*   [物理和虚拟内存](https://www.win.tue.nl/~aeb/linux/lk/lk-9.html)
*   [mtrace(3)–Linux 手册页](https://www.systutorials.com/docs/linux/man/3-mtrace/)
*   [内存不足管理](https://www.kernel.org/doc/gorman/html/understand/understand016.html)
*   [内核过量使用-记账](https://www.kernel.org/doc/Documentation/vm/overcommit-accounting)
*   [Linux 中的虚拟内存设置——过量使用的问题](http://engineering.pivotal.io/post/virtual_memory_settings_in_linux_-_the_problem_with_overcommit/)
*   [存储器–第 1 部分:存储器类型](https://techtalk.intersec.com/2013/07/memory-part-1-memory-types/#Shared_Memory)
*   [内存映射](https://linux-kernel-labs.github.io/master/labs/memory_mapping.html#struct-page)

### 类似的帖子

*   <small>04/03/2019</small> [Redis:无法打开日志文件:只读文件系统](https://rtfm.co.ua/en/redis-cant-open-the-log-file-read-only-file-system-2/)
*   <small>04/10/2019</small>[Redis:Sentinel–bind 0 . 0 . 0 . 0，localhost 问题和 announce-ip 选项](https://rtfm.co.ua/en/redis-sentinel-bind-0-0-0-0-the-localhost-issue-and-the-announce-ip-option/)
*   <small>03/29/2019</small> [Redis:复制，第 1 部分–概述。复制与分片。哨兵 vs 集群。Redis 拓扑。](https://rtfm.co.ua/en/redis-replication-part-1-overview-replication-vs-sharding-sentinel-vs-cluster-redis-topology/)*