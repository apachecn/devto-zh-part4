# 随机化您的 XFCE / LightDM 登录屏幕壁纸

> 原文：<https://dev.to/karlredman/randomize-your-xfce-lightdm-login-screen-wallpaper-1ape>

如何:随机化 LightDM / XFCE v4.x 登录屏幕壁纸图像。

对于轻量级和高度可配置的 linux 桌面环境 [XFCE](https://xfce.org/) 的粉丝来说，它的默认欢迎界面由 [LightDM](https://wiki.ubuntu.com/LightDM) 管理，这是一个令人沮丧的现实，没有内置的方式来随机化登录屏幕图像。这个简单的 perl 脚本和目录布局将使用 cron 作业提供该服务。该过程会将包含图像池的目录中的随机图像文件复制到“发布”目录中；作为`background.jpg`。配置完成后，XFCE 会将该图像用作登录屏幕的背景。

## 概述:

这一程序的基本前提是:

1.  用你想用于登录背景的图片填充一些目录。
2.  设置一个 cron 作业来运行一个脚本，将随机映像从源目录复制到一个`publish`目录
3.  XFCE 将使用该图像作为登录屏幕的背景图像。

## TL；博士:

一个为 XFC 配置随机登录屏幕的示例项目可以在[这里](https://github.com/karlredman/LoginWallpaper-XFCE-Variety)找到

## 单用户 vs 多用户系统和加密的`home`目录:

*   不使用加密主目录的单用户系统可以使用主目录中的一个目录(即`/home/karl/Repositories/LoginWallpaper-XFCE-Variety`)。
*   使用像 [variety](https://peterlevi.com/variety/) 这样的程序的多用户系统将需要使用用户组级别的目录位置。(即`/opt/LoginWallpaper-XFCE-Variety`)
*   使用加密主目录的单用户和多用户系统需要使用用户组级别的目录位置(即`/opt/LoginWallpaper-XFCE-Variety`)

## 通用设置

示例将假设一个多用户系统具有加密的主目录。

### 将示例项目克隆到适当的目录下:

```
# a global directory space
cd /opt
#
# clone the project
sudo git clone git@github.com:karlredman/LoginWallpaper-XFCE-Variety.git
#
# set permissions on the project directory
sudo chown -R root:users LoginWallpaper-XFCE-Variety 
```

### 将图片添加到图片目录中`Variety`

*   将您认为有价值/安全的任何`jpg`图片添加到登录屏幕上。在本例中，您添加的图像将进入`/opt/LoginWallpaper-XFCE-Variety/Variety/`。
*   务必确认您的图像属于`users`组-根据需要进行更改

### 运行项目脚本来播种`publish`目录

(即`/opt/LoginWallpaper-XFCE-Variety/Greeter/background.jpg`)

*   给文件设定种子

```
cp $(/opt/LoginWallpaper-XFCE-Variety/mbin/print_random_file.pl /opt/LoginWallpaper-XFCE-Variety/Variety) /opt/LoginWallpaper-XFCE-Variety/Greeter/background.jpg 
```

*   验证文件是否存在-故障排除...

```
ls /opt/LoginWallpaper-XFCE-Variety/background.jpb 
```

### 通过 GUI 界面配置 LightDM Greeter

*   使用以下方法之一启动欢迎配置应用程序:
    *   通过`LightDM GTK Greeter settings`的 GUI 菜单
    *   名称可能因 linux 桌面发行版而异
    *   通过命令行

```
 sudo lightdm-gtk-greeter-settings 
```

[![ghtdm-gtk-greeter-settings.png](img/149954d5a9db4ae893aa42aed50f61b1.png)](https://raw.githubusercontent.com/karlredman/Articles/master/content/dev.to/xfce_login_wallpaper_cycle/lightdm-gtk-greeter-settings.png)

*   选择`Background->Image`

*   将`Image`设置为`<path>/background.jpg`

    *   即`/opt/LoginWallpaper-XFCE-Variety/Greeter/background.jpg`
    *   名称`background.jpg`是必需的
*   保存设置并退出应用程序

### 测试和调试

如果您遇到问题，请尝试手动运行该命令。

### 添加 cron 作业

*   在默认编辑器中打开 cron:

```
cron -e 
```

*   添加作业(示例)

```
# change to wallpaper every 10 min
*/10 * * * * cp $(/opt/LoginWallpaper-XFCE-Variety/mbin/print_random_file.pl /opt/LoginWallpaper-XFCE-Variety/Variety) /opt/LoginWallpaper-XFCE-Variety/Greeter/background.jpg 
```

## 使用`Variety`作为图像来源:

[Variety](https://peterlevi.com/variety/) 是一款壁纸后台管理器，可用于多个 linux 发行版，兼容多个桌面管理器。`variety`提供的选项之一是管理你的登录欢迎图像的能力。

1.  打开综艺偏好设置并导航至`customize`选项卡
2.  选择`Login Screen Support`复选框
3.  指定图像目录(即/opt/LoginWallpaper-XFCE-Variety/Variety)

[![variety.png](img/b51ebcb1e0b374760e60c260b536fad4.png)](https://raw.githubusercontent.com/karlredman/Articles/master/content/dev.to/xfce_login_wallpaper_cycle/variety.png)