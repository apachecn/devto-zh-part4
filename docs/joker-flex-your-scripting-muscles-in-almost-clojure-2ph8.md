# Joker:在 Clojure 中(几乎)展示您的脚本肌肉

> 原文：<https://dev.to/hlship/joker-flex-your-scripting-muscles-in-almost-clojure-2ph8>

如果你阅读了足够多的开发博客，并有一点冒名顶替综合症，
它开始看起来像我们的领域充满了优步编码员，他们可以不断地
在，比如说，用 Python 写机器学习代码，
用 JavaScript 和 React 写复杂的用户界面之间切换，然后跳下来用 c 快速组装一个设备驱动程序。为了好玩，我想象这些优步编码员然后回家用 6502 汇编写复古游戏。

这些人大概是存在的。我不是其中之一。我曾用许多语言专业地编写过代码，从 C 到 PL/1，到 Java，到 Lua。但是对于日常工作，我*专攻*。我喜欢 [**Clojure**](http://clojure.org) 。我在转换齿轮和语言方面很糟糕，不像我神话中的优步编码员。

Clojure 非常擅长它所做的事情:面向 REPL 的开发、
函数式思维、数据转换以及利用现有的 Java 库。

但是你不要用 Clojure 写 shell 脚本，即使你和我一样不擅长 Bash 脚本。

不是你不想，只是启动时间是个障碍:所有的 JVM 启动时间，加上所有的 Clojure 启动时间，再加上加载你需要的任何库的所有时间。

它加起来。

如果您正在开发一个应用程序，您可能一整天只启动一次 REPL，所以这个启动时间是无关紧要的。但是如果你正在写一个*脚本*...无论是一次性的，还是帮助您实现环境自动化的东西，您都不希望无所事事地等待脚本加载(甚至一秒钟的等待都感觉像是永远)。

这就是为什么[小丑](https://github.com/candid82/joker)是这样一个启示；Joker 是 Clojure 的一种解释方言，用 Google 的 Go 编写，目的**只是**用于脚本:我们在我的团队内部使用它来实现我们所有的自动化。

几乎所有让 Clojure 令人惊叹的东西都存在于 Joker 中，包括不可变数据类型、宏和动态类型，以及 Clojure 的绝大多数核心库。

小丑启动*非常*快:

```
> time joker -e '(println "There and back again")'
There and back again

real    0m0.050s
user    0m0.038s
sys 0m0.017s 
```

Enter fullscreen mode Exit fullscreen mode

这几乎是瞬间的事。相比之下，Clojure 有一个值得注意的启动时间:

```
> time clj -e '(println "A price must be paid")'
A price must be paid

real    0m1.011s
user    0m1.695s
sys 0m0.124s 
```

Enter fullscreen mode Exit fullscreen mode

更好的是， [Joker 包含电池](https://candid82.github.io/joker/):它有一系列内置库，涵盖所有典型任务，包括发送 HTTP 请求、JSON 和 YAML 编码、基本加密...所有你需要的东西，比如说，与亚马逊 AWS 一起工作。

一个对我来说很近很重要的特性是`joker.tools.cli`，Clojure 的
[clojure.tools.cli](https://github.com/clojure/tools.cli) 库的一个端口。这是*对 GNU 风格命令行选项的第一级*支持。

## 利用小丑

假设您在国防部找到了一份不错的工作，并且想要创建一个脚本来控制北美防空司令部的一些事情: