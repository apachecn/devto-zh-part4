# 逆向工程游戏文件

> 原文：<https://dev.to/crimsonmed/reverse-engineering-game-files-217e>

# 简介

查看来自法国 mmorpg **Dofus** 的逆向工程游戏文件

在上一篇讨论 d2i 的文章中，我没有提到如何反转文件和理解其结构的思考过程。我想这可能会引起一些人的兴趣。

这是上一篇文章背后的思考过程:[http://medericburlet.com/dofus-d2i-reverse-engineering/](http://medericburlet.com/dofus-d2i-reverse-engineering/)

# 关于 d2i 文件

在做了一些游戏模拟的工作后，我知道像武器名称、对话、地图、音乐等信息都存储在本地文件中。

我知道 d2i 文件是负责 npc 对话的文件，因此我决定试着理解它们。你可以使用我使用过的相同文件来学习本教程，这个文件可以在这里找到: [i18n_fr.d2i](https://github.com/crimson-med/FastD2IReader/blob/master/Example/i18n_fr.d2i)

# 模式识别

知道我们正在处理一个视频游戏，数据必须以一种容易阅读的方式存储，否则在本地保存它们就没有意义了。毕竟本地游戏资源的原则是加快加载速度。

我的第一个方法是在 atom 中打开文件，如果运气好的话，它可能是像 JSON 这样可读的文件。结果文件的一部分是可读的。幸运的是，这意味着数据没有加密。

[![Readable d2i lines](img/e2ee8d909fe9261cc6f34379e8768e05.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ZAh39JBm--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://medericburlet.com/wp-content/uploads/2019/07/Screenshot-2019-07-03-at-4.28.38-PM.png)

然而，有些行完全不可读。

[![Unreadable d2i lines](img/29a909edc6031ef2658541e187fd0952.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--NZ2jnA2A--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://medericburlet.com/wp-content/uploads/2019/07/Screenshot-2019-07-03-at-4.30.51-PM.png)

我的下一步是打开它进入一个十六进制编辑器，因为混乱的文本意味着它不是一个可读的文本格式。

在用十六进制编辑器打开文件时，一些东西立刻吸引了我的注意！

有一个模式至少是一个可见的模式！我不知道它代表什么，也不知道如何解释它，但至少还有希望。

[![visible pattern on right panel](img/d6daf492b078182af4d84baa3205dd11.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--lu4ydc_D--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://medericburlet.com/wp-content/uploads/2019/07/Screenshot-2019-07-03-at-4.37.19-PM-1024x265.png)

我们可以在右边的面板上清楚地看到一些重复，如果我们查看十六进制数据，我们可以看到许多以 **00** 或 **00001** 结尾的 **2 字节**组。

# 理解十六进制数据

所以我首先决定看看文件的开头，注意到第一个字符串的开头在一组 **4 字节**和 **2 字节**之后。

[![first bytes of the file](img/dfdd3c9ab6dbc83b9e165ee2cdea842d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--xWqt3HEp--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://medericburlet.com/wp-content/uploads/2019/07/Screenshot-2019-07-03-at-9.00.40-PM.png)

这个数字看起来非常**【幸运】**，因为这很容易是 **3，2 字节整数**或 **1，4 字节整数**和一个 **2 字节整数**。

在查看文件开头的时候，我注意到了一些有趣的东西。文件中的第一个字符串被写入两次(红色)，我们注意到在构成字符串开头的一组 **4 个字节**中，它们都有第一个 **2 个不可读字节**(蓝色)

[![string start pattern](img/b5fe30c2e74967e8174de531eafacb70.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--S8h1Buwm--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://medericburlet.com/wp-content/uploads/2019/07/Screenshot-2019-07-03-at-9.08.23-PM.png)

**0022** 转换成 int 给我们 **34** 恰好是字符串的长度。进一步看，我们可以看到每个字符串都以一个指定字符串长度的 **2 字节**整数开始。

如果我们把第一个 **4 字节**看作一个整型数，我们得到的数字是: **26343905 (0191F9E1)**

如果我们选择所有的字节，直到我们到达所有字符串列表的末尾，我们得到相同的数字。所以 d2i 文件的第一个 **4 字节**代表字符串数据的大小。

[![size of string list](img/ae97c020f4577911b99a78d49865be5a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--1j3SNo3f--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://medericburlet.com/wp-content/uploads/2019/07/Screenshot-2019-07-03-at-10.01.26-PM.png)

如果我们把接下来的 **4 个字节**看作一个 int，它们使得: **1957787 (001DDF9B)** 这将我们带到另一个字符串列表的开始

[![size of indexes](img/5318c27fcab4af8ca9adb7563158318f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--2Dhtshvt--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://medericburlet.com/wp-content/uploads/2019/07/Screenshot-2019-07-03-at-10.06.36-PM.png)

# 十六进制指针。。。等等什么？？？

是的，现在我们要去无聊或有趣的东西。如果我们回到第一个字符串列表的末尾，我们会注意到十六进制数据中的模式。第一个 **4 字节**组就是这样一个完美的集合: **00000001**

如果我们仔细观察，我们会发现一个模式:

[![emerging id pattern](img/8befcc04fa3e8a3faaabffab4ca13278.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--_NBnAmjJ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://medericburlet.com/wp-content/uploads/2019/07/Screenshot-2019-07-03-at-10.18.59-PM.png)

我们在这里看到，我们有一个递增的值，与 ID 的原理相同。这是有意义的，因为这些文件是游戏的数据存储。

我们还注意到在每个 **ID** 之后重复出现的 **01** 。直到下一个 **ID** 我们还有另外一个 **8 个字节**我们可以很容易的看出它们也是两个数字:
**4 (00000004)** 和 **40 (00000028)**

从那里，我只是跟着直觉寻找 **ID 01** 我们得到数字 4，它是我们文件中第一个字符串的位置。如果我们计算数据大小的前 4 个字节，则位置为 4。第二个数字 40 如果我们从第 40 个字节开始看，我们会看到它和第一个是一样的字符串，但是没有大写字母或重音符号。

由此我推出了下面的格式，在我之前的博文中已经解释过了:[http://medericburlet.com/dofus-d2i-reverse-engineering/](http://medericburlet.com/dofus-d2i-reverse-engineering/)

[![image of file structure](img/ff81a82758340d52ad4c6047974b2f78.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--cGRsgWXv--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://medericburlet.com/wp-content/uploads/2019/07/indexes.png)

ID(橙色)，音调符号存在？(青色)、字符串指针(棕色)、音调符号指针(蓝色)

然后，我检查第二个 ID，看它是否对应于第二个字符串，确实如此。从那时起，我写了一个简单的解析器，然后它变成了一个完全成熟的阅读器:[https://github.com/crimson-med/FastD2IReader](https://github.com/crimson-med/FastD2IReader)

# 结论

这是一个非常有趣和娱乐性的挑战，它迫使我使用逻辑以及理解我的环境和跟随我的直觉。重要的是要记住，文件来自游戏，因此阅读时间和可访问性一样重要。

## ![GitHub logo](img/a73f630113876d78cff79f59c2125b24.png)/[fastd 2 ireader](https://github.com/crimson-med/FastD2IReader)

### 基于逆向工程文件的快速. d2i (D2I) Dofus 文件阅读器。

<article class="markdown-body entry-content container-lg" itemprop="text">

# FastD2IReader

出于好玩，我对 Dofus 的. d2i 文件进行了逆向工程，决定为它们制作一个简单的阅读器。

这是基于 **2.10** 版本。

# 如何使用

```
        Dim MyReader As New FastReader("MyFile.d2i", True)
        MyReader.GetText(41903)
        MyReader.Dispose()
```

# 正常负载与快速负载

**正常负载:**

[![Slow Load](img/76289c9fb3e68a1ad5f0e826612e5d11.png)](https://raw.githubusercontent.com/crimson-med/FastD2IReader/master//Screens/sload.PNG)

[![Slow Ram](img/2fe9fe2d065f07949e658f13442fa1ca.png)](https://raw.githubusercontent.com/crimson-med/FastD2IReader/master//Screens/sram.PNG)

**快速加载:**

[![Fast Load](img/19813fa173bb5bd4082d31b62228839e.png)](https://raw.githubusercontent.com/crimson-med/FastD2IReader/master//Screens/fload.PNG)

[![Fast Ram](img/ff098d5744cd0d1219bb2e816ae26eed.png)](https://raw.githubusercontent.com/crimson-med/FastD2IReader/master//Screens/fram.PNG)

# D2I 文件

* * *

## 介绍

D2I 格式是 Ankama 用来储存字串(字串)的格式，例如项目或对话方块名称等。该文件因语言而异，但结构保持不变。

## La 结构

### 档案

该文件由四个主要部分组成:

*   Les 数据
*   Les 指数
*   消息 UI
*   额外数据

这些部分中的每一部分都由一个索引(“t0”4 字节【t1])组成，指示除了额外数据以外的后续数据的大小。

### *Les 数据*

日期由 3 个部分组成:

*   …

</article>

[View on GitHub](https://github.com/crimson-med/FastD2IReader)

来源:[http://medericburlet . com/reverse-engineering-gaming-files-d2i-from-dofus/](http://medericburlet.com/reverse-engineering-gaming-files-d2i-from-dofus/)