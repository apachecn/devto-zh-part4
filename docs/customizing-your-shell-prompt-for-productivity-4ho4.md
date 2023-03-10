# 定制 Shell 提示符以提高工作效率

> 原文：<https://dev.to/nikoheikkila/customizing-your-shell-prompt-for-productivity-4ho4>

对于开发人员来说，命令行是日常工作的自然环境。然后对其进行定制，使其尽可能有用，这是有意义的。

试着在你的机器上打开一个非定制的 Bash shell，你可能会看到一个非常无用的*提示*(光标前的文本)类似于`bash-5.0$`的内容。这只表明您正在运行 Bash shell 版本 5.0，这是打开终端时最没用的信息。

那么在提示中应该包含哪些足够有用的信息呢？我已经习惯于根据具体情况要求提供以下信息:

*   当前工作目录
*   当前 Git 分支
*   当前用户名，如果登录到远程机器或做超级用户的事情
*   在全屏模式下工作时的系统时间
*   在笔记本电脑上工作时的电池电量
*   本地项目克隆的状态；例如，未转移、已修改和已删除的文件或未推送和未推送的提交
*   如果使用 Javascript 项目，当前应用程序版本来自 *package.json*
*   当前运行时版本取决于编程语言

您可以找到合适的命令来打印出这些信息，并定制您的 shell 启动文件`~/.bashrc`来包含这些信息，但是为什么要重新发明轮子呢？

## 登上星舰！

[**星舰**](https://starship.rs/) 是用 *Rust* 打造的高度可定制的跨壳提示。它继承了 ZSH 流行的 [*宇宙飞船*](https://denysdovhan.com/spaceship-prompt/) 主题和三维鱼乐队*鱼主题。我已经使用后者一年多了，我对它有时缓慢的输出有点恼火。毕竟，在命令行环境中导航应该是一种极快的体验。多亏了 Rust，速度不再是星际飞船的问题。该项目还没有出测试版，但基础是非常成熟的日常使用。*

 *你可以通过下载二进制文件到你的系统或者使用 Rust 的包管理器`cargo`来安装 Starship。我相信维护者最终会将它添加到其他流行的包管理器中，比如 *APT* 和 *Homebrew* 。安装后，您应该将这个简短的代码片段放到您的 shell 启动文件中。

```
# ~/.bashrc or ~/.zshrc
eval "$(starship init $0)"

# ~/.config/fish/config.fish
eval (starship init fish) 
```

Enter fullscreen mode Exit fullscreen mode

这将在你打开一个新的 shell 会话时加载 Starship 初始化代码。如果您想在运行代码之前查看它，请删除对`eval`的调用，并运行括号内的表达式。

接下来，在`~/.config/starship.toml`创建一个配置文件。如果您不熟悉 TOML 语法，也不用担心，它非常易读。最后，用[记录的配置参数](https://starship.rs/config/)开始破解，你可能会得到这样的结果。

[![Starship Shell](img/d181965e742557ba7fe88f8793034c05.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--7Z1rSULV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/0dxmv9qhf63xm95nc28o.png)

## 结论

通过几分钟的工作，我已经设法从我的环境中挤出了所有重要的信息。现在我可以避免频繁地输入`pwd`、`git status`或`node -v`，并且可以专注于我的实际工作。

当您定期切换机器时，您可能会认为这是浪费时间，并且总是会退回到默认的 Bash 提示符。我以前也是这么想的。然而，除非你是 2000 年代初的系统管理员，除了 SSH'ing 到服务器之外没有更好的工作流程，否则你有可能 95% 的时间都在本地环境中工作。没有必要因为这 5%而放弃生产力定制。这样做的投资回报是巨大的。

我敢打赌*你*那里有一些不错的定制。在评论中分享吧！*