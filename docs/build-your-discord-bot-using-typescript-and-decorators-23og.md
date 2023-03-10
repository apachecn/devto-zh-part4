# 使用 TypeScript 和 decorators 构建您的不和谐机器人！

> 原文：<https://dev.to/owen/build-your-discord-bot-using-typescript-and-decorators-23og>

在用 JS 开发了几个 Discord 机器人之后，我决定改用 TypeScript，但是我对我们监听一个非常“JavaScript”的事件的方式不满意。

所以昨晚，我决定创建一个非常小的框架来开发一个不和谐机器人，使用三个类型脚本装饰器:`@Discord`、`@On()`和`@Once()`。

## 它是如何工作的？

很简单，当你用`@Discord`装饰你的类时，它是内部实例化的，所以当某个事件被触发时，框架会简单地调用相应的类方法，并把触发这个事件提供的所有参数传递给它。

## 如此，如何？

#### 完整的文档可以在[这里找到](https://github.com/OwenCalvin/DiscordTS)但这里有一个简单的例子:

```
import {
  Discord,
  On,
  Client // Use the Client that are provided by @typeit/discord
} from "@typeit/discord";
// You must import the types from @types/discord.js
import {
  Message
} from "discord.js";

// Decorate the class with the @Discord decorator
@Discord
export class AppDiscord {
  private static _client: Client;
  private _prefix: string = "!";
  private _sayHelloMessage: string = "hello !";
  private _commandNotFoundMessage: string = "command not found...";

  static start() {
    this._client = new Client();
    // In the login method, you must specify the glob string to load your classes (for the framework).
    // In this case that's not necessary because the entry point of your application is this file.
    this._client.login(
      "YOUR_TOKEN",
      `${__dirname}/*Discord.ts` // glob string to load the classes
    );
  }

  // When the "message" event is triggered, this method is called with a specific payload (related to the event)
  @On("message")
  async onMessage(message: Message, client: Client) {
    // Your logic...
    if (AppDiscord._client.user.id !== message.author.id) {
      if (message.content[0] === this._prefix) {
        const cmd = message.content.replace(this._prefix, "").toLowerCase();
        switch (cmd) {
          case "hello":
            message.reply(this._sayHelloMessage);
            break;
          default:
            message.reply(this._commandNotFoundMessage);
            break;
        }
      }
    }
  }
}

// Start your app
AppDiscord.start(); 
```

Enter fullscreen mode Exit fullscreen mode

就这样！