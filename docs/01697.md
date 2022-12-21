# 使用 Hyper-V 时出现 Git 错误

> 原文：<https://dev.to/arturolinares/git-errors-when-using-hyper-v-21fb>

在建立一个[实验性 Drupal 开发环境](https://arturo.linar.es/en/blog/drupal-development-windows)时，我开始在 Linux VM 中使用 git 时遇到一些问题。我无法克隆存储库，因为我收到一些错误，说远程主机意外挂起，或者无法推送更改。然而，我能够克隆浅层回购，但奇怪的是，Bitbucket 和 Gitlab 似乎更容易出现这些错误。

```
$ git clone git@....
Cloning into 'somedir'...
error: RPC failed; curl 56 GnuTLS recv error (-12): A TLS fatal alert has been received.
fatal: The remote end hung up unexpectedly 
```

在某种程度上，禁用卸载解决了这个问题:[在使用 Netplan 和/或 systemd-networkd 的 Ubuntu 18.04 上禁用卸载](https://michael.mulqueen.me.uk/2018/08/disable-offloading-netplan-ubuntu/)

这显然有效，但很快我发现只有在使用 Github 时才有效。当然，这没有任何意义。当我试图将更改推送到 Bitbucket 或 Gitlab 时，我再次遇到同样奇怪的错误，说远程主机意外挂起。

我尝试使用`GIT_SSH_COMMAND`调试 git，尝试使用 https，甚至重新编译 git 以使用 OpenSSL 而不是 GnuTLS。似乎什么都不管用...直到我从[这个找到解决方案的家伙](https://stackoverflow.com/a/56946337/547972)那里找到了答案:我需要更新我的 Wi-Fi 驱动程序。

确实是奇怪的错误。

**TLDR**:Hyper-V 和我的 Wi-Fi 卡(*英特尔 5060* )之间的一个错误导致了这一点。更新驱动程序解决了这个问题:

[https://download center . Intel . com/download/28876/Windows-10-Wi-Fi-Drivers-for-Intel-Wireless-Adapters？v=t](https://downloadcenter.intel.com/download/28876/Windows-10-Wi-Fi-Drivers-for-Intel-Wireless-Adapters?v=t)