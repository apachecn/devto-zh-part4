# 我的 Julia VM 和我学到的东西

> 原文：<https://dev.to/alecsanchez/my-julia-vm-and-what-i-learned-3n4i>

你好，我是亚历克！这是我的第一篇帖子，所以如果你有任何建议或反馈，请随时评论！

GitHub 回购:

## ![GitHub logo](img/a73f630113876d78cff79f59c2125b24.png) [ F0x1fy ](https://github.com/F0x1fy) / [茱莉亚-虚拟化-CPU](https://github.com/F0x1fy/Julia-Virtualized-CPU)

### 朱莉娅虚拟化 CPU，只是一个奇怪的激情项目

<article class="markdown-body entry-content container-lg" itemprop="text">

# 虚拟化 CPU

朱莉娅虚拟化的 CPU，可能会被用于我正在制作的视频游戏。这是作为学习 Julia 的一个有趣的小项目，并不意味着是汇编或 CPU 如何真正工作的忠实再现。虽然它忠实于某些部分，但它并不意味着是 100%完美的虚拟化。我可能会决定改变许多限制，这取决于我是否想回到这个项目中来。

# 基础设施

CPU 有四个 16 位寄存器，A(累加器)、B(开放寄存器)、C(计数寄存器)和 D(数据寄存器)。还有三个 16 位指针，RP(读指针)、WP(写指针)和 SP(堆栈指针)。目前只有一个中断，UOI，或用户输入溢出中断，现在默认返回到第一行代码，以后可能会改变。中央处理器…

</article>

[View on GitHub](https://github.com/F0x1fy/Julia-Virtualized-CPU)

## 简介

你可能会问“Alec，为什么你一直在说你三个月前做的一个项目，一个月都没有更新？”

嘘。出于一些奇怪的原因，我无条件地热爱这个项目，尽管它有很多很多的缺陷。

## 我的 VM 是什么？

我创建了一个 Julia 虚拟机(VM)来运行我自己的定制指令集。我为什么要这么做？它实际上让我可以同时学习 Julia 和 Assembly！让我们更深入地探究这是为什么，以及它如何让我的学习受益匪浅。

对于 Julia，我想要一些项目想法作为学习 Julia 的借口，因为我当时(现在仍然)对这门语言非常感兴趣。我也对学习汇编感兴趣，但是当前的通用语言看起来令人望而生畏，这让我有些气馁。我的虚拟机是我自己设计的，这样我可以更容易地学习汇编。我知道我不能让它接近我想要的 x86，但是简单的开始总比没有开始好。

## 代码示例及解释

这里有一句“你好，世界！”x86 (Windows)中的示例:

<header>

# ![](img/01c67cd39e9a8e551fcb75e1091225e9.png) [ 回答 re:如何在 Windows 下用汇编语言编写 hello world？](https://stackoverflow.com/questions/1023593/how-to-write-hello-world-in-assembler-under-windows/1029093#1029093)

Jun 22 '09[![](img/e3f0373ec76330150a340eacd410b600.png)126![](img/f7bb704c8c93dfae05d2b57012ed2754.png)](https://stackoverflow.com/questions/1023593/how-to-write-hello-world-in-assembler-under-windows/1029093#1029093) </header>

这个例子展示了如何直接进入 Windows API，而不是链接到 C 标准库中。

```
 global _main
    extern  _GetStdHandle@4
    extern  _WriteFile@20
    extern  _ExitProcess@4

    section .text
_main:
    ; DWORD  bytes;    
    mov     ebp, esp
    sub     esp, 4

    ; hStdOut = GetstdHandle( STD_OUTPUT_HANDLE)
    push    -11
    call    _GetStdHandle@4
    mov     ebx,
```

…<button class="ltag__stackexchange--btn" type="button">[Open Full Answer](https://stackoverflow.com/questions/1023593/how-to-write-hello-world-in-assembler-under-windows/1029093#1029093)</button>

(代码从答案中抽出)

```
 global _main
    extern  _GetStdHandle@4
    extern  _WriteFile@20
    extern  _ExitProcess@4

    section .text
_main:
    ; DWORD  bytes;    
    mov     ebp, esp
    sub     esp, 4

    ; hStdOut = GetstdHandle( STD_OUTPUT_HANDLE)
    push    -11
    call    _GetStdHandle@4
    mov     ebx, eax    

    ; WriteFile( hstdOut, message, length(message), &bytes, 0);
    push    0
    lea     eax, [ebp-4]
    push    eax
    push    (message_end - message)
    push    message
    push    ebx
    call    _WriteFile@20

    ; ExitProcess(0)
    push    0
    call    _ExitProcess@4

    ; never here
    hlt
message:
    db      'Hello, World', 10
message_end: 
```

这里有一句“你好，世界！”我的指令集中的例子(在最少的行中):

```
strwrite Hello World!
print 12 
```

这明显更简单，不是吗？有更好的方法可以做到这一点(甚至在我自己的指令集中)，但这非常好地突出了两者之间的明显差异。我根据最有助于我学习的内容量身定制了这种汇编语言，尽管有一些令人讨厌的方面(如`print`指令和跳转如何是绝对行)，但我相信它确实帮助我比以前更好地理解了汇编。虽然我没有学习特定的通用指令集，但我仍然学习了代码背后的整体概念，以及汇编与高级语言的不同之处，但有时也很相似。

我的虚拟机是一个蹩脚的，未经优化的烂摊子，我明白。它不是一个现代的、优化的虚拟机。它是为了让我同时学习 Julia(以及它所有的怪癖，比如你不能为一个变量名多次设置类型，即使它们是相同的，即使它们在不同的局部范围内)和汇编。我相信它达到了令人难以置信的目标，我全心全意地推荐其他初学者做类似的事情。这是一个很好的学习高级语言的方法，同时学习汇编代码背后的概念，以及如何在其他指令集和架构中应用它。

## 结论

这个项目，虽然 VM 并不真正接近真实 CPU 的功能和指令集的工作方式(特别是在操作系统级集成上)，但我相信它帮助我在高级和低级两个方面成长为一名程序员。在学习 Julia 的过程中，我能够在舒适的环境中轻松地理解 Assembly。这真的是我做过的最好的个人项目(也是迄今为止我唯一完整完成的项目)，我建议其他初学者不要无条件地效仿我的做法，因为我是一个会浪费时间写文章的人，但建议他们理解我为什么要做这个项目以及我对这个项目的想法，然后用这种心态创造他们自己的版本，帮助他们学到最好的东西。

感谢您的阅读，祝您度过愉快的一天！