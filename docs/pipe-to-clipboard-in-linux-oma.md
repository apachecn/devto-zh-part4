# Linux 中到剪贴板的管道

> 原文：<https://dev.to/connorbode/pipe-to-clipboard-in-linux-oma>

好吧，这是一个非常快速的提示，但是如果你能记住这个提示，你会在终点站很开心的。

假设您想要将命令的输出复制到剪贴板。你是做什么的？使用`xclip`。

例如，我只需要将我的公共 SSH 密钥添加到我的数字海洋帐户中。

```
cat ~/.ssh/id_rsa.pub | xclip -sel clip 
```

**就这样，现在你可以`ctrl + v`你的 SSH 密钥了。赢了！**

* * *

关注我的 dev.to 或 Twitter 上的[以获得更多快速提示！](https://twitter.com/connorbode)