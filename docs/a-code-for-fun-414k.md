# 有趣的代码

> 原文：<https://dev.to/devabanoub/a-code-for-fun-414k>

我很无聊。我无法为我的工作编写任何代码，甚至无法修改一些过去的工作。我觉得太懒了，什么也不想做。所以我坐下来继续思考..直到我想到一个主意。

我想自动生成的随机数需要匹配另一个自动生成的随机数多少次。所以我想到用代码来编码这个问题。正如你所知道的，我喜欢用 [Go](https://dev.to/devabanoub/why-i-love-go-h3c) 来写我的小代码。

在代码中，我生成了一个随机数，赋给 x .然后生成另一个随机数，赋给 y .如果 x！= y 然后我重新生成 y 随机数，直到和 x 随机数匹配。我为随机数的错误猜测添加了一个计数器。

我的思考结论是**我需要一定数量的猜测来匹配两个随机产生的数字**，正如你在 YouTube 上的[视频中看到的](https://youtu.be/pRKrl-83ZTc)。

我在 github 上发布了代码，这里是代码本身。

```
package main

import "fmt"
import "math/rand"

func main() {
    x := rand.Intn(33)
    y := rand.Intn(33)
    numberOfFalseGuesses := 0

    for x != y {
        fmt.Printf("x = %v and y = %v\n", x, y)
        y = rand.Intn(100)
        numberOfFalseGuesses++
    }
    fmt.Printf("finally x = y = %v\n", x)
    fmt.Printf("number of false guesses are %v", numberOfFalseGuesses)
} 
```

Enter fullscreen mode Exit fullscreen mode

下次博客见。保持联系。