# 使用 MTProto (Pyrogram)与电报机器人进行简单的自动化交互

> 原文：<https://dev.to/blueset/simple-automated-interactions-with-telegram-bots-using-mtproto-pyrogram-4ge5>

[Telegram](https://telegram.org) 是一个流行的 <abbr title="instant messaging">IM</abbr> 平台，以其开放性著称。许多应用程序都是通过它们的公共 Bot API 和用户 API 被发现的。作为 HTTP 接口公开的 Bot API 在 Telegram 上更受欢迎，但是为了与 Bot 交互，我们仍然需要公开它的用户 API，它使用名为 MTProto 的原始协议。下面是我截取的简单代码，它使用[pyro gram](https://github.com/pyrogram/pyrogram)——一个 MTProto 的 Python 包装，向一个机器人发送一条消息，并将它的第一条回复标记为已读。

## 你需要什么

*   Python 3.6 或更高版本
*   电报帐户

## 安装烙画

```
pip3 install 'pyrogram[fast]' 
```

Enter fullscreen mode Exit fullscreen mode

`[fast]`这里的意思是使用基于 C 的密码模块以获得更好的性能。

## 电报 API 键

从[https://my.telegram.org/apps](https://my.telegram.org/apps)处获取自己的电报 API 密钥，稍后会用到。

## 脚本

```
from pyrogram import Client, Filters, MessageHandler, Message
from threading import Event

# Put your Telegram API key here api_id = 12345
api_hash = "12345678901234567890abcdefabcdef"

# User to send message to user = "botfather"
# Message content command = "/help"

feedback_evt = Event()

def mark_as_read(client: Client, message: Message):
    client.read_history(message.chat.id, message.message_id)
    feedback_evt.set()

with Client("login", api_id, api_hash) as app:
    app.send_message(user, command)
    app.add_handler(MessageHandler(mark_as_read, Filters.chat(user)))
    feedback_evt.wait() 
```

Enter fullscreen mode Exit fullscreen mode

相应地更改突出显示的行。

## 第一次使用

用 Python 运行这个脚本，应该会提示您用您的电话号码和登录代码登录。这仅在第一次运行时需要。

> 注意
> 
> 您的登录会话数据将存储在`login.session`文件中。让这个文件像你的密码一样安全。

现在这个脚本可以运行了。您可以用任何想要的东西来运行它，bash 脚本、cronjob 或任何可以调用命令的东西。

使用 MTProto (Pyrogram) 与电报机器人进行简单自动交互的帖子[首先出现在](https://blog.1a23.com/2019/09/22/simple-automated-interactions-with-telegram-bots-using-mtproto-pyrogram/) [1A23 博客](https://blog.1a23.com)上。