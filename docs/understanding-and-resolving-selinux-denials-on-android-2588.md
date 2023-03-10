# 了解和解决 Android 上的 SELinux 拒绝

> 原文：<https://dev.to/msfjarvis/understanding-and-resolving-selinux-denials-on-android-2588>

这篇博文主要是由我曾经的一个要点组成的。我认为它需要一些额外的阅读来了解我们到底在处理什么，所以增加了一个概述 SELinux 是什么以及如何理解我们正在编写的规则的序言。要点本身来自于我知识少得多的时候，因此做了相应的调整，经常是用额外的信息。

### 什么是 SELinux？

SELinux 是安全增强的 Linux 的缩写。它是内置在 [Linux 内核](http://github.com/torvalds/linux/tree/master/security/selinux)中的一个安全特性，通过由系统管理员实施的预定义安全策略来加强对程序的访问控制。

传统上，Linux 上的访问控制是在**自主访问控制(DAC)** 方法下完成的。在 DAC 下，每个进程在一个用户和组下运行。进程可以访问其用户和/或组拥有的所有文件。

[nixCraft](https://cyberciti.biz) 有一个以 httpd 为中心的例子，突出了 DAC 和 MAC 的区别。

<center>![Compromised daemon under DAC](img/2f23c35b6bc1f8ca2baeeb90086048f7.png)</center>

SELinux 是一种 MAC 安全机制的实现。MAC 代表**强制访问控制**，这种模式允许限制一个流程的多个方面，如

*   管道
*   文件
*   网络端口
*   套接字
*   目录
*   其他流程

SELinux 建立在基于 DAC 的限制之上，并在 UID 和 GID 之上添加了额外的限制层，以确保被入侵的用户仍然可以被限制做通常不需要做的事情。

### 如何检测和解决 Android 上的 SELinux 拒绝

这里有一个我们将尝试解决的拒绝示例。

```
avc: denied { read write } for pid=29059 comm="i.tetherservice" name="ipa" dev="tmpfs" ino=11991
scontext=u:r:system_app:s0 tcontext=u:object_r:ipa_dev:s0 tclass=chr_file permissive=0 
```

Enter fullscreen mode Exit fullscreen mode

解决它的策略规则:

```
allow system_app ipa_dev:chr_file {read write}; 
```

Enter fullscreen mode Exit fullscreen mode

我是怎么写的？

首先，您需要识别试图进行拒绝操作的进程/设备。

`scontext`中的值是违例者。在我们的例子中，它是 **system_app** 。

接下来，找到试图访问的域。这是通过获取 tcontext 的值，在 **:** 分隔符上将其拆分，并获取索引 2，或从右数第二个值来找到的。将它与 tclass 的值相结合，就可以得到这个域，在我们的例子中是 **ipa_dev:chr_file** 。

最后，找到正在执行的操作。这相当简单。如果你看到`avc: denied { read write }`，那么**{读写}** 就是这里的动作。

把这些放在一起，你就得到最终的政策规则。

### SELinux 中的标签

为了定义添加策略排除的新类型，我们使用了标签。

对于这个例子，让我们假设我们想要在`sysfs_kcal`类型下标记 KCal sysfs 节点。

KCal sysfs 节点位于`/sys/devices/platform/kcal_ctrl.0/`目录中。为了将它们放在一个标签下，我们使用一个简单的正则表达式。类型名称应添加到设备树的 sepolicy 文件夹中的`file_contexts`。如果它不存在，用以下内容创建它(如果文件存在，则追加)。

`/sys/devices/platform/kcal_ctrl.0(/.*)? u:object_r:sysfs_kcal:s0`

`file_contexts`中的这一行将把`/sys/devices/platform/kcal_ctrl.0/`目录中的所有文件标记为`sysfs_kcal`。

接下来，我们打开要授予标签访问权限的域的 sepolicy 文件。对于这个例子，域是 **system_app** ，所以我们打开 **system_app.te** 。和以前一样，不存在就创建。

`type sysfs_kcal, fs_type, sysfs_type;`

在那里，我们现在可以为`sysfs_kcal`添加排除项，将其视为 sysfs 节点以及文件系统项目，如文件夹或文件。

为了能够读写 sysfs 节点，我们需要先找到它。然后我们需要授予`system_app`读写它的能力。

`allow system_app sysfs_kcal:dir search;` `allow system_app sysfs_kcal:file rw_file_perms;`

就是这样！现在，所有系统应用程序都可以读取和写入`/sys/devices/platform/kcal_ctrl.0/`目录中的所有节点。

### 补充阅读

*   [AOSP——撰写 SELinux 政策](https://source.android.com/security/selinux/device-policy)
*   [RedHat -理解 SELinux](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/4/html/SELinux_Guide/selg-part-0057.html)