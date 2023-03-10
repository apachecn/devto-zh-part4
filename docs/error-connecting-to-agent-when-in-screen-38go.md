# 在屏幕中连接到代理时出错

> 原文：<https://dev.to/suntong/error-connecting-to-agent-when-in-screen-38go>

### 情况

前几天，当我连接到一个旧的`screen`会话时，我得到了一个*“错误连接到代理”*错误，但是当同时搜索“错误连接到代理”*和*“屏幕”时，没有找到关于如何修复它的好文章。所以我决定写下来，这样下一个人就知道将来如何解决这样的问题。

### 解

实际上，解决方案很简单，下面是具体的操作方法。

*   在连接到旧的`screen`会话之前，执行`echo "$SSH_AUTH_SOCK"`。
*   连接到旧的`screen`会话后，再次执行`echo "$SSH_AUTH_SOCK"`。您应该会看到这两种情况下的值是不同的。
*   用正确的值固定`SSH_AUTH_SOCK`，即连接到`screen`之前的值。

这应该可以解决问题。

再一次

```
# after logged into remote host, 
$ echo "$SSH_AUTH_SOCK"
/tmp/ssh-2HsGhzu77S/agent.125711

# attaching to the old `screen` session
screen -r old_session

# get into screen
$ echo "$SSH_AUTH_SOCK"
/tmp/ssh-vosa42Ualz/agent.22985

$ ssh-add -l
Error connecting to agent: No such file or directory

# The SSH_AUTH_SOCK is no longer there. Point to the new one
SSH_AUTH_SOCK=/tmp/ssh-2HsGhzu77S/agent.125711 
```

Enter fullscreen mode Exit fullscreen mode

现在，用另一个`ssh-add -l`进行验证，它现在应该输出在连接到旧的`screen`会话之前的内容。

一切就绪！

### 信用

封面图片来自 [TechRepublic](https://www.techrepublic.com/article/how-to-use-ssh-agent-to-make-working-with-secure-shell-more-efficient/) 。