# 一种在 Go 应用程序中传递配置的干净方法

> 原文：<https://dev.to/ilyakaznacheev/a-clean-way-to-pass-configs-in-a-go-application-1g64>

> *这是我在 [kaznacheev.me](http://kaznacheev.me) 的博客的转贴。我欢迎新读者，也很乐意讨论我写的关于*的内容

如今有许多组织应用程序配置的并行方法。

经典的`.ini`文件、`.json`、`.toml`、`.yaml`、配置、现代的`.env`，当然还有容器环境。不要忘记 CLI 参数！我错过了什么吗？

老实说，我真的不喜欢界面中的任何隐含性。当然，CLI 也是如此。你的任何接口，无论是公共的还是内部的，API 还是对象接口，类方法还是模块外观，它们都必须公平地合作。你和另一方之间的合同应该是明确的、合法的，并且在页面底部没有任何小字体注释。

这意味着，他们必须准确地接受他们向你提出的要求，并准确地给你他们承诺的东西。当你转身离开的时候，他们不应该从你的口袋里拿走任何东西。如果你同意立即交易，他们不应该在一两个星期后把你的东西送来。他们能把握住内心的状态。不是所有的都应该是不可变的。但是你应该总是能够清晰和公平地互动，这意味着所有的互动都必须是可见和合法的，也就是说，你将能够传递数据。否则，你会丢失合同细节，迟早会上当。

任何接口都必须有一个单一的入口点，这意味着通过显式传递，可以一次性传递接口需要的全部数据。它可以是任何类型的数据——值、结构化或序列化数据、要解析的文件路径或要连接的套接字——主要思想是将数据放在接口调用中。

## 一种清理配置的方式

因此，根据[十二因素应用](https://12factor.net/)将任何有意义的设置移入环境参数的现代趋势在我看来很丑陋。不，十二因素应用程序的想法很好。但是我不得不通过环境变量 setup(即全局变量)隐式传递我的配置，这让我很难过。

可悲的是，在开始的时候，我把我所有的配置放在一个配置文件中，并为每个环境(例如，本地、阶段、测试、生产等)创建了一个单独的配置文件。).通常配置文件是结构良好的，所以我可以根据它们的用途对参数进行分组。但是它并不那么有用，因为我需要在 repo 中为每个环境存储几个配置，并在配置结构改变后更新每个配置。

然后，我将 config 与 CLI 参数结合起来，因为我必须将一些数据直接传递给应用程序，例如机密、本地路径和其他变量数据以及我无法保存在配置文件中的数据。这样更好，因为现在我可以传递一些在不同环境中不同的值作为参数，并对每个系统使用相同的配置文件。它运行得很好，直到...

集装箱来了。设置容器环境的常用方法是使用环境变量。理论上，将配置文件存储在 secrets 中是可能的，但这一点用都没有。

环境变量的主要缺点是它们的隐含性质。你不能只看应用程序`help`的输出，或者检查一个配置文件结构来查看一个变量集和结构。当您的应用程序配置基于环境变量时，您应该有它们的文档，并且您正在承担其更新和同步的负担。否则，用户将不被允许获取配置信息，他对应用程序的体验将非常糟糕。

这类 app 也很难调试。作为全局变量，环境变量隐含地起作用，可以在任何时候影响程序的行为。当你的代码小到可以放入你的内存时，这没什么大不了的，但是当它变大时，你将很难记住你从哪里得到一个或另一个配置值。

但毕竟我已经找到了方法，这看起来可行，满足我的要求。它结合了多种技术，所以让我简单介绍一下。

## 配置文件

首先，我们仍然使用配置文件。我使用它有几个目的:

*   声明配置结构；
*   保留每个变量的默认值；
*   记录变量或节；
*   为应用程序用户提供配置示例；

我更喜欢将我的配置存储在 YAML 文件中。我不想发动一场圣战，但我只想宣布为什么这对我是最好的:

*   层级结构——我可以在一个多变的组织中随心所欲地灵活变通；
*   干净的标记——我不需要任何括号或一堆符号来和解析器交流；
*   评论——我可以提供详细的文档、选项、限制、示例、最佳实践等。；
*   丰富的语义——非常先进的技术，比如锚点、别名、扩展、嵌入等等。我并不经常使用它们，但有时它们非常有用；

假设我们有这样一个简单的配置文件:

```
# Server configurations
server:
  host: "localhost"
  port: 8000

# Database credentials
database:
  user: "admin"
  pass: "super-pedro-1980" 
```

Enter fullscreen mode Exit fullscreen mode

要在 Go 中使用来自`.yml`文件的数据，您需要像对 JSON 那样将它解组到结构中。

映射看起来类似于 JSON:

```
type Config struct {
    Server struct {
        Port string `yaml:"port"`
        Host string `yaml:"host"`
    } `yaml:"server"`
    Database struct {
        Username string `yaml:"user"`
        Password string `yaml:"pass"`
    } `yaml:"database"`
} 
```

Enter fullscreen mode Exit fullscreen mode

我使用 Cannonical 的`gopkg.in/yaml.v2`库来解析 YAML 文件。

您可以使用`yml.Unmarshal`来解析一个字节片，但是在大多数情况下，您将使用作为`io.Reader`实现提供的一些数据，所以 I/m 使用读取字节流的解码器，而不是存储在内存中的全部数据:

```
f, err := os.Open("config.yml")
if err != nil {
    processError(err)
}
defer f.Close()

var cfg Config
decoder := yaml.NewDecoder(f)
err = decoder.Decode(&cfg)
if err != nil {
    processError(err)
} 
```

Enter fullscreen mode Exit fullscreen mode

仅此而已。就像任何 JSON 文件一样。现在，您可以继续编写您自己的文档齐全、结构良好的配置文件，它也将作为您的存储库中的一个示例。

## 环境变量

现在我们来谈谈环境变量。通常，他们分散在应用程序中。但是还有另一种方法。在 Go 中，你可以将环境变量赋给结构字段，就像你对 JSON、YAML 和其他人做的那样。它会是这样的:

```
type Config struct {
    Server struct {
        Port string `envconfig:"SERVER_PORT"`
        Host string `envconfig:"SERVER_HOST"`
    }
    Database struct {
        Username string `envconfig:"DB_USERNAME"`
        Password string `envconfig:"DB_PASSWORD"`
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

魔法在`github.com/kelseyhightower/envconfig`图书馆里。

在几行代码中，它检索环境变量并将它们分配给已经定义的结构字段:

```
var cfg Config
err := envconfig.Process("", &cfg)
if err != nil {
    processError(err)
} 
```

Enter fullscreen mode Exit fullscreen mode

仅此而已。现在，所有的环境变量都在同一个地方。不需要浏览所有的 repo 来找到你在哪里使用这个变量，你可以简单地跟踪配置结构，它只有一个入口点。

此外，现在您已经在同一个地方声明了所有的环境变量。你可以打开配置结构，看到应用程序需要的 env 的完整列表。lib 提供了一组具有广泛输出可能性的`Usage`函数，这将允许您添加 env 列表。变量来帮助输出或任何你想要的。您的应用程序的用户会很感激这一点。

现在你可以用你最喜欢的方式来提供环境变量。env 文件、容器设置、makefile 或只是一个 shell 脚本。这取决于你。

## 都在一起

所以现在让我们把它们混合起来！

同样的结构会是这样:

```
type Config struct {
    Server struct {
        Port string `yaml:"port", envconfig:"SERVER_PORT"`
        Host string `yaml:"host", envconfig:"SERVER_HOST"`
    } `yaml:"server"`
    Database struct {
        Username string `yaml:"user", envconfig:"DB_USERNAME"`
        Password string `yaml:"pass", envconfig:"DB_PASSWORD"`
    } `yaml:"database"`
} 
```

Enter fullscreen mode Exit fullscreen mode

所以首先我从 YAML 文件中加载数据。它也为我提供了一组默认值。

然后我加载 env 并覆盖已填充的字段。也就是说，您不需要关心丢失的值，它们将从配置文件中填充。

```
func main() {
    var cfg Config
    readFile(&cfg)
    readEnv(&cfg)
    fmt.Printf("%+v", cfg)
}

func processError(err error) {
    fmt.Println(err)
    os.Exit(2)
}

func readFile(cfg *Config) {
    f, err := os.Open("config.yml")
    if err != nil {
        processError(err)
    }
    defer f.Close()

    decoder := yaml.NewDecoder(f)
    err = decoder.Decode(cfg)
    if err != nil {
        processError(err)
    }
} 

func readEnv(cfg *Config) { 
    err := envconfig.Process("", cfg) 
    if err != nil { 
        processError(err)
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

因此，这种方法有许多优点:

*   单一入口点——很容易找到每个配置变量的来源；
*   通过标签的简单声明；
*   结构化配置-您可以根据配置节的用途对它们进行分组；
*   在配置文件中声明默认值，在每个环境中覆盖您需要的内容；
*   应用程序中使用的环境变量的显式列表；

我不确定这种方法是否涵盖了任何可能的使用场景，但是它非常有用，而且非常重要。

UPD: 我已经将所有讨论过的技术添加到一个名为 CleanEnv 的小而简单的配置包中。

## ![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) [伊利亚卡兹纳切夫](https://github.com/ilyakaznacheev) / [克列安夫](https://github.com/ilyakaznacheev/cleanenv)

### ✨Clean 和 Golang 的极简环境配置读本

<article class="markdown-body entry-content container-lg" itemprop="text">

[![Clean Env](img/9c755d36b1de6714000e5cddd29465af.png)](https://github.com/ilyakaznacheev/cleanenvlogo.svg)

# 清洁环境

极简配置阅读器

[![Mentioned in Awesome Go](img/1ccd181a01eb46221abe3b2cdab927b2.png)](https://github.com/avelino/awesome-go)[![GoDoc](img/2893ed6677bba11f97906a5fa4434aa1.png)](https://godoc.org/github.com/ilyakaznacheev/cleanenv)[![Go Report Card](img/08523ca7444e10e20a0b915dc4a29612.png)](https://goreportcard.com/report/github.com/ilyakaznacheev/cleanenv)[![Coverage Status](img/fec22c81c78f71e5e717f050897cdad1.png)](https://codecov.io/gh/ilyakaznacheev/cleanenv)[![Build Status](img/ff4a9c1cd9458b30dfc4eaf516662ad8.png)](https://travis-ci.org/ilyakaznacheev/cleanenv)[![Release](img/d5b65afd81cf1a8be536e578cc589ea7.png)](https://github.com/ilyakaznacheev/cleanenv/releases/)[![License](img/3194aa82bd5eb04c9d38c6b8daad5d19.png)](https://github.com/ilyakaznacheev/cleanenv/blob/master/LICENSE)

## 概观

这是一个简单的配置读取工具。它只是执行以下操作:

*   从文件中读取并解析配置结构
*   从环境变量中读取和覆盖配置结构
*   编写详细的变量列表以帮助输出

## 内容

*   [安装](https://github.com/ilyakaznacheev/cleanenv#installation)
*   [用途](https://github.com/ilyakaznacheev/cleanenv#usage)
    *   [读取配置](https://github.com/ilyakaznacheev/cleanenv#read-configuration)
    *   [仅读取环境变量](https://github.com/ilyakaznacheev/cleanenv#read-environment-variables-only)
    *   [更新环境变量](https://github.com/ilyakaznacheev/cleanenv#update-environment-variables)
    *   [描述](https://github.com/ilyakaznacheev/cleanenv#description)
*   [模型格式](https://github.com/ilyakaznacheev/cleanenv#model-format)
*   [支持的类型](https://github.com/ilyakaznacheev/cleanenv#supported-types)
*   [自定义功能](https://github.com/ilyakaznacheev/cleanenv#custom-functions)
    *   [自定义值设置器](https://github.com/ilyakaznacheev/cleanenv#custom-value-setter)
    *   [自定义值更新](https://github.com/ilyakaznacheev/cleanenv#custom-value-update)
*   [支持的文件格式](https://github.com/ilyakaznacheev/cleanenv#supported-file-formats)
*   [整合](https://github.com/ilyakaznacheev/cleanenv#integration)
    *   [【标志】t1㎡](https://github.com/ilyakaznacheev/cleanenv#flag)
*   [例题](https://github.com/ilyakaznacheev/cleanenv#examples)
*   [贡献](https://github.com/ilyakaznacheev/cleanenv#contribution)
*   [感谢](https://github.com/ilyakaznacheev/cleanenv#thanks)

## 装置

要安装软件包，请运行

```
go get -u github.com/ilyakaznacheev/cleanenv
```

Enter fullscreen mode Exit fullscreen mode

## 使用

这个包的目标是简单易用和清晰明了。

主要思想是使用结构化的配置变量，而不是像某些库那样使用任何类型的动态配置字段集，以避免不必要的类型转换，并将配置作为简单的结构在程序中移动，而不是作为具有复杂行为的对象。

你只需要做几个动作…

</article>

[View on GitHub](https://github.com/ilyakaznacheev/cleanenv)