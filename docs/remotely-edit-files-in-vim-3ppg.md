# 在 Vim 中远程编辑文件

> 原文：<https://dev.to/serhatteker/remotely-edit-files-in-vim-3ppg>

如果你想用你当前可爱的 vim 配置编辑远程服务器上的文件，有两个选择:

一种是把你的`.vimrc`复制到所有服务器上。哪个不是效率低下并且否定了软件开发的一个主要原则——[干](https://en.wikipedia.org/wiki/Don%27t_repeat_yourself)，

另一种方法是不要把`.vimrc`带到你需要工作的每个服务器上，而是从你的本地`vim`编辑远程文件:

开始`vim`这样:

```
$ vim scp://remoteuser@host//path/to/file/file.extension 
```

Enter fullscreen mode Exit fullscreen mode

如果您已经有一个`vim`会话:

```
:e scp://remoteuser@host//path/to/file/file.extension 
```

Enter fullscreen mode Exit fullscreen mode

*   `remoteuser`是远程服务器上的用户名
*   `host`是远程服务器名称/ip

这将无缝地打开文件(它实际上在本地复制文件)，当您保存时，它会将编辑过的文件发送回服务器。

它要求输入 ssh 密码，但这可以通过 ssh 密钥简化。

这种方法的唯一缺点是不能像直接在机器上工作时那样获得路径/文件的竞争，这除了优点之外没什么大不了的。

完成了。

* * *

**变更日志**

*   2020-11-06:更改标题和文件名。添加 scp 标签。