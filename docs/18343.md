# SignalR 核心 python 客户端(V):重新连接客户端

> 原文：<https://dev.to/mandrewcito/signalr-core-python-client-v-reconnecting-client-4ade>

# [T1】简介](#intro)

这些天我一直在做一个新的功能。客户端重新连接。要了解客户端是否已断开连接，我们必须设置一个最长时间不与套接字交互，该时间将存储在一个变量中，我们将在以下情况下更新该变量:

*   最后发送的消息
*   最后收到的消息

所以，现在我们有了一个变量，它包含了上次通过套接字发送/接收信息的时间。如果该时间已过，我们必须使用插座并检查连接。此操作将重新验证我们的超时或关闭我们的套接字。

如果我们的套接字关闭，我们必须停止应用程序或尝试重新连接。但是，我们不能随机重新连接，我们需要建立一个重新连接的策略，以避免服务器饱和。在接下来的几行中，我将描述我考虑过的几个选项的实现。

## 套接字中的最后一条消息

我实现了一个连接状态检查器类，这个类的目的是检查和更新 last_message 变量。如果变量达到阈值，我们发送一个 ping，如果 ping 失败，套接字将关闭。

```
class ConnectionStateChecker(threading.Thread):
    def __init__(
            self,
            ping_function,
            keep_alive_interval,
            sleep = 1):
        self.sleep = sleep
        self.keep_alive_interval = keep_alive_interval
        self.last_message = time.time()
        self.ping_function = ping_function
        self.runing = True

    def run(self):
        while self.runing:
            time.sleep(self.sleep)
            time_without_messages = time.time() - self.last_message
            if self.keep_alive_interval < time_without_messages:
                self.ping_function() 
```

Enter fullscreen mode Exit fullscreen mode

### 策略

现在我只实现了 2 个策略，将被激活，用 with_automatic_reconnect 方法上的参数“type”。

```
class ReconnectionType(Enum):
    raw = 0  # Reconnection with max reconnections and constant sleep time
    interval = 1  # variable sleep time 
```

Enter fullscreen mode Exit fullscreen mode

#### 间隔

这种重新连接与一系列的“延迟”一起工作。有了它，指数重新连接将很容易实现。

```
 class IntervalReconnectionHandler(ReconnectionHandler):
    def __init__(self, intervals):
        self._intervals = intervals

    def next(self):
        self.reconnecting = True
        index = self.attempt_number
        self.attempt_number += 1
        return self._intervals[index] 
```

Enter fullscreen mode Exit fullscreen mode

您可以很容易地生成一个指数延迟列表。

```
delays = [pow(2, x) for x in range(1,10)]
# [2, 4, 8, 16, 32, 64, 128, 256, 512] 
```

Enter fullscreen mode Exit fullscreen mode

#### 【原始】重新连接

在“原始重新连接”时，我决定建立一个限制，但如果没有建立限制，连接将是无限的。

```
class RawReconnectionHandler(ReconnectionHandler):
    def __init__(self, sleep_time, max_attempts):
        super(RawReconnectionHandler, self).__init__()
        self.sleep_time = sleep_time
        self.max_reconnection_attempts = max_attempts

    def next(self):
        self.reconnecting = True
        if self.max_reconnection_attempts is not None:
            if self.attempt_number <= self.max_reconnection_attempts:
                self.attempt_number += 1
                return self.sleep_time
            else:
                raise ValueError("Max attemps reached {0}".format(self.max_reconnection_attempts))
        else:  # Infinite reconnect
            return self.sleep_time 
```

Enter fullscreen mode Exit fullscreen mode

## 客户端语法

语法将类似于 [net core signalr 3.0](https://devblogs.microsoft.com/aspnet/asp-net-core-updates-in-net-core-3-0-preview-4/)

```
hub_connection = HubConnectionBuilder()\
    .with_url(server_url)\
    .with_automatic_reconnect({
        "type": "raw",
        "keep_alive_interval": 10,  # Ping function
        "reconnect_interval": 5, # 5s between attempts
        "max_attempts": 5
    }).build()

hub_connection = HubConnectionBuilder()\
    .with_url(server_url, options={
        "access_token_factory": lambda: signalr_core_example_login(login_url, username, password)
    }).with_automatic_reconnect({
        "type": "interval",
        "keep_alive_interval": 10, # Ping function
        "intervals": [1, 3, 5, 6, 7, 87, 3]
    })\
    .build() 
```

Enter fullscreen mode Exit fullscreen mode

## 链接

[Github](https://github.com/mandrewcito/signalrcore)
[Pypi](https://pypi.org/project/signalrcore/)

感谢您的阅读，并在:D 下面写下您的任何想法