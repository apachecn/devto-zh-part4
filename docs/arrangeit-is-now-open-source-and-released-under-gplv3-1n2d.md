# arrangeit 现在是开源的，在 GPLv3 下发布

> 原文：<https://dev.to/ipaleka/arrangeit-is-now-open-source-and-released-under-gplv3-1n2d>

TL；DR 最终用户、Pythonistas、图形设计师和校对人员被邀请加入一个全新的开源多平台 Python 项目

[在动作中安排 v 0.3α](https://vimeo.com/351440620)

## 关于

**arrangeit** -跨平台桌面实用程序，简化 windows 管理

版本 [0.91](https://github.com/ipaleka/arrangeit/tree/master/assets) 是 Win95/98 桌面实用程序 ArrangeIt 的最新发布版本。

二十年后，名为 **arrangeit** 的新软件诞生了。它是用 Python 从头开始开发的，[最初的公开发行版 v0.3alpha](https://github.com/ipaleka/arrangeit/releases/tag/v0.3alpha) 现在可以从 Github 下载。请记住，这个版本是阿尔法软件，它不适合生产。

## 安装

### 二元分布

0.3alpha 版是 Debian/Ubuntu 和 MS Windows 的二进制发行版(32 位和 64 位版本)。

在 Ubuntu 中，你应该至少安装了 Python 版。提取临时目录中的归档文件，并运行以下命令:

```
$ sudo apt-get install python3-pil.imagetk python3-xlib
$ cd tmp_directory
$ sudo dpkg -i python3-pynput_1.4.2_all.deb
$ sudo dpkg -i arrangeit_0.3alpha_all.deb

# run the program with
$ arrangeit 
```

在 Windows 中，只需解压存档文件并在其中运行`arrangeit.exe`可执行文件。

### 来源分布

您应该安装 Python 3 来从源代码运行 **arrangeit** 。对于其他依赖项，您可以在文档中找到各种平台[的说明。要准备在 Ubuntu 上启动，您可以运行以下命令:](https://arrangeit.readthedocs.io/en/latest/development.html)

```
$ sudo apt-get install python3-dev git gcc libcairo2-dev pkg-config
$ sudo apt-get install libgirepository1.0-dev gir1.2-gtk-3.0 gir1.2-wnck-3.0 
```

下载源归档文件作为压缩文件，或者使用以下内容克隆 git 存储库:

```
$ cd ~/projects
$ git clone https://github.com/ipaleka/arrangeit.git 
```

您应该创建并激活一个单独的虚拟环境来安排:

```
$ mkdir venvs
$ python3 -m venv venvs/arrangeit
$ source venvs/arrangeit 
```

并从激活的虚拟环境中运行以下命令来安装 Python 依赖项:

```
(arrangeit) $ cd arrangeit
(arrangeit) $ python -m pip install -r requirements/linux.txt  # windows.txt/darwin.txt 
```

运行 arrangeit 与:

```
(arrangeit) $ python -m arrangeit 
```

## 
  
基本用法

移动鼠标来设置窗口的未来位置。您可以通过按键盘上的 **Ctrl** 键手动更改活动角，也可以通过捕捉功能动态更改(当一个移动窗口捕捉到某个边不与当前活动角相连的窗口时)。

通过按下鼠标左键(或者按下键盘上的 **Enter** 键),你可以设置窗口的未来位置，然后移动鼠标来调整主窗口的大小。再次按下鼠标左键确认未来尺寸。

要将当前窗口移动到另一个工作区，您应该在键盘上按下想要的工作区编号。或者，您可以通过单击鼠标中键或按下键盘上的 **Shift** 键来释放鼠标，然后用鼠标单击所需的工作区框。

单击列出的窗口区域中的窗口标题，从其他窗口开始操作。

单击标题框内的最小化/恢复图标，更改当前窗口的最小化状态。

如果您只想更改当前窗口的位置，而不想更改其大小，请单击“固定/可调整大小”图标(反之亦然)。

## 已知问题

问题列表可以在 [Github 页面](https://github.com/ipaleka/arrangeit/issues)上找到。除了赞扬和谴责之外，请随意添加与编曲发展相关的任何目的的新问题-出于这种目的，请使用此线程。无红利

*   GNU/Linux 中的 arrangeit 只能在 X Window 系统下工作——Wayland 还不被支持，欢迎大家在[相关问题中提出可能的指导方针](https://github.com/ipaleka/arrangeit/issues/5)
*   Mac OS X 功能计划在下一个版本中推出 **0.4beta**
*   Windows 10 下的 X-mouse 有时会阻止二进制分发可执行文件的启动
*   旧版本的 Windows 没有工作区/虚拟桌面功能。Windows 10 是第一个拥有虚拟桌面官方 API 的版本——如果一些定制实现在以前的 Windows 版本中非常流行，请告诉我
*   当前的平面设计有点乏味；欢迎所有建议