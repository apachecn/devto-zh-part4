# DSL(来自存储库，可能也不是最好的方式)

> 原文：<https://dev.to/bugmagnet/domain-specific-language-probably-not-the-best-way-55l9>

刚刚贴了一个编程大事记:一个简单的“[脚本语言解释器](https://github.com/axtens/sli)”，最初用 [MoonRock BASIC](http://www.rowan.sensation.net.au/moonrock.html) 编写，然后用 C#重新实现。

原始的 MoonRock BASIC 源代码是与最新的 C#一起提供的。还提供了许多示例脚本，包括经典的“99 瓶啤酒”。

```
 MACRO dec inc $1 -1
    define times 1
    defined %2
    ifyes set times {%2}

    define bottles 99
    define pronoun one
label drink
    message {bottles} bottles of beer on the wall\n
    message {bottles} bottles of beer\n
    message take {pronoun} down and pass it round\n
    inc bottles -1
    test eq {bottles} 1
    ifyes goto lastone
    message {bottles} bottles of beer on the wall\n
    message \n
    goto drink

label lastone
    message {bottles} bottle of beer on the wall\n\n
    set pronoun it
    message {bottles} bottle of beer on the wall\n
    message {bottles} bottle of beer\n
    message take {pronoun} down and pass it round\n
    message no more bottles of beer on the wall\n\n
    message no more bottles of beer on the wall\n
    message no more bottles of beer\n
    message go to the store and buy some more\n
    set bottles 99
    set pronoun one
    message {bottles} bottles of beer on the wall\n
    message \n

    inc times -1
    test eq {times} 0
    ifno goto drink
    end Burrrrp! 
```

Enter fullscreen mode Exit fullscreen mode

关于 99BoB 的其他实现，参见 RosettaCode 上的 [99 瓶啤酒](http://rosettacode.org/wiki/99_Bottles_of_Beer)。

RosettaCode 不仅仅适用于不相关的饮酒歌曲。那里展示了许多优秀的编程代码。

> Rosetta Code 是一个编程网站。这样做的目的是用尽可能多的不同语言呈现同一任务的解决方案，展示语言的相似性和差异性，并帮助一个人在学习另一种语言时在一种方法上打下基础。Rosetta 代码目前有 961 个任务，215 个草案任务，并能识别 744 种语言，尽管我们没有(也不可能)用每种语言解决每项任务。