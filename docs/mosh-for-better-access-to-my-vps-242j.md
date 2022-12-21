# 为了更好地访问我的 VPS

> 原文：<https://dev.to/shrysr/mosh-for-better-access-to-my-vps-242j>

Mosh 是 mobile shell 的缩写，是 SSH 的一种替代方案，尤其适用于网络条件差、需要频繁切换网络的情况。它通过 UDP 端口工作，该端口必须专门启用。我是通过#emacs 里的人知道 mosh 的。

由于 SSH 连接上的网络问题，我经常遇到麻烦，延迟通常会影响我的打字能力，在 IRC/Weechat 上回复尤其不方便。我希望 mosh 能缓解这个问题。

需要启用 UDP，mosh 才能工作。为此，我在端口 60000:61000 上使用了 UFW。

```
sudo ufw allow 60000:61000/udp 
```

*   本质上，一个 mosh 服务器在两台机器(VPS 和本地机器)上运行，这些机器执行彼此同步命令和输出的后台工作。这减少了打字的延迟，还有其他优点。Mosh 的初始连接(包括身份验证)是通过 SSH 进行的，之后使用 UDP 协议。

安装 Mosh:

在 debian - mosh 上可以作为一个包直接获得。运行`apt-get update`然后安装 mosh。

```
apt-get install "mosh" 
```

`mosh-server`必须在两台机器上运行。把这个包含在`.bashrc`或者启动程序列表中可能是个好主意。该命令将启动 mosh 服务器并分离进程(进入后台)。

```
mosh-server 
```

这就是我遇到麻烦的地方。为了运行 mosh，必须指定一个 UTF-8 环境，看起来两台连接机器的语言环境必须匹配(？).在 Debian 上，使用`dpkg`
相对容易

```
sudo dpkg-reconfigure locales 
```

我选择了`en_USA.UTF-8`选项。可用`locale`查看现有的语言环境配置。

```
locale

LANG="en_CA.UTF-8"
LC_COLLATE="en_CA.UTF-8"
LC_CTYPE="en_CA.UTF-8"
LC_MESSAGES="en_CA.UTF-8"
LC_MONETARY="en_CA.UTF-8"
LC_NUMERIC="en_CA.UTF-8"
LC_TIME="en_CA.UTF-8"
LC_ALL= 
```

有时，在像`~/.bashrc`这样的位置定义地区的附加设置。这应该类似于:

```
export LANGUAGE=en_US.UTF-8
export LANG=en_US.UTF-8
# export LC_ALL=en_US.UTF-8 
```

上述内容可用于明确设置首选项。Debian wiki 劝阻终端用户不要使用`LC_ALL`，但这是最简单的方法。我的初始设置是用`en_CA.UTF-8`。虽然这也是 UTF-8，出于某种原因，mosh 仍然抛出了地区错误。无论如何，我希望我所有的电脑都统一使用`en_US`版本。

## mosh 有所作为吗？

才过了几个小时，但已经能感觉到不同了。Mosh 清楚地指示连接何时丢失，并且在键入时没有延迟。要了解它的行为还需要进一步的实验，但至少我可以平静地打出一条消息，没有延迟。

[2019-07-31 Wed]在使用 mosh 3 天多之后，我很高兴地注意到，通过终端与我的 vps 互动的体验有了显著改善。很少有网络连接真的很差的情况，mosh 会清楚地指示连接断开，并在需要时允许安全退出。我可以切换计算机并直接进入，而不用费心去恢复 SSH 连接。