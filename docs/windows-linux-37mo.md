# 文件名乱码:解决在 Linux 上解压 Windows 压缩的文件时出现乱码的方法

> 原文：<https://dev.to/nabbisen/windows-linux-37mo>

## 关于文件名的乱码

在 Linux 上解压缩 Windows 压缩的文件时，包含日语的文件名会变成乱码。
相反的 Linux - >Windows 的情况也一样。

此事件是由于压缩文件时文件名编码方法的差异引起的:

*   Linux: UTF-8
*   windows:CP 932(JIS 移位寄存器)

描述在 Linux 上解压缩时如何在命令行中解决乱码问题。
使用的环境为操作系统为基于[Arch Linux](https://www.archlinux.org/) 的[Manjaro](https://manjaro.org/) ，桌面为[Cinnamon](https://github.com/linuxmint/Cinnamon) 。

## 用 How To: Linux 命令解决

### #1。[T4`unar`](https://www.archlinux.org/packages/community/x86_64/unarchiver/)

是推荐的方法。

这是一种在一条线上实现压缩文件解压和文件名解析的方法:

```
$  unar 圧縮ファイル.zip
圧縮ファイル.zip: Zip
  圧縮ファイル/  (dir)... OK.
  圧縮ファイル/ファイル  (xxxxx B)... OK.
 ... Successfully extracted to "./圧縮ファイル". 
```

Enter fullscreen mode Exit fullscreen mode

命令名称的由来是" Unar"chiver。
虽然很方便，但是因为使用的机会很少，所以我很容易忘记命令名称本身...。 苦笑

即使减去它，操作者也不需要指定编码，所以非常方便。
被输入密码的情况下，会酌情在控制台上询问。
我主要使用这个方法。

### #2。 [`unzip`](https://linux.die.net/man/1/unzip) + [`convmv`](https://linux.die.net/man/1/convmv)

首先用`unzip`解压文件:

```
$  unzip 圧縮ファイル.zip
Archive:  圧縮ファイル.zip
   creating: ���.../
  inflating: ���.../������...... 
 ... 
```

Enter fullscreen mode Exit fullscreen mode

之后用`convmv`一个一个地处理文件，变换文件名的方法:

```
$  convmv --notest -f shift_jis -t utf-8 ���.../������...... 
```

Enter fullscreen mode Exit fullscreen mode

在此，除了`--notest`选项以外执行时，标准输出会显示文件名如何转换，但实际的文件名转换将不会进行。

`convmv`本身在向 Linux - >Windows 发送文件时也很活跃。

是编码指定错误，还是环境内的语言信息，还是原来的文件信息，在这次验证的文件中没有顺利进行。

### #3。[T4`7za`](https://linux.die.net/man/1/7za)

[是利用 7-Zip](https://www.7-zip.org/) 的解压方法。

必须在`-scs`选项中指定文件名的编码。
除此之外可以简单地执行。

```
$  # Windows
$  7za x -scsWIN 圧縮ファイル.zip
$  # DOS
$  7za x -scsDOS 圧縮ファイル.zip
$  # CP932
$  7za x -scs932 圧縮ファイル.zip 
```

Enter fullscreen mode Exit fullscreen mode

但是，我也和#2.一样，在我的环境下进行得不顺利。

### #参考信息.多个文件的批量处理

如果需要分别对多个文件运行命令(如#2)，则可以通过使用 shell 迭代来提高效率:

```
$  # fish シェルの場合:
$  for x in ���.../*; convmv --notest -f utf-8 -t shift_jis $x; end 
```

Enter fullscreen mode Exit fullscreen mode