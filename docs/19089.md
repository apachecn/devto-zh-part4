# 把 GitHub 变成你自己的注册表

> 原文：<https://dev.to/mgattozzi/turning-github-into-your-own-registry-1i9n>

[![Turning GitHub Into Your Own Registry](img/39c1b851d315665a57daeb365cda3ba3.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--BU2Fw_kw--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://mgattozzi.com/conteimg/2019/06/hamburg-3021820_1920.jpg)

偶尔会有想法。它们不一定是好主意，有时我会错误地按照它们去做。你将要读到的就是其中之一。

> 听着，我不是说我把 [@github](https://twitter.com/github?ref_src=twsrc%5Etfw) 变成了 [@rustlang](https://twitter.com/rustlang?ref_src=twsrc%5Etfw) 包的备用注册表，但事实上我做到了。现在，如果他们在回购测试版中加入实际支持，那就太好了。【pic.twitter.com/GadqKxXSbJ T4】
> 
> —迈克尔·加托齐([@姆加托齐](https://dev.to/mgattozzi))[2019 年 6 月 11 日](https://twitter.com/mgattozzi/status/1138294090355871745?ref_src=twsrc%5Etfw)

那么，亲爱的读者，你是如何做这件奇怪(有点没用)的事情的呢？跟着我你就进入了一个纯配置的世界。

* * *

## 像你的 git 回复一样索引我

第一步是你必须建立一个索引。替代登记处的工作方式是，它假设你有一个索引，告诉它你有哪些板条箱，以及在哪里可以得到这些东西。首先，您需要在 GitHub 上设置一个 git repo。现在，我还没有用私人回购测试这一点，但我怀疑它不会因为认证的原因而工作。对于注册中心来说,`cargo login`的令牌方面可能会起作用。很可能不会。出于我们的目的，假设我们对所有事情都使用公共回购。

在 repo 的顶层创建一个名为`config.json`的文件，看起来应该是这样的(用你的 github 用户名替换`mgattozzi`:

```
{
    "dl": "https://github.com/mgattozzi/{crate}/releases/download/v{version}/{crate}-{version}.crate"
} 
```

如果您正在运行一个`crates.io`实例，web api 还有另一个字段，但是我们不在这里，所以我们可以删除它。我们告诉 cargo 的是，它需要的所有板条箱文件将在我们拥有的某个回购中，板条箱名称是回购名称，并且作为标记发布的一部分。这允许我们为下载设置一个确定性的 url，按版本发布我们的板条箱，并让它为我们所有的个人板条箱工作。现在 url 的`/v`中的`v`不需要在那里。这只是我用来标记发布的前缀。只要你是一致的，但是当你标记你的版本时，版本是它的一部分，你应该是好的。

酷，所以我们有一个配置文件。有没有一种实际列出板条箱的方法？有一种文件格式和存储文件的方法。以下是文档中文件层次结构的规则:

> 索引存储库的其余部分包含每个包的一个文件，其中 filename 是小写的包名。包的每个版本在文件中都有单独的一行。这些文件组织在一个目录层中:
> 
> *   带有 1 个字符名称的包放在一个名为`1`的目录中。
> *   有 2 个角色名的包放在一个名为`2`的目录下。-有 3 个字符名称的包放在目录`3/{first-character}`中，其中`{first-character}`是包名的第一个字符。
> *   所有其他包都存储在名为`{first-two}/{second-two}`的目录中，其中顶层目录是包名的前两个字符，下一个子目录是包名的第三和第四个字符。例如，`cargo`将被存储在一个名为`ca/rg/cargo`的文件中。

所以在这个例子中，我的箱子的名字是`abrade`，所以我的文件应该在`ab/ra/abrade`下。

下面是它的样子:

```
{"name": "abrade","vers": "0.1.0","deps": [],"cksum": "65b07c5782e771125d9feda1ec08d908087997051339c492220346a14d6dd936","features": {},"yanked": false,"links": null} 
```

注意，一个板条箱的每个版本都有一个条目，并且必须在它自己的行上，否则它就不能工作。这意味着多行 JSON 是行不通的。cksum 字段是我们需要生成的`.crate`文件的 SHA256。完成这一步后(我们将在接下来介绍)，提交文件和配置，并将其推送到 GitHub。注册中心的[文档有点简单，但是更深入地描述了一个条目应该是什么样子。还有](https://doc.rust-lang.org/nightly/cargo/reference/registries.html#running-a-registry)[`crates.io`的实际指数](https://github.com/rust-lang/crates.io-index)，你可以看更多的例子。

* * *

## 框

好的，那么你如何生成一个`.crate`文件呢？幸运的是`cargo`有一个子命令给我们！在你知道你将在发布中使用什么提交，并把它推送到 GitHub 之后，只需运行`cargo package`。如果你在`target/package`下寻找，你应该会看到一个文件`{crate}-{version}.crate`。我的情况是`abrade-0.1.0.crate`。就是这样。接下来你要做的是在 GitHub 上标记一个发布，并将这个`.crate`文件作为发布的一部分。最终在我们自己的代码中使用它。

* * *

## 我、我的注册表和我

好的，所以无论你有什么项目，你都要指定它应该使用你的替代注册表。在你的`Cargo.toml`里放一些像这样的东西(取决于你的箱子):

```
[dependencies]
abrade = { version = "0.1.0", registry = "my-registry" } 
```

现在`cargo`不知道`my-registry`是什么，我们需要告诉它。打开这个文件，因为我们需要修改它`~/.cargo/config`(或 Windows 上的等效)。如果您以前没有这样做过，这个文件可能不存在。那也行！你需要向它添加这样的东西:

```
[registries]
my-registry = { index = "https://github.com/mgattozzi/mgattozzis-crates-on-github-index.git" } 
```

这是您的索引的`http` url。把我的`username/repo`换成你的做你的索引。那就这样吧。`cargo build`你应该看到它把它拉下来并编译它！

你可以在下面找到我的回购作为参考:

*   index:[https://github . com/mgattozzi/mgattozzis-crates-on-github-index](https://github.com/mgattozzi/mgattozzis-crates-on-github-index)
*   板条箱:[https://github.com/mgattozzi/abrade](https://github.com/mgattozzi/abrade)

你可以在[这个链接](https://doc.rust-lang.org/cargo/reference/config.html)找到更多关于`cargo`的配置文件。

* * *

## 货物养殖

你真的应该这么做吗？我的意思是不，可能不会，但你可以，这是很酷的事情。有一些限制(比如只有你的板条箱),这是一个手动过程，因为你不能只是`cargo publish`但这是一个自动化的事情。如果你想成为一个叛逆者，你仍然可以选择，但是你最好使用 crates.io 或者在你自己的服务器上建立一个注册表。我希望你喜欢跟随一个坏主意。看到系统以非常规的方式使用总是很有趣。