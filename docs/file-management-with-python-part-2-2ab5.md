# 用 Python 管理文件:第 2 部分

> 原文：<https://dev.to/marvinkweyu/file-management-with-python-part-2-2ab5>

这是 python 文件管理系列的第二部分。我们从上次离开的地方开始[第 1 部分](https://marvin.hashnode.dev/file-management-with-python-ck02cpxu30010fqs1stv451gx)，在那里我们根据扩展名组织文件。那么，我们开始吧。

有时候，组织文件可能需要的不仅仅是知道它们的扩展名。例如，假设一个目录中的所有文件都是同一类型的。是否。pdf，。doc，. mp4 等等。在这篇文章中，我们将我们的组织向前推进了一点。假设您有一个包含幻灯片的文件夹。ppt)。在这种情况下，你刚刚收到了一大堆讲座文件，但它们并不容易浏览。所以第一节课不是用一整张幻灯片，而是把它们分解成一张幻灯片。在这种情况下，我们的文件夹假定如下所示。

```
DataStructures/
|_Datastructuressession1Slide1.ppt
|_Datastructuressession1Slide2.ppt
|_Datastructuressession1Slide3.ppt
|_Datastructuressession2Slide3.ppt
|_Datastructuressession7Slide8.ppt
|_Datastructuressession9Slide2.ppt

... and so on 
```

Enter fullscreen mode Exit fullscreen mode

发生什么事了？我们拿到了幻灯片，但它们一团糟。你必须在文件夹中寻找你刚读过的那张幻灯片。我们应该让这变得更简单，让我们根据会议来组织所有的幻灯片。还记得我们在上一篇文章中是如何生成随机文件的吗？我们将做同样的事情，只是这一次，所有的文件将是相同的类型。看看这里的，快速复习一下。我们的文件看起来很像`create_random_files.py`。

```
#!/bin/python3
# create_lectures.py

import os
from pathlib import Path

sessions = [str(x) for x in range(1,21)]  # create 20sessions 
sessions = [str(0)+item if int(item) < 10 else item for item in sessions]

# Datastructuressession01Slide1.ppt

# get into the DataStructures directory
os.chdir('./DataStructures')

for item in sessions:
    # create 20 slides for each session
    for num in range(21):
        file_to_create = f"Datastructuressession{item}Slide{num}.ppt"
        Path(file_to_create).touch() 
```

Enter fullscreen mode Exit fullscreen mode

好吧好吧。我承认这次我在文件数量上有点过火了。那是相当多的数字。

让我们注意这一行:

```
sessions = [str(0)+item if int(item) < 10 else item for item in sessions] 
```

Enter fullscreen mode Exit fullscreen mode

在这之前的一行是一个 20 个数字的列表，但是这里有一个问题，我们将每个数字转换成字符串。为什么？如果数字 0 小于 10，我们将把它作为一个字符串附加到每个数字上。这将使 10 位以下的每个数字看起来像这样；01, 02, 03,...诸如此类。

上面，我们为 20 个会话中的每个会话创建了许多文件。

我们接下来要做的很简单，根据会话将这些文件分组。

```
# clean_reading.py
#!/bin/python3
# move files to directories according to the file name pattern

import os
import shutil

# get into the Datastructures directory
os.chdir('./DataStructures')

# Datastructuressession01Slide1.ppt
for f in os.listdir("."):
    folder_name = f[14:23]
    # print(folder_name)

    if not os.path.exists(folder_name):
        os.mkdir(folder_name)
        shutil.move(f, folder_name)
    else:
        shutil.move(f, folder_name) 
```

Enter fullscreen mode Exit fullscreen mode

唯一需要解释的一行可能是:

```
folder_name = f[14:23] 
```

Enter fullscreen mode Exit fullscreen mode

我们已经计算了文件夹的命名字符数。在这种情况下，我们分解了文件名的字符串`Datastructuressession01Slide1.ppt`,从那里我们得到会话的第一个`s`是整个字符串中的字符数`14`,而最后一个会话计数将是字符数`23`,从我们生成的文件翻译过来就是数字`20`的最后一位。
运行这个程序可以快速、干净地获取各自会话中的所有幻灯片。人们想要做的还有很多，比如让程序知道包括哪些会话，而不需要手动键入字符位置，但是有更高级的工具可以做到这一点，尤其是在 UNIX 环境下。随意做一些调查，找到最适合你的工作。作为提醒，这里有一个用 python 制作的很酷的 GUI [分类器](https://github.com/giantas/sorter)。保罗在这方面做得很棒。有问题吗？请务必在评论区联系我们。像往常一样，所有这些代码都可以在[绿色代码](https://github.com/TheGreenCodes/FileManagement)中找到。