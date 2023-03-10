# 使用 Alpine Linux 设置最小 kiosk 环境

> 原文：<https://dev.to/nesterow/setup-minimal-kiosk-environment-with-alpine-linux-27b>

在这篇文章中，我想分享一个为 Raspberry PI 和类似物开发 kiosk 应用程序的方法。

## 为什么是 Alpine Linux？

几乎所有领先的 linux 发行版都为 Raspberry PI 提供了构建版本。然而，它们中的大多数都是无用的，尤其是当你需要一个运行单一应用程序的系统时。Alpine Linux 体积很小(~130mb)，为您提供简单(但功能强大)的工具来构建自己的系统。
Alpine 被设计为从 RAM 运行，这使得它非常适合创建容器或嵌入式系统。

## 你需要:

*   树莓 PI 模型 3
*   SD 卡，1GB 或更多
*   HDMI 电缆
*   树莓派的电源
*   带 HDMI 端口的电视或显示器
*   键盘
*   SSH 终端应用程序(针对 Windows 用户)

## 准备 SD 卡

无需刷新磁盘映像，安装过程就像复制文件一样简单:

1.  为您的模型下载 Alpine 发行版。我们需要`aarch64`架构的构建。[https://alpinelinux.org/downloads/](https://alpinelinux.org/downloads/)
2.  在 Fat32 中格式化 SD 卡并使其可引导( [Linux](https://askubuntu.com/questions/1064278/how-to-create-win95-fat32-lba-partition-type-on-32-gb-usb?answertab=votes#tab-top) ， [Mac](https://wiki.alpinelinux.org/wiki/Create_a_bootable_SDHC_from_a_Mac) )
3.  将下载的档案文件解压到 SD 卡`alpine-rpi-<version>-armhf.tar.gz`

## 安装树莓 PI 固件

一些司机不包括在标准阿尔卑斯分布。您需要手动下载它们。

1.  打开 SD 卡，创建名为`firmware`的目录
2.  从以下存储库中复制名为`brcm`的目录:[https://github.com/RPi-Distro/firmware-nonfree](https://github.com/RPi-Distro/firmware-nonfree)

现在 SD 卡准备好树莓派了。

* * *

## 首次开机

第一次启动时，Alpine 会要求登录。最初，系统只有`root`个用户有一个*空密码*。

```
Welcome to Alpine!

The Alpine Wiki contains a large amount of how-to guides and general
information about administrating Alpine systems.
See <http://wiki.alpinelinux.org>.

You can setup the system with the command: setup-alpine

login: root
... 
```

Enter fullscreen mode Exit fullscreen mode

首次登录后立即运行设置脚本:

```
alpine:~# setup-alpine 
```

Enter fullscreen mode Exit fullscreen mode

设置过程将有助于获得正确的初始设置。它非常直接和直观。

## 保存系统配置

因为 Alpine 从内存中运行，所以您对系统所做的所有更改都会在重新启动后丢失。当您需要永久保存任何更改时，Alpine 提供了一个名为[本地备份实用程序](https://wiki.alpinelinux.org/wiki/Alpine_local_backup) (lbu)的工具。

在任何重大变更后，如编辑配置或安装新软件，总是调用`lbu commit` :

```
alpine:~# lbu commit

usage: lbu commit|ci [-nv] [<media>]

Options:
  -d    Remove old apk overlay files.
  -e    Protect configuration with a password.
  -n    Don't commit, just show what would have been committed.
  -p <password> Give encryption password on the command-line
  -v    Verbose mode. 
```

Enter fullscreen mode Exit fullscreen mode

## 管理软件

Alpine 中的包管理很简单，但与其他发行版略有不同。

更新和升级软件包:

```
alpine:~# apk update && apk upgrade 
```

Enter fullscreen mode Exit fullscreen mode

首先，我们来安装 nano 编辑器:

```
alpine:~# apk add nano 
```

Enter fullscreen mode Exit fullscreen mode

现在，我们需要添加 APK 存储库，以便能够安装其他软件。

打开`nano /etc/apk/repositories`并取消指向社区存储库的链接的注释。该链接应类似于以下内容:

```
http://mirror.example.com/mirrors/alpine/v3.10/community 
```

Enter fullscreen mode Exit fullscreen mode

然后更新包索引:

```
alpine:~# apk update 
```

Enter fullscreen mode Exit fullscreen mode

添加新软件后不要忘记提交更改:

```
alpine:~# lbu commit -d 
```

Enter fullscreen mode Exit fullscreen mode

现在我们已经准备好根据我们的需求来构建系统。

## 配置 SSHD

从笔记本电脑管理设备更方便。让我们设置`sshd`来打开远程外壳。

打开`nano /etc/ssh/sshd_config`，编辑如下:

```
PermitRootLogin yes
PasswordAuthentication yes
PermitEmptyPasswords no
DenyUsers guest 
```

Enter fullscreen mode Exit fullscreen mode

保存文件并提交系统配置:

```
alpine:~# lbu commit -d 
```

Enter fullscreen mode Exit fullscreen mode

重启 SSH 守护进程:

```
alpine:~# service sshd restart 
```

Enter fullscreen mode Exit fullscreen mode

从桌面连接到设备:

```
~#> ssh root@192.168.2.199
password: ********* 
```

Enter fullscreen mode Exit fullscreen mode

* * *

## 安装 X11

下一步是设置基本的显示服务器，并配置 Raspberry PI GPU 来实现 3D 加速。

Setup Xorg server:

```
alpine:~# setup-xorg-base
(1/100) Installing ... 
```

Enter fullscreen mode Exit fullscreen mode

安装视频驱动:

```
alpine:~# apk add mesa-dri-vc4 mesa-egl xf86-video-fbdev xf86-video-vesa xf86-input-mouse xf86-input-keyboard dbus setxkbmap kbd xrandr xset
(1/100) Installing ... 
```

Enter fullscreen mode Exit fullscreen mode

将更改保存到磁盘:

```
alpine:~# lbu commit -d 
```

Enter fullscreen mode Exit fullscreen mode

## 配置 GPU

树莓 PI 没有 BIOS，但是必要的硬件配置可以通过 [usercfg.txt](https://www.raspberrypi.org/documentation/configuration/config-txt/) 文件进行。

插入 SD 卡，用以下选项创建`usercfg.txt`:

```
dtoverlay=vc4-fkms-v3d
gpu_mem=256 
```

Enter fullscreen mode Exit fullscreen mode

根据显示器型号和分辨率，您需要修改设置。所有可能的选项都可以在这里找到[。](https://www.raspberrypi.org/documentation/configuration/config-txt/video.md)

* * *

## 创建用户

现在，为了运行 kiosk，我们需要创建一个非特权用户。该用户还将通过使用(https://en.wikipedia.org/wiki/SFTP)[]来访问应用程序目录

我们将把应用程序文件保存在`/srv`目录中，它也将是用户的主目录。

```
alpine:~# adduser -h /srv user
alpine:~# chown -R user /srv
alpine:~# chmod 701 /srv
alpine:~# lbu commit 
```

Enter fullscreen mode Exit fullscreen mode

用户的主目录位于`/srv`，但是该目录中的文件不会持久化，直到该目录位于 LBU 索引:

```
alpine:~# lbu add /srv
alpine:~# lbu commit 
```

Enter fullscreen mode Exit fullscreen mode

最后，用户必须在系统启动时自动登录。为了实现这种行为，编辑`/etc/inittab`改变`tty1`的选项:

```
tty1::respawn:/bin/login -f user 
```

Enter fullscreen mode Exit fullscreen mode

* * *

## 创建信息亭

安装 Chromium 浏览器:

```
alpine:~# apk add chromium
alpine:~# lbu commit -d 
```

Enter fullscreen mode Exit fullscreen mode

Chromium 将在用户登录后启动。让我们为此创建两个 init 脚本。

创建包含以下内容的`/srv/.profile`文件:

```
#!/bin/sh
# start X server
exec startx 
```

Enter fullscreen mode Exit fullscreen mode

创建包含以下内容的`/srv/.xinitrc`:

```
#!/bin/sh

# turn off screensaver
xset -dpms
xset s off
xset s noblank

# screen size
width="1920"
height="1080"

# url
url="https://dev.to"

exec chromium-browser $url --window-size=$width,$height --window-position=0,0 --kiosk --no-sandbox --full-screen --incognito --noerrdialogs --disable-translate --no-first-run --fast --fast-start --ignore-gpu-blacklist --disable-quic --enable-fast-unload --enable-tcp-fast-open ---enable-native-gpu-memory-buffers --enable-gpu-rasterization --enable-zero-copy --disable-infobars --disable-features=TranslateUI --disk-cache-dir=/tmp 
```

Enter fullscreen mode Exit fullscreen mode

使初始化文件可执行并提交更改:

```
alpine:~# chmod u+x /srv/.profile
alpine:~# chmod u+x /srv/.xinitrc
alpine:~# lbu commit 
```

Enter fullscreen mode Exit fullscreen mode

亭子准备好了！chromium 将在系统启动时启动。此设置的总映像大小约为 235Mb，这意味着您可以从 256Mb 的 SD 卡上运行 Alpine Linux kiosk。

* * *

## 接下来呢？

这种设置为您的 kiosk 应用程序提供了非常简单的开发环境。开发应用程序时，您可以将 kiosk 指向 dev。你的电脑上的服务器进行实时调试和优化。它与 Webpack 和 Gulp 配合得很好。为了上传生产版本，只需使用`SFTP`和`lbu commit`。