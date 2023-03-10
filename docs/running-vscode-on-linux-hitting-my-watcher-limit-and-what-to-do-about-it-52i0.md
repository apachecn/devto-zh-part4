# 在 Linux 上运行 VSCode:达到我的观察器极限以及如何应对

> 原文：<https://dev.to/stephencweiss/running-vscode-on-linux-hitting-my-watcher-limit-and-what-to-do-about-it-52i0>

我最近一直在开发一个 Linux VM，关于它的一件特别恼人的事情(有许多令人沮丧的小怪癖)是，VSCode 不能监控变化，因为它不在观察器之内。

观察者是`inotify` Linux 内核子系统 <sup>1</sup> 的一部分，它扩展文件系统以注意变化并向监听它们的应用程序报告这些变化。 <sup>2</sup>

然而，有固定数量的观察者，很明显，我的机器的默认值(8192)不足以完成任务。

解决方法相对简单:增加观察者的数量。回购有一个关于这个过程的信息帖子。下面引用 <sup>3</sup> :

> **技术细节**
> 
> Listen 在 Linux 上默认使用 inotify 来监控目录的变化。遇到系统限制您可以监控的文件数量的情况并不少见。例如，Ubuntu Lucid(64 位)的 inotify 限制设置为 8192。
> 
> 您可以通过执行
> 
> `$ cat /proc/sys/fs/inotify/max_user_watches`来获得您当前的 inotify 文件监控限制
> 
> 当此限制不足以监控目录中的所有文件时，必须增加限制以使 Listen 正常工作。
> 
> 您可以用
> 
> `$ sudo sysctl fs.inotify.max_user_watches=524288`
> 
> `$ sudo sysctl -p`设置新的临时限制
> 
> 如果你想让你的限制永久化，使用:
> 
> `$ echo fs.inotify.max_user_watches=524288 | sudo tee -a /etc/sysctl.conf`
> 
> `$ sudo sysctl -p`
> 
> 如果 Listen 一直抱怨，您可能还需要注意 max_queued_events 和 max_user_instances 的值

在做了这些更改之后，我的 VSCode 再次能够跟踪我的更改，这反过来意味着源代码控制特性实际上是有用的，我不必使用终端来查看我的差异。

## 脚注

*   单词，它们很难，我不确定我是否确切知道它们的意思。
*   <sup>2</sup>[inotify-Wikipedia](https://en.wikipedia.org/wiki/Inotify)
*   <sup>3</sup>