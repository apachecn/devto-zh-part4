# 如何在 linux mint 上安装 vit

> 原文：<https://dev.to/callas1900/how-to-install-vit-on-linux-mint-1hlg>

一个非常棒的命令行伙伴创造了一个[任务战士](https://github.com/GothenburgBitFactory/taskwarrior)包装器，https://github.com/scottkosty/vitVIT([)。](https://github.com/scottkosty/vit)

我认为这个包装器是 tmux 友好的。我也喜欢这种方法来改进 TaskWarrior，因为如果你已经在使用 TaskWarrior，就没有转移成本。

# 如何安装

```
$ git clone https://github.com/scottkosty/vit.git
$ cd vit
$ ./configure
$ make
$ make install 
```

VIT 是用 perl 创建的。我不得不安装 perl 模块，因为我的 linux 系统不熟悉 perl。

# 故障排除

## 1。perl 的诅咒

如果您从`./configure`
收到此错误信息

```
configure: error: can not continue: perl "Curses" module not found 
```

通过[https://community . Linux mint . com/software/view/lib curses-perl](https://community.linuxmint.com/software/view/libcurses-perl)安装`libcurses-perl`

## 2。运行 vit 时

```
Can't locate Try/Tiny.pm in @INC (you may need to install the Try::Tiny module) (@INC contains: /etc/perl /usr/local/lib/x86_64-linux-gnu/perl/5.22.1 /usr/local/share/perl/5.22.1 /usr/lib/x86_64-linux-gnu/perl5/5.22 /usr/share/perl5 /usr/lib/x86_64-linux-gnu/perl/5.22 /usr/share/perl/5.22 /usr/local/lib/site_perl /usr/lib/x86_64-linux-gnu/perl-base .) at /usr/local/bin/vit line 13.
BEGIN failed--compilation aborted at /usr/local/bin/vit line 13. 
```

通过[https://Ubuntu . pkgs . org/18.10/Ubuntu-universe-amd64/libtry-tiny-smart catch-perl _ 0.5-1 _ all . deb . html](https://ubuntu.pkgs.org/18.10/ubuntu-universe-amd64/libtry-tiny-smartcatch-perl_0.5-1_all.deb.html)安装`Try::Tiny`

如果安装后出现同样的错误信息...

我试图在这个目录
中找到`Tiny.pm`

```
usr/share/perl5/vendor_perl/Try/Tiny.pm 
```

根据这个错误消息，没有用于`usr/share/perl5/vendor_perl/`的 perl 路径

所以，

```
export PERL5LIB=/usr/share/perl5/vendor_perl/ 
```

然后

有用！！