# 你好，终点站！- Windows 版

> 原文：<https://dev.to/terabytetiger/hello-terminal-windows-edition-37l3>

[跳转到信息图表摘要！](#Infographic)

# [T1】简介](#intro)

终端是一个神秘而可怕的地方，尤其是对于新开发者来说。即使上了一学期的服务器课，我仍然对终端感到不安——尤其是 Windows，因为感觉我们在课堂上学到的每一个命令都不起作用(因为课堂上的一切都是为了 BASH - Bourne-again Shell)。在这篇文章中，我想介绍一下 Windows 终端，我希望我已经介绍过了，这样你就不会像我一样害怕终端。

# 术语

## 目录

“目录”是文件夹的别称。当你打开文件浏览器时，所有的小文件夹图标都是你的目录。

[![file icons in File Explorer for Windows](img/9c38556c9ffaab4c90548b199a9594c2.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--rr-7HFSU--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/8ntu60hpjb6ooq8oam8i.PNG)

在浏览目录时，有两个特殊的快捷目录，分别用`.`和`..`表示。

### [T1。-当前目录](#-current-directory)

`.`是当前目录的快捷方式。在 CSS 或 JavaScript 导入语句中构建网站时，您可能会看到这种情况:

```
<link href="./style.css" rel="stylesheet" type="text/css"> 
<script src="./javascriptFile.js"> 
```

Enter fullscreen mode Exit fullscreen mode

### ..-父目录

`..`是表示“我的父目录”的快捷方式，可用于在导入 HTML 格式的文件时“跳转”文件夹:

*在本例中，我们假设有标记为`html`、`css`和`javascript`的文件夹，文件根据类型*和
放置在这些文件夹中

```
<link href="../css/style.css" rel="stylesheet" type="text/css"> 
<script src="../js/javascriptFile.js"> 
```

Enter fullscreen mode Exit fullscreen mode

## 路径

如果您输入命令`echo %PATH%`，您将看到一个分号分隔的文件路径列表，每个路径(可能)看起来有点像`C:\Program Files\Program Name\`或`C:\WINDOWS\system32\`。这是您的**路径环境变量**——也就是检查命令的位置列表。

每当你调用一个命令时，Windows 需要知道这个命令做什么——所以它开始查看 Path 变量，直到找到你试图调用的命令。

你可以把它想象成一排盒子，你需要在其中一个盒子里找到一些东西。你会开始一次检查一个盒子，直到你找到你要找的东西(这时你会停止寻找)。在同一命令有多个实例的情况下，这一点很重要(即如果您有 VS 代码和 VS 代码内部人员，使用`code .`将总是触发路径中最先列出的那个)。

如果您想在您的当前目录中运行一个命令，但是它也在您的路径中，您可以通过使用`.\targetCommand.exe`来定位当前目录的版本(文件扩展名会有所不同)。

# 命令

## 导航

住在候机楼最重要的部分就是能够四处走动！我最常用的两个命令是:

### dir

`dir`相当于寻找最近的路牌，但也注意到街上的房子。当你运行这个命令时，Windows 会让你知道你在哪里，你周围有什么——也就是你当前的目录。您应该会看到这样的内容:

```
Volume in drive C is OS
Volume Serial number is 12AC-12A6

Directory of {{ Your Location }}
07/16/2019  12:05 PM    <DIR>          .
07/16/2019  12:05 PM    <DIR>          ..
06/04/2019  02:08 PM    <DIR>          public
07/04/2019  10:44 AM               522 README.md
07/04/2019  10:48 AM             1,459 package.json
               2 File(s)         1,981  bytes
               3 Dir(s)  315,250,264,546 bytes free 
```

Enter fullscreen mode Exit fullscreen mode

这里需要注意的关键点是:

*   第三列中的`<DIR>`表示实体是否是目录。
*   第四列是以字节为单位的文件大小(只有文件才有这个数字)
*   我们的朋友`.`和`..`也在这里！

### cd

现在我们知道我们在哪个目录下，但是我们如何移动呢？

输入`cd`—“更改目录”的缩写——后跟您要移动到的目录的路径。

例子:

```
cd ..                           // Move to the parent directory

cd .\public                     // Move to the public folder

cd ..\..                        // Move to the parent directory's parent

cd ..\Desktop\Documents\Folder  // Move to the parent directory
                                // then a directory "Desktop" in that 
                                // parent directory. 
```

Enter fullscreen mode Exit fullscreen mode

## 创建目录

**命令:** `mkdir subfolder`

这将在当前目录中创建一个名为“子文件夹”(可以使用任何名称)的目录。当您运行这个命令时，不要忘记您将需要`cd`进入它(您不会被自动带到那里)。

## 创建文件

**命令:** `type nul > doc1.txt`

如果你曾经看过教程参考`touch filename.txt`，这是 Windows 中的等效命令。接下来会创建一个名为“doc1.txt”的空文件(或者您提供的任何文件名+扩展名)。

我经常忘记包括文件扩展名🙃

*感谢 [@nicolaerario](https://dev.to/nicolaerario) 指出`echo .`将创建一个空行，并且`type nul`是该命令的正确开始！*

## 复制

**命令:** `copy doc1.txt doc2.txt`

该命令将创建第一个文件的副本，并提供第二个文件名(&可选位置)。在上面的例子中，doc1.txt 将被复制到 doc2.txt 中。

您也可以使用任一参数指定位置:

```
copy index.html .\public\index.html         // This creates a copy of index.html in
                                            // the public subfolder named index.html

copy ..\Desktop\file1.txt .\public\test.txt // Copy file 1.txt from the 
                                            // Desktop folder's file1.txt into
                                            // the current directory's public subfolder
                                            // with the name test.txt 
```

Enter fullscreen mode Exit fullscreen mode

## 移动

**命令:** `move doc1.txt .\subfolder`

移动允许您将指定的文件移动到指定的目录。在本例中，doc1.txt 被重新定位到名为“subfolder”的子文件夹中。您可能已经注意到，这次我指定了`.\subfolder`，而不仅仅是`subfolder`。您不需要包含`.\`，但是每当我引用一个目录时，我倾向于这样做，这样如果需要的话，我可以更容易地再次找到这个命令(在下面的“历史”中有更多的信息)

示例:

```
move index.html public     // Move index.html into the public subfolder

move index.html ../Desktop // Move index.html to the parent directory's 
                           // Desktop directory 
```

Enter fullscreen mode Exit fullscreen mode

## 删除文件

**命令:** `del doc3.txt`

每个人最喜欢做的事:倒垃圾！

`del`类似于在文件资源管理器中高亮显示文件并按 delete 键。在上面的例子中，我们从当前目录中删除了 doc3.txt。

您也可以连锁删除命令:

```
del doc1.txt doc2.exe doc3.js  // Delete doc1.txt, doc2.exe, and doc3.js

del *.txt                      // Delete all files in the current directory 
                               // with the .txt extension 
```

Enter fullscreen mode Exit fullscreen mode

**注意，这不会将文件发送到回收站，也不会对目录**起作用

## 删除(清空)目录

**命令:** `rmdir subdirectory`

`rmdir`将让你删除一个指定的目录，**但只有当目录是空的**。如果目录不为空，该命令将抛出一个错误，让您知道。

有一种方法可以告诉 rmdir 删除一个非空的目录，但是为了安全起见，我会推荐使用文件浏览器(或者使用`del`手动清除目录，然后使用`rmdir`)。

```
rmdir sub  // Delete the empty sub-directory 'sub' from the current directory 
```

Enter fullscreen mode Exit fullscreen mode

## 标签完成

最有用的命令行技巧之一是使用 tab 键来省去键入文件/目录名的麻烦。

例如，如果您有`C:\Desktop\User\directory-name\reallyLongFileNameYouDontWant2Type.txt`，您可以使用以下按键:

```
C:\D{tab}\U{tab}\dir{tab}\reall{tab}

// At this point you should see 
// C:\Desktop\User\directory-name\reallyLongFileNameYouDontWant2Type.txt 
// if all the directories and files exist 
```

Enter fullscreen mode Exit fullscreen mode

**注意:**如果按 tab，将返回按字母顺序排列的第一个结果。您可以重复按 tab 键来循环所有的自动完成功能(当它到达列表的末尾时，它将再次从头开始)。

*Shift + Tab 将向相反方向循环*

## 历史

如果您正在输入与先前输入的命令相同的命令(或想要使用过去的命令作为起点/参考点)，您可以使用⬆键和⬇键浏览过去的命令。

类似地，如果你按下➡键，你可以一次一个字符地输入最后一个命令。

# 关闭

现在，您已经拥有了在命令行上运行所需的一切😄

如果你有任何问题，我非常乐意帮忙！

# 信息图汇总

以下是信息图中命令的摘要:

[![Infographic summarizing the above commands.](img/57439fdbfa61395a8fd4dc67d46b4f1d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--dFIQiiq4--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ubrqxp8b4l0bwtmc9a8z.png)