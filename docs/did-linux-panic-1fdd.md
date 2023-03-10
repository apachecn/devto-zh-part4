# Linux 慌了吗？

> 原文：<https://dev.to/lbonanomi/did-linux-panic-1fdd>

一个 Linux 主机就像一个酒吧里的花花公子:在输入了足够多的坏信息后，它会惊慌失措，吐得鞋子到处都是，然后试图继续下去，就像什么都没发生过一样。

*让我们使用`last`工具检查 wtmp 登录记录，看看最后一次重启是故意的还是主机自动从崩溃中恢复。*

Linux 用户登录的详细信息存储在(二进制！)文件`/var/log/wtmp`并用`/bin/last`实用程序读取。输出显示登录的用户、他们的源主机或 IP、他们的登录时间以及他们的注销时间或状态“仍然登录”:

```
VM: /bin/last
lbonanomi pts/0        bastion_host Tue Sep 10 08:04   still logged in lbonanomi pts/0        bastion_host Tue Sep 10 07:39 - 07:43  (00:03) 
```

现在默认情况下`last`只显示真实用户，但是使用`-x`参数将显示真实用户和*伪*用户的登录。作为正常操作的一部分，`/sbin/shutdown`和`/sbinb/reboot`都将它们的名字写入 wtmp 记录，所以 wtmp 日志记录了这样一次有意的重启:

```
reboot   system boot  3.10.0-229.el7.x Sat Sep  7 02:31 - 09:27 (3+06:56)
shutdown system down  3.10.0-229.el7.x Sat Sep  7 02:31 - 02:31  (00:00) 
```

用户“关机”登录后紧接着用户“重启”。来自用户“reboot”而不是立即*的条目，后跟来自用户“shutdown”的条目，应被视为足够可疑，足以引起管理员的注意。*

 *将这部分 shell 功能放入/etc/rc.local，检查崩溃恢复，并添加钩子来禁用任何会导致争用的服务:

```
(
  last -x -f $(ls -1t /var/log/wtmp* | head -2 | tail -1);  
  last -x -f /var/log/wtmp
) | grep -A1 reboot | head -2 | grep -q shutdown || echo "panic-boot" 
```*