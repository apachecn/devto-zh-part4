# 使用 strace 进行调试，第 2 部分

> 原文：<https://dev.to/dbdanilov/using-strace-for-debugging-part-2-1i3o>

这是关于用 strace 调试的第二篇文章。

在我们的解决方案中，我们有一个运行另一个进程的进程。父进程通过 TCP 套接字与子进程通信。
为了知道孩子是否准备好通信，父母读取
的 stdout 并寻找准备就绪指示字符串，例如“我准备好了”。
一切正常，但是突然我和我的同事开始观察到一个奇怪的行为:
子进程过了一会儿就卡住了。

所以我跑了`strace -p 1666`，其中 1666 是孩子的 id。
strace 输出:

```
$sudo strace -p 25485
strace: Process 25485 attached
write(1, "Some important information...."..., 101

```

子进程试图向
stdout(1 是 stdout 的文件描述符)中写入内容，但未能成功！
我试图通过手动运行该流程来重现问题。
令我们惊讶的是，这一期没有再现！所以我开始研究我们的应用程序启动这个过程的方式:

```
cmd = ['./child.py']
handle = subprocess.Popen( cmd, stdout=subprocess.PIPE,
                           stderr = subprocess.STDOUT,
                           close_fds=True, shell=True)
wait_for_child_readiness(handle)
#do_some_real_work() handle.wait() 
```

Enter fullscreen mode Exit fullscreen mode

这看起来很正常:启动一个进程，然后将 stdout 转发给 stderr
,并通过管道将两者传递给父进程。然后父进程读取输出:

```
def wait_for_child_readiness(proc_handle):
        last_stdout = ""
        while "CHILD IS READY" not in last_stdout :
                last_stdout = proc_handle.stdout.read(512)
                sys.stdout.write(last_stdout)
                sys.stdout.flush()
        sys.stdout.write("\nParent detected child's readiness!\n") 
```

Enter fullscreen mode Exit fullscreen mode

然后我们读取输出，直到找到指示字符串。但是等等！子进程继续向 stdout/stderr 写入，管道继续向父进程发送
输出。但是父进程不读取它。
然后管道溢出发生，子进程不能写任何东西到标准输出！

解决方案是继续读取子进程的输出。因此，一旦开始读取子进程输出，就不要停止！；)

另外，了解 Linux 上管道大小的方法是检查:

```
$cat /proc/sys/fs/pipe-max-size
1048576

```

该文件的默认值是 1048576 (1 MiB)

[http://man7.org/linux/man-pages/man7/pipe.7.html](http://man7.org/linux/man-pages/man7/pipe.7.html)