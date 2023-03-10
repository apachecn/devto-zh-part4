# 在 Arch Linux 上安装 yay

> 原文：<https://dev.to/mattdark/installing-yay-on-arch-linux-1kgm>

Yay 是一个 AUR 助手，你可以用它来代替 yaourt(已弃用)。它在 Arch Linux 存储库中不可用。所以如果你最近安装了 Arch，你可以按照 GitHub 库中的说明来做。

## 安装

首先需要克隆 PKGBUILD，运行`makepkg`脚本来构建包。

### 要求

Git 是 yay 的一个依赖项，AUR 的安装是通过一个 fakeroot 环境完成的，所以你需要安装这两个包。在新的 Arch 安装中，默认情况下不会安装 git 和 fakeroot，您还需要 Git 从 AUR 克隆 *PKGBUILD* 。

要使用`makepkg`，需要安装它的依赖项。您可以安装包含`binutills`、`make`、`gcc`、`fakeroot`的 *base-devel* 包组，也可以单独安装。

去吧" T0 "

```
$ sudo pacman -S git 
```

Enter fullscreen mode Exit fullscreen mode

基础开发:

```
$ sudo pacman -S base-devel 
```

Enter fullscreen mode Exit fullscreen mode

运筹学

binutils，make，gcc，fakeroot:

```
$ sudo pacman -S binutils make gcc fakeroot 
```

Enter fullscreen mode Exit fullscreen mode

### 安装 yay

```
$ git clone https://aur.archlinux.org/yay.git
$ cd yay
$ makepkg -si 
```

Enter fullscreen mode Exit fullscreen mode

Yay 已经安装，你可以使用这个助手直接从 AUR 获得软件包。