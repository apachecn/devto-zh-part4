# 如何将数据从 Flux 输出到 MQTT

> 原文：<https://dev.to/davidgs/how-to-output-data-from-flux-to-mqtt-natively-2p8c>

## 使用 Flux 将数据从 InfluxDB 写入 MQTT

我在 Alpha 版本的早期就开始使用 InfluxDB v2.0 的开源(OSS)版本。即使在早期版本中，我也非常迷恋事物的发展方式。但正如你所知，我做了很多物联网构建，并使用 InfluxDB 来完成所有的构建，所以有一些事情我需要它来做，但它还没有。

我在所有物联网演示中做的一件事是向 MQTT 代理发出警报。我还有其他物联网设备，它们从该代理读取信息，并根据收到的信息采取行动。但是 InfluxDB 2.0 Alpha 没有真正的输出功能。

**注意:**influx db 2.0 很快会推出一个警报框架，但 a)当时还没有，b)我现在需要它。

怎么办？嗯，Flux 是一种可扩展语言，所以我决定扩展该语言来编写 MQTT。首先，需要注意的是，Flux 有两种语言结构用于读写数据:`from()`和`to()`。如果你曾经编写过 Flux，你会发现`from()`语法就是你从 InfluxDB 获取数据的方式。`to()`业务有点难。该语言内置了使用`to()`语法写回 InfluxDB 的能力。我还发现了 http 的一个`to()`扩展，它允许您将 Flux 查询的结果写到 http 端点。至少我现在有了一个起点！

## 添加 MQTT 到 Flux

我开始研究 Flux 代码，看看 http `to()`方法是如何实现的，并很快发现对 MQTT 使用相同的框架几乎是微不足道的，所以我复制了 http `to()`输出的所有代码，并开始将它转移到 MQTT。与所有这些事情一样，它并没有我最初想象的那么“琐碎”,但是经过几周的断断续续的工作，我有了一个从 Flux 到 MQTT 的工作输出！

首先，我必须定义 MQTT 输出需要哪些选项，并且我选定了一种默认的最小选项集:

```
golang
type ToMQTTOpSpec struct {
    Broker string `json:"broker"`
    Name string `json:"name"`
    Topic string `json:"topic"`
    Message string `json:"message"`
    ClientID string `json:"clientid"`
    Username string `json:"username"`
    Password string `json:"password"`
    QoS int `json:"qos"`
    NameColumn string `json:"nameColumn"` // either name or name_column must be set, if none is set try to use the "_measurement" column.
    Timeout time.Duration `json:"timeout"` // default to something reasonable if zero
    NoKeepAlive bool `json:"noKeepAlive"`
    TimeColumn string `json:"timeColumn"`
    TagColumns []string `json:"tagColumns"`
    ValueColumns []string `json:"valueColumns"`
} 
```

Enter fullscreen mode Exit fullscreen mode

当然，并不是所有这些都是**必需的**，但是我会一一介绍。

当然，首先您需要定义一个代理。这是您想要使用的 MQTT 代理的 URL。在您的 URL 中，您的经纪人应该被标识为`tcp`、`ws`或`tls`，因此`tcp://mqtt.mybroker.com:1883`将是它正在寻找的。其余的大部分，总的来说，在一定程度上是可选的。**如果**提供了一个`Username`，那么**也必须**提供一个密码。两者缺一不可！此外，如果您不提供一个`Topic`，那么将会通过把您的查询返回的所有标签串在一起为您创建一个。我建议给出一个主题，因为在很多情况下，`/tag1/tag_2/tag_3/...`的主题并不理想。

## 这个新东西怎么用？

很高兴你问了！首先，它实际上还不是 Flux 的一部分。我已经提交了一份 PR，它已经被接受，但是(在撰写本文时)还没有被合并。如果你想构建你自己的 Flux 版本，以便得到这个 *now* ，那么你需要拉下分支，从源代码开始构建。查看 [MQTT PR](https://github.com/influxdata/flux/pull/1653) 并从那里开始。

一旦这样做了，开始编写 MQTT 代理的 Flux 代码实际上就很简单了！您将希望在 InfluxDB 2.0 UI 中创建一个任务，然后可以粘贴以下代码:

```
import "mqtt"
from(bucket: "telegraf")
    |> range(start: -task.every)
    |> filter(fn: (r) =>
        (r._measurement == "cpu"))
    |> filter(fn: (r) =>
        (r._field == "usage_system"))
    |> filter(fn: (r) =>
        (r.cpu == "cpu-total"))
    |> last()
    |> mqtt.to(
        broker: "tcp://davidgs.com:8883",
        topic: "cpu",
        clientid: "cpu-flux",
        valueColumns: ["_value"],
        tagColumns: ["cpu", "host"],
    ) 
```

Enter fullscreen mode Exit fullscreen mode

这将把最后一个 CPU `usage_system`值写入 MQTT 代理。使用 UI，您可以决定希望数据写入的频率。

## 几点注意事项

重要的是要认识到 [Flux 将所有来自查询的数据作为表](https://www.influxdata.com/blog/use-flux-to-group-shape-and-analyze-your-time-series-data/)返回。上述任务使用`last()`函数的原因是将返回值限制在一个只有一行的表中。MQTT `to()`函数将把整个表写入代理，作为线路协议。如果您的查询返回一个非常大的表，那么您的 MQTT 代理应该准备好获取一个非常大的表作为消息负载。

此外，如果您的查询返回多个表，MQTT `to()`函数将为每个表写一条消息**，每条消息包含一个完整的表。如果这不是您想要的行为，您应该考虑如何设计您的查询，以便它返回一个单独的(最好是小的)表作为结果。**

您可能还注意到了上面的一个可选字段`Message`。如果你想发送一个预定义的消息而不是一个结果表，你可以在对`to()`的调用中定义`message`参数，这个消息就会被发送。

到目前为止，我已经用了大约 2 个月了，效果非常好！我能够根据其他物联网设备的读数来控制一些物联网设备，而且效果很好！

更新:这个的 PR 现在已经合并到主分支中，所以它应该很快就会出现在 Flux 的版本中！

帖子[如何将数据从 Flux 原生输出到 MQTT】最早出现在](https://davidgs.com/2019/how-to-output-data-from-flux-to-mqtt-natively/) [David G. Simmons](https://davidgs.com) 上。