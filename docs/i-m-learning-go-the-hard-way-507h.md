# 我学会了走艰难的路

> 原文：<https://dev.to/krystofee/i-m-learning-go-the-hard-way-507h>

这是我的第一篇博文。

这不是围棋教程，而是我为什么选择开始学习围棋以及如何学习的故事。

几天前，我决定为 RegioJet(捷克最大的私人运营商之一， [link](https://www.regiojet.com) )实现 rest api 客户端。我的第一个计划是用 Python 写它，我想我已经很擅长了，但是我已经做了很多 rest api 集成和...在同样的环境下再做一次，没有什么新奇的。

## 本项目的目标

1.  学习如何用 Golang 制作 web 应用程序。
2.  有一个网络应用程序，可以统一许多运输公司的座位和机票预订。

想象一下，你想在下午乘坐繁忙的火车，而早上却满员了。我知道许多人一个月前就在买票。我不是那种人。我喜欢提前一天决定我什么时候通勤，或者更好的是，在那一天。如果你经历过这种情况，你可能会注意到，在你决定的时间，火车票已经卖完了。如果是这样的话，你可能会注意到一些人可能会在几个小时前就把票卖了。我宁愿说，有些人在旅行当天出售机票是一种惯例，而不是例外。如果你经常查看航空公司的网站，你就能幸运地得到他们的座位。这是我做的，ahis 也做了许多其他人，如果你不够快，你将不会是幸运的家伙。
我的愿景是，我将选择我想去的时间和地点，它将为我处理机票预订。是的，我不能 100%确定能买到座位，但可以通过其他一些不需要预订座位的承运商来处理(这意味着即使火车票完全售完，你也可以乘坐)。

## 为什么在走？

我认为我越来越擅长使用 Python 和 Django 进行 web 开发。如今，我已经有了 2 年的 Django 和 DRF 的日常经验(值得夸耀的是，我最近为 DRF 做了一点贡献:)。我完全可以在 Django 做这个项目，不需要学习任何新东西，只需要有最终的产品。把事情做好很难，结果只会是一个应用程序，没有学到多少新东西。
所以更令人兴奋的方法是用一种我写这篇文章时还不知道的语言来写(除了一些基础知识)。发展会更有趣，即使我没有完成这个项目，我也会学到很多东西。

## 让我们投入进去吧

我将从构建后端开始(正如这篇关于我的围棋的帖子所预期的)。我的计划是:

1.  主要使用标准库编写 api 客户端包
2.  选择框架并创建 web REST 后端(Beego 或 Gin)
3.  在 React 中创建简单的前端

最有趣的事情将是我的第一个观点。第二点是创建一些与我的客户交互的端点。第三点就是在 React 中写一个简单的前端，我已经知道了。

## 我在 Go 中的第一个 HTTP 请求

我找到了 Abu Ashraf Masnun 的这篇文章，它清楚地解释了如何创建简单的 GET、POST 和其他类型的 HTTP 请求。
有了这些知识，我可以第一次尝试与 RegioJet API 交互。有他们的公共 api [文档](https://app.swaggerhub.com/apis/regiojet/ybus/1.0.0)。

main.go 文件目前是我们的应用程序入口点。运行`go run main.go locations.go`时，调用`func main()`。我们称之为从`locations.go`输出的`GetCountries`，这将在后面解释。然后我遍历返回国家中的每个国家并打印它们的代码。

**main . go**T2】

```
package main

import (
    "fmt"
    "log"
)

func main() {
    log.Println("Running RegioJet API client")

    var countries = GetCountries()

    for _, country := range countries {
        fmt.Println(country.Code)
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

在`location.go`的后面是根据 RegioJet 模型定义的位置结构(`Country`、`City`、`Station`)。在 golang 中解析 json 最酷的一点是，你可以定义结构，然后使用`json.Unmarshall(data []byte, v interface{}) error`来解析 json。我发现这非常有用，因为结果已经输入了，你马上就知道你在做什么。(我不习惯这样，因为我主要使用 Python 和 Javascript，这两种语言都是松散类型的语言)

**T2**

```
package main

import (
    "encoding/json"
    "io/ioutil"
    "log"
    "net/http"
)

var endpointUrl = "https://brn-ybus-pubapi.sa.cz/restapi/consts/locations"

type Station struct {
    ID                 string `json:"id"`
    Name               string `json:"name"`
    Fullname           string `json:"fullname"`
    Aliases            string `json:"aliases"`
    Address            string `json:"address"`
    StationTypes       string `json:"stationTypes"`
    IataCode           string `json:"iataCode"`
    StationURL         string `json:"stationUrl"`
    WheelChairPlatform string `json:"wheelChairPlatform"`
    Significance       string `json:"significance"`
}

type City struct {
    ID            string    `json:"id"`
    Name          string    `json:"name"`
    Aliases       []string  `json:"aliases"`
    StationsTypes []string  `json:"stationTypes"`
    Stations      []Station `json:"stations"`
}

type Country struct {
    Country string `json:"country"`
    Code    string `json:"code"`
    Cities  []City `json:"cities"`
}

type Countries []Country

func GetCountries() Countries {
    log.Println("... getting countries")

    resp, err := http.Get(endpointUrl)
    if err != nil {
        log.Fatalln(err)
    }

    defer resp.Body.Close()

    byteBody, err := ioutil.ReadAll(resp.Body)
    if err != nil {
        log.Fatalln(err)
    }

    var countries Countries
    err := json.Unmarshal(byteBody, &countries)
    if err != nil {
        log.Fatalln(err)
    }

    return countries
} 
```

Enter fullscreen mode Exit fullscreen mode

您可能会注意到，我为 json 显式地命名了所有的结构成员，即使我不应该这样做，因为它是由结构成员名称的约定所决定的，所以例如没有显式`json:"code"`的`Country.Code`将匹配`code`甚至`cOdE`。但是我认为这样命名是很好的实践，可以避免大的重构，因为所有的结构成员都不依赖于 JSON 响应中接收到的键。

最后但同样重要的是，让我们运行我上面展示的代码...

**输出:**

```
$ go run locations.go main.go
2019/07/08 22:19:50 Running RegioJet API client
2019/07/08 22:19:50 ... getting countries
DE
BE
CH
LU
HR
IT
FR
UA
HU
AT
UK
CZ
SK
PL
RO
NL 
```

Enter fullscreen mode Exit fullscreen mode

结果是预期的每行国家代码。

* * *

许多新的东西要学，许多学到的东西。我和 golang 的第一次经历非常愉快。在没有任何相关知识的情况下，我认为它更像 C 语言。这真的很顺利。

我将跳过其他端点的实现，直接进入如何查询获取的数据，我将尝试考虑核心功能，如获得免费座位和尝试预订座位。