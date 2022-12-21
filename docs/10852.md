# 自动化你的空闲互动

> 原文：<https://dev.to/rafaelcpalmeida/automate-your-slack-interactions-37ed>

我是自动化重复任务的忠实粉丝，我总是尽我所能尝试自动化一切。还记得那个将自己工作的一部分自动化的家伙吗？我也试着做一些类似的事情！想象一下下面的场景:每次你合上 MacBook Pro 的盖子，它都会发送一条信息，告诉你的团队你已经离开一天了。在这篇文章中，我将展示如何做到这一点！

要在**空闲**上发送消息，你需要一个**空闲访问令牌**。要获得一个，你需要创建一个新的 **Slack 应用**，安装在你的工作区，进入 OAuth &权限，复制你生成的 **OAuth 访问令牌**。

为了能够在你的 Mac 进入睡眠或醒来时执行脚本，我们将使用[伯恩哈德·巴尔](https://www.bernhard-baehr.de/)的[睡眠观察者 2.2](https://www.bernhard-baehr.de/sleepwatcher_2.2.tgz) 。下载文件后，解压并在其目录下打开一个新的终端窗口，然后键入以下内容:

```
$ sudo mkdir -p /usr/local/sbin /usr/local/share/man/man8

$ sudo cp sleepwatcher /usr/local/sbin

$ sudo cp sleepwatcher.8 /usr/local/share/man/man8 
```

Enter fullscreen mode Exit fullscreen mode

在本文中，我只打算写一下**睡眠**事件触发器。如果你想了解更多的活动或阅读文档，只需输入:

```
$ man sleepwatcher 
```

Enter fullscreen mode Exit fullscreen mode

现在 **SleepWatcher** 已经在正确的位置了，你可以尝试用下面的命令执行这个脚本:

```
$ /usr/local/sbin/sleepwatcher --verbose --sleep /path/to/your/sleepscript 
```

Enter fullscreen mode Exit fullscreen mode

当你完成测试并且一切正常后，是时候设置 **SleepWatcher** 作为守护进程运行。为此，您可以键入以下命令:

```
$ sudo cp config/de.bernhard-baehr.sleepwatcher-20compatibility.plist /Library/LaunchDaemons/de.bernhard-baehr.sleepwatcher.plist
$ sudo cp config/rc.* /usr/local/bin 
```

Enter fullscreen mode Exit fullscreen mode

之后，只需执行启动您的守护进程。

```
$ sudo launchctl load /Library/LaunchDaemons/de.bernhard-baehr.sleepwatcher.plist 
```

Enter fullscreen mode Exit fullscreen mode

现在一切都准备好了，您需要编写每次计算机进入睡眠状态时都要执行的脚本。我们称之为。睡眠并使其可执行:

```
$ touch ~/.sleep
$ chmod +x ~/.sleep 
```

Enter fullscreen mode Exit fullscreen mode

在这个文件中，我们将放置用于向 **Slack** 发送消息的代码。我的看起来像:

```
#!/bin/bash

STARTING_TIME="17:00"

ENDING_TIME="19:00"

CURRENT_TIME=$(date +%H:%M)

CURRENT_DATE=$(date "+%d/%m/%Y")

READ_DATE=$(cat ~/.say_message.lock)

if [[ "$CURRENT_TIME" > "$STARTING_TIME" ]] && [[ "$CURRENT_TIME" < "$ENDING_TIME" ]] && [[ "$READ_DATE" < "$CURRENT_DATE" ]]; then TOKEN=xoxp-2…

    CHANNEL=#channel_name

    declare -a EXPRESSIONS=("Write" "Your" "Expressions" "Here")

    SELECTED_EXPRESSION=${EXPRESSIONS[$RANDOM % ${#EXPRESSIONS[@]} ]}

    curl -X POST \

        --connect-timeout 10 \

        --max-time 10 \

        --retry 10 \

        --retry-delay 0 \

        --retry-max-time 60 \

        --'content-type: multipart/form-data' \

        -F token=$TOKEN \

        -F "channel=$CHANNEL" \

        -F text="$SELECTED_EXPRESSION" \

        -F as_user=true \

        https://slack.com/api/chat.postMessage

        echo $CURRENT_DATE > ~/.say_message.lock

fi 
```

Enter fullscreen mode Exit fullscreen mode

让我一行一行地解释这个脚本。

## 脚本解释

我使用以下变量来指定消息只在 17:00 和 19:00
之间发送

```
STARTING_TIME="17:00"

ENDING_TIME="19:00" 
```

Enter fullscreen mode Exit fullscreen mode

这些用于确保消息每天只发送一次，以防止每次锁定计算机时都发送消息。

```
CURRENT_TIME=$(date +%H:%M)

CURRENT_DATE=$(date "+%d/%m/%Y")

READ_DATE=$(cat ~/.say_message.lock) 
```

Enter fullscreen mode Exit fullscreen mode

下面的 if 条件允许我确保满足我上面指定的条件。它们是:消息仅在 17:00 和 19:00 之间发送，并且每天仅执行一次。

```
if [[ "$CURRENT_TIME" > "$STARTING_TIME" ]] && [[ "$CURRENT_TIME" < "$ENDING_TIME" ]] && [[ "$READ_DATE" < "$CURRENT_DATE" ]]; then

fi 
```

Enter fullscreen mode Exit fullscreen mode

要将消息发送到所需的通道，您必须指定从 **Slack** 获得的令牌以及发布消息的通道。稍后我还有一组要随机化的表达式。这意味着发出不同的信息。为了实现这一点，我有以下几行:

```
TOKEN=xoxp-2[…]

CHANNEL=#channel_name

declare -a EXPRESSIONS=("Write" "Your" "Expressions" "Here")

SELECTED_EXPRESSION=${EXPRESSIONS[$RANDOM % ${#EXPRESSIONS[@]} ]} 
```

Enter fullscreen mode Exit fullscreen mode

当一切都设置好后，我们需要将我们的消息发送给 **Slack API** 。这是使用**卷曲**完成的。这些是我的配置:

```
curl -X POST \

--connect-timeout 10 \

--max-time 10 \

--retry 10 \

--retry-delay 0 \

--retry-max-time 60 \

--'content-type: multipart/form-data' \

-F token=$TOKEN \

-F "channel=$CHANNEL" \

-F text="$SELECTED_EXPRESSION" \

-F as_user=true \

https://slack.com/api/chat.postMessage 
```

Enter fullscreen mode Exit fullscreen mode

我告诉 **cURL** 要:

*   每次尝试持续 10 秒，最长时间为 10 秒
*   使用- retry 10 重试 10 次
*   使用- retry-delay 0 禁用重试之间的卷曲睡眠
*   使用- retry-max-time 60 最多等待 60 秒

上面的代码将用指定的有效负载向`https://slack.com/api/chat.postMessage`发送一个 POST 请求。你可以查看更多关于**postMessage**endpoint reading Slack[documentation](https://api.slack.com/methods/chat.postMessage)。

在请求执行之后，我用新的日期更新“锁”文件，代码行是:

```
echo $CURRENT_DATE > ~/.say_message.lock 
```

Enter fullscreen mode Exit fullscreen mode

这是为了确保每天只发送一条消息。

这就是我如何自动化我与 Slack 的交互。请用下面的评论让我知道你对此的想法。