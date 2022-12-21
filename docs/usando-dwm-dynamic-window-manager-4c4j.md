# 动态窗口管理器

> 原文：<https://dev.to/nenitf/usando-dwm-dynamic-window-manager-4c4j>

[DWM](http://dwm.suckless.org/) 是一个***【uting】****窗口经理，负责在屏幕上排列应用程序。它具有 suckless 理念，使其源代码尽可能小，通常会提高程序性能。*

 *## Por que DWM？

*   内存消耗低，非常适合于较弱的笔记本电脑
*   由你指挥，完全自由的快捷键
*   可选功能很少，DWM 的重点在于窗口管理器的 MVP
*   具有主区域概念的窗口布局，使查看新应用程序变得可预测

## Dmenu

DWM 附带了 [dmenu](http://tools.suckless.org/dmenu/) ，这是一个应用程序启动器(`Super+d`。

# 运行

DWM 使用客户端、主区域、标记和模式的概念。

*   客户端是打开的应用程序窗口，例如 firefox。
*   主区域是发送新客户端的位置(可与`Super+Enter`互换)，默认情况下位于左侧。每个新客户，旧客户向右移动，已经向右移动的客户向下移动。

```
+---------------+    +-------+-------+    +-------+-------+    +-------+-------+
|               |    |       |       |    |       |   2   |    |       |   3   |
|               |    |       |       |    |       |       |    |       +-------+
|       1       | -> |   2   |   1   | -> |   3   +-------+ -> |   4   |   2   |
|               |    |       |       |    |       |   1   |    |       +-------+
|               |    |       |       |    |       |       |    |       |   1   |
+---------------+    +-------+-------+    +-------+-------+    +-------+-------+ 
```

Enter fullscreen mode Exit fullscreen mode

*   标签与常用工作区非常相似，您可以在其中以用户身份限制打开哪些应用程序。但是，您可以将客户端标记为同时出现在多个标记中—例如，如果您希望音乐播放器跟随您。
*   模式是窗口的排列方式，默认为 3-tile(默认)、float(浮动窗口)和 mono(一次显示一个窗口，可以通过‘t0’或‘t1’访问其他窗口)

## 我的常用

*   我在全屏或应用程序旁边打开终端(`Super+Shift+Enter`
*   我是否正在搜索任何 dmenu 程序，例如 firefox
*   用`Super+j`或`Super+k`从一个窗口跳到另一个窗口
*   用`Super+Enter`将一些窗口移到主区域
*   与〔t0〕交换标记
*   使用`Super+Shift+q`关闭窗口
*   我用`Super+Shift+s`关闭系统

## 设施

要安装，只需下载源代码并使用命令`sudo make clean install`进行编译，然后将片段`exec dwm`
放入~/.xinitrc 中即可。注意:根据负责启动桌面的显示管理器的不同，您可能需要对其进行配置。对于 lightdm，只需创建如下所示的新文件并将其放在/usr/share/xsession 中。

```
[Desktop Entry]
Name=DWM
Comment=DWM
Exec=/home/neni/.xinitrc
Type=Application 
```

Enter fullscreen mode Exit fullscreen mode

在登录之前，您可以选择 DWM 配置文件。

## 配置

### 写入 config.h

最简单的设置(如颜色、快捷方式、标记符号、窗口边框和默认字体)位于 config.h 文件中。每次修改都需要重新编译代码并重新启动会话。

### 补丁

DWM 附带了最少的要求，但其他程序员还可以实现其他几项功能。它们是通过[补丁](http://dwm.suckless.org/patches)进行的，这是代码的“粘贴”。
如何运作:

*   获取修补程序. diff 文件并分析实现的源代码差异
*   修改 diff 的读取区域中的对应文件
*   重新编译 o código
*   重新启动会话

### Personalizando 状态栏

可以通过命令修改状态栏:

```
xsetroot -name "tudo que for colocado aqui | vai atualizar a barra de status"
xsetroot -name "$(echo 'tabém é possivel usar comandos bash')" 
```

Enter fullscreen mode Exit fullscreen mode

只更新(用于时钟的)条的脚本示例为:

```
online(){
    curl -w %{http_code} --silent -o /dev/null google.com | awk '{if($0=="000") {print "OFFLINE"} else { print "ONLINE"}}'
}

datetime(){
    echo " $(date +%H:%M)"
}

while true; do xsetroot -name "$(online) | $(datetime)"
    sleep 5s
done 
```

Enter fullscreen mode Exit fullscreen mode

要在登录时自动启动脚本，只需将脚本放在~/.xinitrc.
文件中即可

```
# mata processo do script se existente
pkill -f ~/dev/scripts/dwm/status.sh

# modifica status
~/dev/scripts/dwm/status.sh&

# inicia dwm
exec dwm 
```

Enter fullscreen mode Exit fullscreen mode

## 本员额的更新

我打算用我的 DWM 经验时间更新这个员额。在第一个版本(2019 年 6 月 8 日)中，我与经理的使用时间约为 1 个月。

### 2021-09-24

我喜欢用这种分散式的瞬间，它有一个[窗口管理器](https://github.com/instantOS/instantWM)dwm 叉。

### 2021-10-21

不支持[dwm](https://dwm.suckless.org)的尖刻描述:

> 因为 dwm 是通过编辑其源代码定制的，所以对其进行二进制打包是没有意义的。这使得它的用户基数很小，并且保持精英化。没有新手问愚蠢的问题。

这让我在 Ubuntu 中体验到了‘t0’awesome，它满足了我对‘T2’twm 的所有需求。

## 备注

*   岗位激励
*   [Wiki 到弧](https://wiki.archlinux.org/index.php/Dwm#Statusbar_configuration)
*   [我的 dwm 储存库](https://github.com/nenitf/dwm)*