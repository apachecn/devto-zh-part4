# Windows 笔记本电脑双引导 Ubuntu Budgie

> 原文：<https://dev.to/funkyidol/dual-boosting-ubuntu-budgie-on-windows-laptop-4a55>

我对在戴尔 Inspiron 15 7000 系列笔记本电脑上并行安装 Ubuntu Budgie 到 Windows 10 的过程的想法。

预配置的戴尔 windows 机器不便于您双启动 Linux。我真的不得不在 BIOS 里乱搞，让它工作。

我遵循的步骤:

1.  禁用安全启动。
2.  更改启动顺序
3.  将 SSD 配置从 RAID 更改为 AHCI([https://www.youtube.com/watch?v=9ngnIKqPOc4](https://www.youtube.com/watch?v=9ngnIKqPOc4))

所有这些都造成了很多摩擦，并且不得不到处搜索才能到达这一点，这对新用户和有经验的人来说都不是空闲的。

我的内置 nVidia GPU 给了我一个非常艰难的整个安装过程。每次我登录 Linux 时，我的机器都变成空白。我最初不明白发生了什么&多次重新安装 Linux 只是为了确保安装正常。

再次经过大量的谷歌搜索，我发现这是一个 nVidia 驱动程序的问题，所以我必须

1.  登录前转到命令行
2.  从 CLI 设置我的 wifi
3.  添加驱动程序报告，从 CLI 安装和配置驱动程序，所有这一切都在 4k 显示器上进行，没有字体缩放，因此可以在那里直接阅读。

只有当发行版忽略 nVidia 硬件，除非安装了驱动程序，这种情况才能得到解决。由于许可问题，bcoz 发行版不能捆绑专有驱动程序。

在成功安装并运行之后，我在 Ubuntu Budgie 的 windows 中少了一些东西

1.  派系显示缩放。不是 100%就是 200%。没有介于两者之间的
2.  概念的原生应用程序，谷歌驱动备份和同步。但是这个列表在 4-5 年前要长得多，所以这是一个很好的迹象，表明了这些年来生态系统进化了多少。

这一切只是最初的想法。我确信他们会进化。总的来说一切都很好。我对这一举动的期望是一个安全的&开发友好的工作环境和更好的编译速度。尽管如此，我仍然没有屏息以待电池寿命的提高。

编辑:
从我的 twitter 朋友那里获得了一些帮助，他们分享了以下链接来帮助解决一些问题
[https://www . omgubuntu . co . uk/2019/06/enable-fractional-scaling-Ubuntu-19-04](https://www.omgubuntu.co.uk/2019/06/enable-fractional-scaling-ubuntu-19-04)
T5】https://www . omgubuntu . co . uk/2019/02/odrive-Google-drive-Linux-client
[https://www . omgubuntu](https://www.omgubuntu.co.uk/2019/05/slimbook-battery-optimizer-ubuntu)

我找到了一个有用的链接:[https://medium . com/@ pwa terz/how-to-dual-boot-windows-10-and-Ubuntu-18-04-on-the-15 英寸-Dell-XPS-9570-with-NVIDIA-1050 ti-GPU-4b 9a 2901493d](https://medium.com/@pwaterz/how-to-dual-boot-windows-10-and-ubuntu-18-04-on-the-15-inch-dell-xps-9570-with-nvidia-1050ti-gpu-4b9a2901493d)