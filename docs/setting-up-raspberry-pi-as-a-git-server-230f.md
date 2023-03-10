# 将 Raspberry Pi 设置为 git 服务器

> 原文：<https://dev.to/kodaman2/setting-up-raspberry-pi-as-a-git-server-230f>

6 月 6 日更新:根据评论，我已经安装了 gitea 来测试它。太棒了。如果你想要一个前端的话，他们的文档可以很好的帮助你开始。如果你不继续写这篇文章。[Gitea Docs](https://docs.gitea.io/en-us/install-from-binary/)T3】

**6 月 2 日，8 日更新:我用我的 pi 设置了我的 UPS APC 600va，安装了 apcupsd 实用程序，编辑了配置文件，并使用了 usb 数据端口。这样，pi 将在断电和/或电涌的情况下正常关闭。确保在启动时将它设置为服务。[此处引导](https://www.cyberciti.biz/faq/debian-ubuntu-centos-rhel-install-apcups/)， [apcupsd 文档](http://www.apcupsd.org/manual/manual.html) )**

> ![Jason Rohrer profile image](img/c0d9f8974093a7734b35799b03c96daf.png)杰森·罗勒@杰森·罗勒![twitter logo](img/ad0c7b03deabfe1a161345efb2d537eb.png)如果你在考虑用 [@github](https://twitter.com/github) 来做你毕生的工作，仅供参考，他们可能会在没有任何警告或通知的情况下移除它，基于一些出于恶意的用户“报告”。这发生在今天，因为我在那里主持了 5 年多的一小时一生活活动。他们甚至没有给我发邮件。02:09AM-05 Jun 2019[![Twitter reply action](img/269095962147c28351274afdd5486a48.png)](https://twitter.com/intent/tweet?in_reply_to=1136092621149880320)[![Twitter retweet action](img/771160ecf06ae3d4d7a7815c29c819c2.png)](https://twitter.com/intent/retweet?tweet_id=1136092621149880320)514[![Twitter like action](img/c077611ab2a5e0b4cd0c826ee7ae1e48.png)](https://twitter.com/intent/like?tweet_id=1136092621149880320)1353

几天前我注意到了这条推文，并对如何将 raspberry pi 设置为 git 服务器产生了一些兴趣。这样的话，如果 Github 出了问题，你仍然可以在另一个地方有一个完整的 git 项目。Github 首席执行官回应说回购已经恢复，我不认为他会回复我，如果我失去了一个回购 lol，但尽管如此，github 团队正在全力以赴，我给他们支持。

我家里总是有可用的 pi 和 arduinos 或微控制器，所以不妨把我的 pi 用于一些真正的工作。我确实有一些回购，我把一些很好的工作，所以有另一个完整的备份会很好。

系好安全带，我花了几个小时才完成全程，但那是因为我有些小问题，所以在这个指南中，我希望你能比我更快地启动并运行。随意把 vim 换成 nano:)

***注意:我既不是 linux 专家，也不是 git 专家，所以如果我做错了，请告诉我。*T3】**

## 你将需要的东西

*   树莓 Pi 3
*   如果您希望数据存储在 u 盘而不是 Pi SD 卡上，可以使用 USB 盘
*   一个[无 IP 账户](https://www.noip.com/free)如果你有 ISP 动态 IP
*   只需要一点点技术知识

## 指南概述

*   SSH 启用
*   静态 IP
*   SSH 端口更改(可选)
*   路由器端口转发
*   DNS 服务器(可选)
*   USB 安装(可选)
*   克隆存储库或初始化新存储库
*   授予 git 目录 pi:pi 权限
*   请改为添加 receive . denycurrentbranch update
*   将 pushurl 添加到原点
*   测试您的设置，并在所有这些艰苦的工作后喝点咖啡！

## 启用 SSH

默认情况下，SSH 是禁用的，一旦启用，默认端口是 22。

1.  在终端窗口中输入`sudo raspi-config`
2.  选择`Interfacing Options`
3.  导航并选择`SSH`
4.  选择`Yes`
5.  选择`Ok`
6.  选择`Finish`

## 静态 IP

需要一个静态 IP 来配置端口转发。在终端上用自己的 pi 地址 ip 替换运行 ***ifconfig***

```
 sudo vim /etc/dhcpcd.conf 
```

```
# Example static IP configuration
interface eth0
static ip_address=192.168.0.28
#static ip6_address=fd51:42f8:caae:d92e::ff/64
static routers=192.168.0.1
static domain_name_servers=192.168.0.1 
```

## SSH 端口(可选)

不需要更改端口，但是如果您的网络上有另一台机器，建议您更改端口。

```
sudo vim  /etc/ssh/sshd_config 
```

更改端口
后

```
sudo service ssh restart 
```

## 路由器端口转发

这是你可能需要谷歌为你的路由器得到明确的指示。也可以下载你的路由器手册。进入浏览器输入您的路由器 IP，凭证，高级设置，并寻找端口转发。我们需要一个 TCP 服务，给它一个名字，你的 pi IP 和端口。在尝试远程 SSH 之前，确保您可以在本地 SSH 到它。
 [这里有一个夜鹰](https://kb.netgear.com/24289/How-do-I-set-up-port-forwarding-on-my-Nighthawk-router)的例子

## DNS 设置(可选)

大多数 ISP 会改变你的路由器 IP，我的是每隔几周左右。因此，为了能够将更改推送到 pi 上的 repo，您需要一个静态地址，因为您不能依赖于不断变化的路由器 ip。在 [NoIP](https://www.noip.com/) 注册一个账户，并按步骤操作。

一旦你有了你的 dns 地址，把它放入你的路由器。如果您的路由器没有 dns 地址的能力，那么从 NoIP 下载一个实用程序，它可以更新您的路由器 IP，但请注意，该实用程序需要在您网络上的机器上运行。

对我来说，我不能在工作中使用我的自定义 dns 地址，所以我仍然必须使用我的路由器 IP。

## u 盘挂载

如果您想将 git 存储库保存在拇指驱动器或硬盘驱动器上，您需要首先挂载它。否则跳过这一部分。

**lsblk** 或 **sudo fdisk -l** 将显示磁盘设备。我的是分区 sda1 上的 sda，如下图。请注意，如果已经装载了，lsblk 也会显示装载点。

```
pi@raspberrypi:~$ lsblk
NAME        MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
sda           8:0    1   15G  0 disk
└─sda1        8:1    1   15G  0 part /media/usbstick
mmcblk0     179:0    0 14.9G  0 disk
├─mmcblk0p1 179:1    0  1.6G  0 part
├─mmcblk0p2 179:2    0    1K  0 part
├─mmcblk0p5 179:5    0   32M  0 part /media/pi/SETTINGS
├─mmcblk0p6 179:6    0   69M  0 part /boot
└─mmcblk0p7 179:7    0 13.2G  0 part / 
```

**安装 usb:**

```
sudo mount /dev/sda1 /mount/path 
```

最后通过运行 **lsblk** 或 **cat /proc/mounts** 检查是否挂载

格式化:警告:确保你在正确的驱动器上。拔下并插上您的驱动器，然后用 cat/var/log/messages | tail-n 50 检查品牌和大小

如果您想要格式化您的 USB，请遵循以下步骤:

*   sudo fdisk /dev/sda1
    *   m，求助
    *   q 代表出口
    *   d .要删除分区，如果有更多分区，即 sda2、sda3 等，请重复上述操作...
    *   为了创建新的分区，我做了 p，并按了 4 次 enter。
    *   w，结束

USB 资源:
[https://dev tidbits . com/2013/03/21/using-USB-external-hard-disk-flash-drives-with-to-your-raspberry-pi/](https://devtidbits.com/2013/03/21/using-usb-external-hard-disk-flash-drives-with-to-your-raspberry-pi/)

## 克隆一个回购

我的拇指驱动器 git 目录是 **/media/usbstick/git/** 我假设这个网站上的大多数人都很擅长 git，但我会尝试给出所有步骤。

```
git clone https://github.com/yourAccount/YourRepo.git 
```

## 克隆私人回购

生成 SSH 密钥，完成后只需点击它，并粘贴到 github:

```
cd ~/.ssh
ls id_*
ssh-keygen -t rsa -C "your_email@example.com" 
```

添加 SSH 密钥[https://help . github . com/en/articles/add-a-new-SSH-Key-to-your-github-account](https://help.github.com/en/articles/adding-a-new-ssh-key-to-your-github-account)

```
git clone git@github.com:kodaman2/RigInfo.git 
```

## 目录权限

您的文件夹可能需要 pi 用户的权限。

```
sudo chown -R pi:pi /your/git/folder 
```

## 改为添加 receive.denyCurrentBranch 更新

如果您想在您的机器上看到下面的错误，或者如果您知道另一种使远程推送工作的方法，您可以跳过这一步:

**`receive.denyCurrentBranch updateInstead`**T3】

```
git config --local receive.denyCurrentBranch updateInstead 
```

如果你不这样配置，你会得到下面的错误。

```
Total 3 (delta 2), reused 0 (delta 0)
remote: error: refusing to update checked out branch: refs/heads/master
remote: error: By default, updating the current branch in a non-bare repository
remote: is denied, because it will make the index and work tree inconsistent
remote: with what you pushed, and will require 'git reset --hard' to match
remote: the work tree to HEAD.
remote:
remote: You can set 'receive.denyCurrentBranch' configuration variable to
remote: 'ignore' or 'warn' in the remote repository to allow pushing into
remote: its current branch; however, this is not recommended unless you
remote: arranged to update its work tree to match what you pushed in some
remote: other way.
remote:
remote: To squelch this message and still keep the default behaviour, set
remote: 'receive.denyCurrentBranch' configuration variable to 'refuse'.
To ssh://yourip:yourport/media/usbstick/git/Data_Preserve/.git
 ! [remote rejected] master -> master (branch is currently checked out)
error: failed to push some refs to 'ssh://pi@yourip:yourport/media/usbstick/git/Data_Preserve/.git' 
```

**此配置上的资源:**
[https://stack overflow . com/questions/1764380/how-to-push-to-a-non-bare-git-repository](https://stackoverflow.com/questions/1764380/how-to-push-to-a-non-bare-git-repository)

[这个选项](https://github.com/git/git/blob/v2.3.0/Documentation/config.txt#L2155)是在 Git 2.3 中添加的[，它让服务器更新它的工作树，如果它是干净的。](https://github.com/blog/1957-git-2-3-has-been-released)

## 将 pushurl 添加到原点

```
git remote set-url --add --push origin ssh://pi@yourIp:yourPort/media/usbstick/git/yourRepo/.git 
```

你也可以进入。git 文件夹，并在配置文件上执行，根据需要更改路径，pi 是用户，因此如果需要，可以更改:
**pushurl = ssh://pi @ yourIp:your port/media/USB stick/git/your repo/。吉特**

如果你愿意，你也可以添加另一个遥控器，这取决于你，但这是通过用一个命令同时推送 github 和 pi 来实现的。

## 测试您的设置

希望您已经完成了，现在您可以将您的更改发布到您的 pi 中了。

在你的远程计算机上，做一个小的改变，提交，然后推。您将需要输入您的 pi 密码，我希望这不是默认密码，如果是，请更改:

```
C:\Users\MyAccount\IdeaProjects\Data_Preserve>git push
Enumerating objects: 5, done.
Counting objects: 100% (5/5), done.
Delta compression using up to 8 threads
Compressing objects: 100% (3/3), done.
Writing objects: 100% (3/3), 290 bytes | 290.00 KiB/s, done.
Total 3 (delta 2), reused 0 (delta 0)
remote: Resolving deltas: 100% (2/2), completed with 2 local objects.
To https://github.com/kodaman2/Data_Preserve.git
   e1e6f2b..3434e4d  master -> master
pi@myRouterIP's password:
Enumerating objects: 5, done.
Counting objects: 100% (5/5), done.
Delta compression using up to 8 threads
Compressing objects: 100% (3/3), done.
Writing objects: 100% (3/3), 290 bytes | 290.00 KiB/s, done.
Total 3 (delta 2), reused 0 (delta 0)
To ssh://myRouterIP:myPort/media/usbstick/git/Data_Preserve/.git
   e1e6f2b..3434e4d  master -> master 
```

SSH 进入您的 pi，并像往常一样确保您的 pi repo 是最新的:

```
git status
git log 
```

## 问题

根据您使用基于 unix 的系统、路由器和 git 的经验，您可能会遇到太多的问题，但是如果您遇到错误，应该有人可以在这里或 google 上帮助您。然而，在这个过程中，你可能会遇到一些问题。

如果您不能从远程 SSH 到您的 pi，您很可能没有正确配置端口转发或者有防火墙问题。如果您得到密码提示，那么您知道您至少连接到您的 pi。

如果 git 推送在 total 之后停止，则很可能是文件夹读/写传输问题，请确保 git 目录中列出了您的用户。您可以通过执行 **ls -la** 来检查这一点。如果您看到每个文件的用户名，则这很可能不是问题，除非您在推送 url 规则中有错误的用户，用户必须匹配。

## 结论

我对这个设置很满意，我不需要做太多的事情来继续添加 repos，下一个需要做的就是克隆它，添加 **`receive.denyCurrentBranch updateInstead`** 规则，并在我的远程设置中添加设置的 url。

你怎么想呢?你会试一试吗？有任何问题和顾虑，请告诉我。