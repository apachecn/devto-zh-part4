# øxoposɇcmɇɇtuᵽ[0x 75]挑战报道

> 原文：<https://dev.to/jpdias/oxoposc-mtu-0x75-challenge-write-up-4g7k>

“总部位于波尔图的øxoposɇc 集团是由热衷于安全的 g33ks 创立的。meetup 的主要任务是通过利用小组成员的专业知识和技能来讨论和解决紧急的安全问题。”这是@aap 对 0x72 和 0x73 meetup 版本的挑战的书面报道。

聚会每月举行一次，欢迎[加入](https://www.meetup.com/0xOPOSEC/)。

这个挑战是和我的朋友*(也是博士生导师)* [@hugosf](http://hugosereno.eu/) 合作解决的。

## 安娜丽丝. eml

这一切都始于一个指向 Dropbox 存储文件的 URL。名为`Análise.eml`的文件是一封电子邮件。在雷鸟中打开它，出现了以下文本和图像:

> 你好，
> 
> 我需要一个能分析电脑的人，你能帮忙吗？
> 
> PS:我们将采用通常的保密信息交流方式！
> 
> ——a**挥发性**是生命的常数！

[![footer](img/b3b2d631299b1c867f552a2ba23adbbf.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--k0gfJhKW--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://jpdias.img/oposec/banner.jpg)

分析电子邮件的标题，没有发现任何有用的信息。

现在关注横幅图像，想到的是使用某种隐写术来隐藏某些东西(如电子邮件文本中暗示的)。

仅仅使用没有密码的`steghide`就可以提取隐藏的信息:

```
$ steghide extract -sf oposec/banner.jpg
Enter passphrase: 
wrote extracted data to "info.txt".

$ cat info.txt

0c60fd56872251909cb07a749b03a34a56e1edac memdmp.zip

https://www.dropbox.com/s/<location>/memdmp.zip?dl=0 
```

Enter fullscreen mode Exit fullscreen mode

转到给定的 URL，下载了一个大约 132 MB 的`memdmp.zip`文件。

```
$ unzip memdmp.zip 
Archive: memdmp.zip
  inflating: memdmp                  

$ file memdmp     
memdmp: data 
```

Enter fullscreen mode Exit fullscreen mode

提取它会给我们一个`memdmp`数据文件。虽然这个名字表明是一个内存转储，但这是我第一次遇到这种挑战，所以帮助不大。

浪费一点时间分析`strings`和摆弄`grep`输出，一些东西开始出现，比如几个对 [WeTransfer](https://wetransfer.com/) 的引用。在我混乱的头脑中，像信息泄露这样的事情开始变得流行起来。

在失去了围绕这个想法的一些东西之后，其他一些东西开始变得奇怪，即这个工具的引用:`C:\Documents and Settings\Forense\My Documents\Downloads\volatility-2.0.standalone\volatility.exew`。稍微搜索一下，这个知识库出现了:[Volatility foundation](https://github.com/volatilityfoundation/volatility)的 Volatility。

读了一点之后，一切都开始有意义了。很明显，有几个工具是用来分析内存转储的！咄！

稍微搜索一下这个工具的 noob 指南，找到了这个:[易失性内存分析的第一步](https://medium.com/@zemelusa/first-steps-to-volatile-memory-analysis-dcbd4d2d56a1)。

所以，按照教程，第一件事就是在 dump 上运行`imageinfo`。

```
$ python volatility/vol.py -f memdmp imageinfo
Volatility Foundation Volatility Framework 2.6.1
INFO : volatility.debug : Determining profile based on KDBG search...
          Suggested Profile(s) : WinXPSP2x86, WinXPSP3x86 (Instantiated with WinXPSP2x86)
                     AS Layer1 : IA32PagedMemory (Kernel AS)
                     AS Layer2 : FileAddressSpace (/home/jpdias/Downloads/memdmp)
                      PAE type : No PAE
                           DTB : 0x39000L
                          KDBG : 0x8054cde0L
          Number of Processors : 1
     Image Type (Service Pack) : 3
                KPCR for CPU 0 : 0xffdff000L
             KUSER_SHARED_DATA : 0xffdf0000L
           Image date and time : 2019-06-09 16:04:21 UTC+0000
     Image local date and time : 2019-06-09 16:04:21 +0000 
```

Enter fullscreen mode Exit fullscreen mode

这样，我们就可以得到一个建议的概要文件，用于下一阶段的分析。使用`$ python volatility/vol.py -f memdmp --profile=WinXPSP2x86 pstree`我们可以得到一个在 memdumped 机器上运行的所有进程的树，但是没有奇怪的事情再次出现。

再往前走，也许这台机器会和一些奇怪的东西说话，并分析它们之间的联系，但还是没有任何有用的东西出现。大多数呼叫来自 Windows 内部(`svchost.exe`)。

```
$ python volatility/vol.py -f memdmp --profile=WinXPSP2x86 connscan
Volatility Foundation Volatility Framework 2.6.1
Offset(P) Local Address Remote Address Pid
---------- ------------------------- ------------------------- ---
0x01fd28d8 10.0.2.15:1032 <ip1>:443 1040
0x02315e68 10.0.2.15:1033 <ip2>:80 1040
0x0231ce68 10.0.2.15:1031 <ip3>:80 1040
0x151408d8 10.0.2.15:1032 <ip4>:443 1040
0x152cbe68 10.0.2.15:1033 <ip5>:80 1040
0x154d2e68 10.0.2.15:1031 <ip6>:80 1040 
```

Enter fullscreen mode Exit fullscreen mode

甚至在一些最奇怪的地方浪费了一些时间运行 nmap 之后，什么也没有。

之后， *@hugosf* 有了个主意，去看看*剪贴板*怎么样。

```
$ python volatility/vol.py -f memdmp --profile=WinXPSP2x86 clipboard
Volatility Foundation Volatility Framework 2.6.1
Session WindowStation Format Handle Object Data  
---------- ------------- ------------------ ---------- ---------- --------
         0 WinSta0 CF_UNICODETEXT 0x30115 0xe146f0b8 NOTEPAD
         0 WinSta0 CF_LOCALE 0x5400fb 0xe1b75620       
         0 WinSta0 CF_TEXT 0x1 ----------
         0 WinSta0 CF_OEMTEXT 0x1 ---------- 
```

Enter fullscreen mode Exit fullscreen mode

剪贴板指向`notepad`。使用波动率进行检查:

```
$ python volatility/vol.py -f memdmp --profile=WinXPSP2x86 notepad  
Volatility Foundation Volatility Framework 2.6.1
Process: 1864
Text:
NOTEPAD

Text:
https://bit.ly/<id>

NOTEPAD 
```

Enter fullscreen mode Exit fullscreen mode

耶！另一个网址。检查这个 URL 会将我们带到一个 dropbox 托管的 TXT 文件，其内容如下:`synt{Z8Z%QHZC%E5PXF!}`。

在检查出标志的预期格式类似于 flag{…..}它似乎接近我们所拥有的。在 CyberChef 上检查后，这是一个简单的 ROT13 密码:

> > 标志{ M8M %转储%R5CKS！}

原来如此。挑战完成！

这是一个有趣的谜语，感谢@aap 的设计！另外，感谢@hugosf 在我应该写论文的时候容忍我沉迷于安全。

##### *我们一起努力。*