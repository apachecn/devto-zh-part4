# Ubuntu 的另一个 Gksu 替代品(Xorg)

> 原文：<https://dev.to/logix2/another-gksu-alternative-for-ubuntu-xorg-2356>

回到 Debian 和 Ubuntu [移除 gksu 包](https://jeremy.bicha.net/2018/04/18/gksu-removed-from-ubuntu/)的时候，这个包是用来在运行图形应用程序时提升你的权限的，建议是:

*   让应用程序开发人员使用 PolicyKit，并且只对特定操作使用提升的权限
*   对于用户来说，利用 gvfs 管理后端，通过使用前缀`admin://`的

不过在 Ubuntu 上使用`admin://`有一个问题——当你第一次在会话中使用它时，它会要求你输入两次密码。这在 Fedora 中不会发生，但在 Ubuntu 18.04 和 19.04 中都会发生。

那么，gksu 在 Ubuntu 上使用默认 Xorg 会话时，有什么好的替代方案，它不会要求输入密码两次？一个不依赖于您所使用的 Linux 发行版或桌面环境(您确实需要安装 PolicyKit)的替代方法是下面的命令，它适用于您希望以提升的权限 root 身份运行的任何图形应用程序(GUI):

```
pkexec env DISPLAY=$DISPLAY XAUTHORITY=$XAUTHORITY DBUS_SESSION_BUS_ADDRESS=$DBUS_SESSION_BUS_ADDRESS` 
```

该命令导出您当前的`$DISPLAY`、`$XAUTHORITY`和`$DBUS_SESSION_BUS_ADDRESS`环境变量，允许您以 root 身份运行任何 GUI 应用程序(不需要这些应用程序的 PolicyKit 文件)，并且不需要像 Ubuntu 上的`admin://`那样要求输入两次密码。

为了更容易使用，因为这个命令很长，您可以为这个命令创建一个别名，称为`gksu`。

为了使这个`pkexec env DISPLAY=$DISPLAY XAUTHORITY=$XAUTHORITY DBUS_SESSION_BUS_ADDRESS=$DBUS_SESSION_BUS_ADDRESS`命令的`gksu`别名永久化，将以下内容添加到您的`~/.bashrc`文件中:

```
alias gksu='pkexec env DISPLAY=$DISPLAY XAUTHORITY=$XAUTHORITY DBUS_SESSION_BUS_ADDRESS=$DBUS_SESSION_BUS_ADDRESS' 
```

您可以通过发出以下命令将它添加到`~/.bashrc`(只运行一次该命令，因为它会在您每次运行时添加这一行):

```
echo "alias gksu='pkexec env DISPLAY=\$DISPLAY XAUTHORITY=\$XAUTHORITY DBUS_SESSION_BUS_ADDRESS=\$DBUS_SESSION_BUS_ADDRESS'" >> ~/.bashrc 
```

此后，来源`~/.bashrc` :

```
. ~/.bashrc 
```

现在，您可以像使用旧的 gksu 命令一样运行这个别名。

例如，您可以使用它以提升的权限运行 Gedit(替换为您希望的任何其他 GUI 文本编辑器)并编辑/etc/default/grub 文件:

```
gksu gedit /etc/default/grub 
```

另一个例子——运行 Nautilus(或其他文件管理器)并打开系统主题文件夹:

```
gksu nautilus /usr/share/themes 
```

关于我写的更多文章，请查看 Linux &开源博客[LinuxUprising.com](https://www.linuxuprising.com/)。