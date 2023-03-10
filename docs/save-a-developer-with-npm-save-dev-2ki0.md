# 使用 npm - save-dev 保存开发人员

> 原文：<https://dev.to/rowan_m/save-a-developer-with-npm-save-dev-2ki0>

我本打算巧妙地将这篇文章命名为“`npm -g`被认为有害”，但不出所料的是[马特·伯伦斯](https://twitter.com/zigg)在 2016 年凭借[这篇优秀的文章](https://spin.atomicobject.com/2016/02/22/npm-install-dangerous/)回到了那里。作为一个单独的教训，阅读它让我觉得我没有必要写这个，因为马特已经解释得很好了-但你知道吗？

这种模式仍然无处不在，所以稍微重复一下♻️和放大📣不会疼的。

# ⚠️有什么问题吗？

我记得最近对一位同事说过，我喜欢 Linux 的原因之一是集中管理所有依赖项的惯例。一切都可以在后台定期更新，当我想启动一个应用程序时，没有惊喜的 6GB 下载(耶-我在看着你，PlayStation)，不需要记住我在哪里下载的软件...就一个命令。

当然，我在对自己撒谎——谈论我希望拥有的生活，而不是我实际经历的单调乏味的现实。运行完`apt-get`之后，我需要运行`npm -g update`。我还应该检查一下`nvm --install --lts`以确保它是最新的。哦，我也应该做一个`flatpak update`,因为那是现在酷孩子放应用程序的地方。当然，还有一个项目决定使用一些 Python 依赖项，所以我最好查找一下那个非常优雅的通过 50 个命令来管理`pip list`直到点击`xargs`的命令行。然后，当然，只是通过掉队者推出自己的更新机制，因为...

(≥的°)≥的)和±的

总之，ノ┬─┬ノ()我写这篇文章的时候是一大早。我真的很想使用你的开源项目并为之做出贡献，但是不要让它变得多余。幸运的是，我们可以一起解决这个问题，这也将使你作为维护者的生活变得更容易。

首先，这不仅仅是更新软件包的开销问题。全局依赖意味着系统上(可能)只有一个版本。如果我有多个依赖于该工具不同版本的包，我就有麻烦了。

# 🆘保存此开发者

🚫因此，如果你阅读或想写一些建议的文档:

```
npm -g install my-cool-tool 
```

Enter fullscreen mode Exit fullscreen mode

✔️，我想让你写:

```
npm --save-dev my-cool-tool 
```

Enter fullscreen mode Exit fullscreen mode

这将添加`my-cool-tool`作为项目的开发依赖项。这意味着将有一个可执行文件作为`./node_modules/.bin/my-cool-tool`访问。当然，每次输入都很麻烦。

🚫那么，你打算在哪里运行这个:

```
my-cool-tool 
```

Enter fullscreen mode Exit fullscreen mode

🚧相反，我们将利用我们的`package.json`中的`"scripts"`部分，因为我们放在那里的任何命令将首先检查我们的包`./node_modules/.bin`目录。

```
{  "scripts":  {  "my-cool-tool":  "my-cool-tool"  }  } 
```

Enter fullscreen mode Exit fullscreen mode

✔️现在，你可以运行以下:

```
npm run my-cool-tool 
```

Enter fullscreen mode Exit fullscreen mode

# 🏄就是这样！

这大部分只是一个翻译，不是一个大的转变——但我认为他们的回报是值得的。现在，如果我想使用或参与你的项目，我只需要运行`npm install`就知道我已经自动建立了我的开发环境。谁知道呢，那个开发人员一年后在一台新机器上回到项目中...甚至可能是你！