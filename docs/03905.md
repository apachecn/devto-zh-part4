# 第 3 集-配置结构

> 原文：<https://dev.to/victoravelar/episode-3-the-configuration-struct-5001>

### 读之前

> 感谢你花几分钟时间阅读这篇文章，我只是想让你知道，我的想法有时就像意大利面条，所以如果有些事情不是 100%清楚，请提问，我会非常乐意回答，很可能我们都会有新的东西来传播。
> 😄

无论何时你用任何编程语言创建一个库，规划如何实现你的配置对象都是非常重要的，因为在我看来，它严重影响了代码的外观和行为。

因此，对于我们可爱的 API 客户端，我们将有一个配置结构，其中我们将传递以下值:

1.  由环境变量或秘密填充。
2.  影响整体代码行为的变量

第一个几乎是不言自明的，你不想从[金库](https://www.hashicorp.com/products/vault)中获取你的秘密(不是这个没有赞助，只是碰巧是我熟悉的那个)，或者到处要求`os`包只是为了能够调用`Getenv`。

对于我们的用例，我们有下面的配置结构

```
type Config struct {
    APIKey       string
    InsecureOnly bool
} 
```

Enter fullscreen mode Exit fullscreen mode

正如您所看到的，我们为前面描述的每个场景都提供了一个属性，这很方便，`APIKey`可能会从一个“安全的”源填充，例如，从您的版本控制系统中是看不到的，除非您希望每个人都能使用您的 dev.to 文章。

`InsecureOnly`是在执行对 API 的请求时会影响 API 客户端行为的东西。

## 构造函数

正如我们将要看到的，go 中的构造函数允许你控制如何构建你的客户端，用户可能会直接实例化这个结构，但是为了尽可能地提供帮助，我们也将提供一种友好的方式来构建一个`usable` Config 对象。

```
func NewConfig(p bool, k string) (c *Config, err error) {
    if p == true && k == "" {
        return nil, ErrMissingRequiredParameter
    }

    return &Config{
        InsecureOnly: !p,
        APIKey:       k,
    }, nil
} 
```

Enter fullscreen mode Exit fullscreen mode

我们的配置构造函数接受一个布尔值(p)和一个字符串(k)，其中 p 表示我们是否将使用受保护的端点，k 是我们的 API 密钥，现在一旦我们提供了这些值，构造函数将根据我们的输入进行检查和反应。

这项检查将节省我们一些时间，也将节省一些不必要的请求，如果我们说我们想要访问受保护的端点，但我们没有提供密钥，那么很明显，当我们执行请求时，API 将会给我们一个可爱的`http.StatusUnauthorized`。

现在，如果 p 为假，我们不关心密钥，因为公共端点不需要密钥。

现在，要小心，因为 p 是我喜欢称之为用户友好变量的东西，这意味着你在问用户一些他们可以理解的东西:你要访问受保护的端点吗？，比:你只需要客户没有安全感吗？。

第二个问题可能会导致一些人做出疯狂的安全假设，我敢打赌有人甚至会与 HTTP/HTTPS 建立联系，因此为了尽可能清楚，我们将询问用户友好的问题，然后取满足我们配置要求的相反值。

这里有一个我们将如何使用它的例子

```
package main

import (
    "fmt"
    "log"
    "os"

    "github.com/VictorAvelar/devto-api-go/devto"
)

var (
    k string
    p bool
)

func main() {
    k = os.Getenv("DEVTO_API_KEY") // if not set you will get an empty string
    p = true
    c, err := devto.NewConfig(p, k)
    if err != nil {
        log.Fatal(err)
    }
    fmt.Printf("%+v", c)
} 
```

Enter fullscreen mode Exit fullscreen mode

在 [Go 游乐场](https://play.golang.org/p/eF2zb0rxS1V)观看比赛