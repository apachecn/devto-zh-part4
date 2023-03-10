# Ubuntu:移除禁用的快照

> 原文：<https://dev.to/boris/ubuntu-remove-disabled-snaps-2mfj>

2015 年 10 月，Ubuntu 15.10 附带了一个叫`snappy`的东西，就是“Canonical 开发的软件部署和包管理系统”(复制/粘贴自[维基百科](https://en.wikipedia.org/wiki/Snappy_(package_manager)))。

到目前为止，大多数 Ubuntu 用户都不知道*快照*，直到他们运行`df -h`并看到类似于
的东西

```
/dev/loop1                   174M  174M     0 100% /snap/spotify/34
/dev/loop4                    55M   55M     0 100% /snap/core18/1049
/dev/loop3                   141M  141M     0 100% /snap/gnome-3-26-1604/88
/dev/loop5                   4.2M  4.2M     0 100% /snap/gnome-calculator/406
/dev/loop7                    89M   89M     0 100% /snap/core/7270
/dev/loop8                   2.8M  2.8M     0 100% /snap/telegram-cli/25
/dev/loop9                   1.0M  1.0M     0 100% /snap/gnome-logs/61
/dev/loop10                   36M   36M     0 100% /snap/gtk-common-themes/1198
/dev/loop11                  436M  436M     0 100% /snap/libreoffice/133
/dev/loop12                  150M  150M     0 100% /snap/gnome-3-28-1804/63
/dev/loop13                   89M   89M     0 100% /snap/core/7169
/dev/loop14                   57M   57M     0 100% /snap/snapcraft/3059
/dev/loop15                   15M   15M     0 100% /snap/gnome-characters/292
/dev/loop16                  181M  181M     0 100% /snap/spotify/35
/dev/loop17                  3.8M  3.8M     0 100% /snap/gnome-system-monitor/95
/dev/loop20                  141M  141M     0 100% /snap/gnome-3-26-1604/90
/dev/loop21                  419M  419M     0 100% /snap/libreoffice/134
/dev/loop2                   3.8M  3.8M     0 100% /snap/gnome-system-monitor/100
/dev/loop22                   15M   15M     0 100% /snap/gnome-characters/296
/dev/loop23                  150M  150M     0 100% /snap/gnome-3-28-1804/67
/dev/loop24                   43M   43M     0 100% /snap/gtk-common-themes/1313
/dev/loop0                    55M   55M     0 100% /snap/core18/1055 
```

Enter fullscreen mode Exit fullscreen mode

事情是这样的，当一个 snap 被安装后，先前的版本**不会被**卸载或删除，你需要手动完成。另外，请记住“更新”实际上是新版本的安装...所以通过计算，几个月后你的`df -h`将会一团糟。

要识别我的计算机中安装的更多细节快照，最佳选项是:

```
$ snap list --all                      
Name                  Version                     Rev   Tracking  Publisher       Notes
core                  16-2.39.3                   7270  stable    canonical✓      core
core                  16-2.39.2                   7169  stable    canonical✓      core,disabled
core18                20190627                    1049  stable    canonical✓      base,disabled
core18                20190704                    1055  stable    canonical✓      base
gnome-3-26-1604       3.26.0.20190705             90    stable/…  canonical✓      -
gnome-3-26-1604       3.26.0.20190621             88    stable/…  canonical✓      disabled
gnome-3-28-1804       3.28.0-10-gaa70833.aa70833  67    stable    canonical✓      -
gnome-3-28-1804       3.28.0-10-gaa70833.aa70833  63    stable    canonical✓      disabled
gnome-calculator      3.32.1                      406   stable/…  canonical✓      -
gnome-characters      v3.32.1+git2.3367201        292   stable/…  canonical✓      disabled
gnome-characters      v3.32.1+git2.3367201        296   stable/…  canonical✓      -
gnome-logs            3.32.0-4-ge8f3f37ca8        61    stable/…  canonical✓      -
gnome-system-monitor  3.32.1-2-ga7c19eaeff        95    stable/…  canonical✓      disabled
gnome-system-monitor  3.32.1-3-g0ea89b4922        100   stable/…  canonical✓      -
gtk-common-themes     0.1-16-g2287c87             1198  stable/…  canonical✓      disabled
gtk-common-themes     0.1-22-gab0a26b             1313  stable/…  canonical✓      -
libreoffice           6.2.5.2                     134   stable    canonical✓      -
libreoffice           6.2.5.2                     133   stable    canonical✓      disabled
snapcraft             3.6                         3059  stable    canonical✓      classic
spotify               1.1.5.153.gf614956d-16      35    stable    spotify✓        -
spotify               1.1.0.237.g378f6f25-11      34    stable    spotify✓        disabled
telegram-cli          1.4.5                       25    stable    marius-quabeck  - 
```

Enter fullscreen mode Exit fullscreen mode

这里有一些有趣的东西:**注释**列。也许我们可以使用该列来过滤并删除未使用的或**禁用的**快照？是的，我们可以！我用下面的脚本来做:

```
#!/bin/bash
set -eu

snap list --all | awk '/disabled/{print $1, $3}' |
    while read s r; do
        sudo snap remove "$s" --revision="$r"
    done 
```

Enter fullscreen mode Exit fullscreen mode

老规矩，希望有帮助:)