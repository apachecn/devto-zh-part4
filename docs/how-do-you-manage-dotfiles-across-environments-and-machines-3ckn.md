# 如何跨环境和机器管理点文件

> 原文：<https://dev.to/rpalo/how-do-you-manage-dotfiles-across-environments-and-machines-3ckn>

我正在寻找一种更好的方法来跨多个环境管理我的点/配置文件。

## 我目前在做什么

我有一个 dotfiles git repo，其中有一些构建脚本，可以动态构建文件，检测操作系统，并相应地填充模板。这个还行。算是吧。它可以更好。缺点是，我总是对我的点文件(特别是我的`.vimrc`和`.zshrc`文件)做非常小的调整，所以单个实例很快就分开了。这是因为我在做了一个小的改变后总是忘记(读:我太懒了)做“提交/推送”，每当我在不同的工作站坐下来时总是忘记“拉/构建/部署”。因此，我几乎总是从一个过时的版本为任何给定的新工作站重写 dotfile。太逊了。

## 我喜欢的功能

1.  存储我的配置文件的非本地中心位置。
2.  一种使它们特定于操作系统或机器的方法。例如，我在 Mac 上有一个别名，将`rm`改为`trash`,这样删除起来更符合人体工程学。我在 Linux 服务器上不一定需要这个，在 Windows 机器上肯定也不需要。
3.  理想情况下，它应该能够稳健地处理微小的调整，以便在更改后(或定期)同步，而无需我键入一堆 git 命令。
4.  如果能轻松部署到新的机器上就好了。这意味着，如果它使用一个构建或部署脚本，它应该无需预先安装一堆东西就可以运行，因为那样会违背初衷。我目前使用的是 Ruby 脚本，但是，正如你可能知道的，Ruby 的设置一点也不简单，而且它在不同的操作系统之间也不一致。
5.  有些部分(密钥、用户名等。)是完全配置一台机器所需要的，但我不一定要把它们公之于众。我不知道如何把秘密/私人的东西和公开的东西分开。

不管怎样，我很乐意根据需要实现尽可能多的自定义脚本。事实上，我超级喜欢它。我只是在寻找一些想法。