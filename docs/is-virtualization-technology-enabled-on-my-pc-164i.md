# 我的电脑上是否启用了虚拟化技术？

> 原文：<https://dev.to/lautarolobo/is-virtualization-technology-enabled-on-my-pc-164i>

对虚拟机感兴趣？你需要知道你的电脑是否能运行它们？嗯，你来对地方了！

首先是永不失败的一个:

```
lscpu 
```

这个命令显示了关于您的 PC 的许多信息，实际上是关于您的 CPU 架构的信息，显示了 CPU 的编号、型号、系列和制造商 ID，以及线程、内核、插槽、节点等等。您应该搜索包含虚拟化关键字的行(不要担心，很容易找到)。

其实就是这样。那个命令*永远不会*失败。但是这里有另一种选择，因为有些时候不可预知的事情会发生。

下一个，你会得到' vmx '(英特尔)或' smv' (AMD)的输出。希望是有色的。

```
egrep "(svm|vmx)" /proc/cpuinfo 
```

如果它没有用颜色显示这些关键词，不要担心，我会保护你:

```
egrep --color -i "svm|vmx" /proc/cpuinfo 
```

这里，最后一个。如果没有成功，不要失去希望！这个就够了。

有些操作系统预装了 cpu-checker 软件包。这可能不是你的情况，所以你可以用
快速简单地安装它

```
sudo apt-get install cpu-checker 
```

然后，让奇迹发生:

```
sudo kvm-ok 
```

如果您启用了虚拟化，您会看到类似于
的内容

```
INFO: /dev/kvm exists
KVM acceleration can be used 
```

否则:

```
INFO: Your CPU does not support KVM extensions
KVM acceleration can NOT be used 
```

* * *

好了，暂时就这样了。任何想法请写在评论里。下次见！