# GSoC 2019 的经验教训

> 原文：<https://dev.to/matheusrich/lessons-learned-on-gsoc-2019-1kcl>

打开 Ruby gems(Ruby 语言的一部分)的 Pull 请求对我来说似乎是一件可怕的事情。比如，有一天我所有的代码都可以在其他人的机器上运行。如果我引入了一个安全漏洞呢？虽然我从 2016 年就开始为开源项目做贡献，但这些想法一直留在我的脑海里。

事实证明，这是一次非常棒的经历。我的代码并不“完美”(如果存在的话)，我犯了很多错误，但在这个过程中我学到了很多东西，所以**真的值得**！

[![](img/a361b33919c25f328755a64104e5e98a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--CwHehVXt--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/http://matheusrich.github.io/assets/img/no-pain-no-gain.gif)

嗯，我想这就是“不劳无获”的原则。=)

## 吸取教训

这里有一些建议，所以我们(是的，我也包括在内)可以在未来避免这些错误:

### 1。保持简单愚蠢

> 如果你不知道的话， [KISS](https://en.wikipedia.org/wiki/KISS_principle) 是一个“真实的东西”，不仅仅是我用的一个很酷的表达

好吧，好吧，好吧…这是事情变得尴尬的地方(对我来说)。作为开发人员，我们倾向于**高估**我们的问题，这导致**过度复杂的**解决方案。这个问题出现在我的 ruby gems PR 的初始代码上。

我并不完全清楚每一张 [gem-web](https://github.com/bitboxer/gem-web/) 是做什么的，所以我试图尽可能少地修改它。Gem-web 使用 Rspec 进行测试，而 Rubygems 使用 Minitest。我知道我必须更新所有的测试，但是我保留了(几乎)所有的开发依赖。

所以，当我打开 PR 时，rubygems 的 gemspec 是这样的:

```
+ s.add_dependency(%q<launchy>, ["~> 2.4.3"])
+ s.add_development_dependency(%q<mocha>, ["~> 1.7.0"])
+ s.add_development_dependency(%q<webmock>, ["~> 3.5.1"])
+ s.add_development_dependency(%q<vcr>, ["~> 4.0.0"]) 
```

Enter fullscreen mode Exit fullscreen mode

因此，我不仅添加了三个新的开发依赖项(mocha、webmock 和 vcr)，还添加了一个运行时依赖项(launchy)。这尤其糟糕，因为 PR 目标是编程语言的一部分！

正如我的导师所指出的，古老的(已经添加的) **Minitest** 可以取代所有那些与测试相关的依赖。

> 我知道这些宝石的存在是有原因的，你(像我一样)可能喜欢 mocha 的风格，而不是 minitest 的，但如果你想增加你的 PR 被合并的机会，这是一个很好的做法，不要添加一堆依赖。

现在我不得不去掉 Launchy(用来自动打开用户浏览器上的 URL)。我的第一个想法是创建一个包含 ruby 可能运行的所有平台的散列，以及一个打开默认浏览器的方法(我是个天才)。

```
OPEN_BROWSER_CMDS = {
  aix: "defaultbrowser",
  cygwin: "cygstart",
  darwin: "open",
  macruby: "open",
  freebsd: "xdg-open",
  # FIXME: What to do?
  # hpux: "",
  # java: "",
  # dalvik: "",
  # dotnet: "",
  linux: "xdg-open",
  mingw32: "start",
  netbsdelf: "xdg-open",
  openbsd: "xdg-open",
  bitrig: "xdg-open", # check this
  # solaris: "sdtwebclient", # version < 11
  # solaris: "xdg-open", # version > 11
  unknown: ""
}.freeze 
```

Enter fullscreen mode Exit fullscreen mode

如您所见，我对一些平台(如 java 和 dotnet)一无所知，一些平台有不同的命令来打开基于其版本的默认浏览器(如 Solaris)。这个解决方案不够好，所以我把这个散列改成了一个**大而丑的开关盒**:

> 你可以在这里
> 看到完全的笨拙

```
def open_default_browser_cmd(local_os, version)
  case local_os
  when 'aix'
    'defaultbrowser'
  when 'cygwin'
    'cygstart'
  when 'darwin'
    'open'
  # ... many other cases
  when 'linux'
    'xdg-open'
  when 'mingw32'
    'start'
  when 'solaris'
    if version < 11
      'sdtwebclient'
    else
      'xdg-open'
    end
  else
    ''
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

显然，这是一个糟糕的解决方案(我也知道)，但我想不出其他方法来完成它(没有外部依赖)。因此，我还是打开了拉取请求，这样社区就可以在这方面提供帮助。

他们对我说的一件事是,`open_default_browser_cmd`很难维护，并且“如果用户只是想看到 URL，而根本不想打开它怎么办？”。然后[安德烈·阿科](https://github.com/rubygems/rubygems/pull/2835#issuecomment-512542718)带来了一个我没有想到的简单而强大的解决方案:“只需从环境变量中读取浏览器”。这太简单了！现在，用户可以选择是否要自动打开浏览器(以及应该使用什么命令)。那段可怕的代码变成了这样:

```
def open_browser(uri)
  browser = ENV["BROWSER"]
  if browser.nil? || browser.empty?
    puts uri
  else
    system(browser, uri)
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

再说一遍:**保持简单，笨蛋！**

### 2。沟通

我喜欢开源(尤其是 Ruby)的一点是社区！与人互动，教学，向他们学习，这太酷了！

一定要利用这一点！就像我在上一节所做的:我不确定某个功能，社区引导我。他们甚至向我展示了我没有想到的用例。不要羞于提问！

还有那种担心增加一个*安全漏洞*？那么，这里的社区可以(也愿意)帮助你！这就是[@ simi 在我的 Rubygems 公关上做的](https://github.com/simi)。

哦，不要把事情看得太重！如果有人对你的代码添加了评论，那只是关于你的**代码**，不要为此难过，而是利用这个机会学习一些东西(或者证明你的观点，如果你对你的解决方案有信心的话)。

### 3。测试测试测试

当您向项目中添加新特性时，千万不要忘记添加测试！这极大地增加了你的改变被接受的几率。当然，它应该帮助你跟踪你的代码什么时候工作或者不工作，如果你插入任何 bug，它会警告你。使用覆盖工具来确保**你的每一段代码都被覆盖了！**

## 最后的想法

是的，这篇文章很大！但是它需要。成为 GSoC 的一员并为 Rubygems 这样的大项目做贡献是一次奇妙的经历！我将把我学到的经验带到我的职业生涯中，它们将引导我成为一名更好的程序员。

我非常感谢谷歌让这一切成为可能。我无法形容这个夏天有多凉爽！我还得感谢 Ruby 和 Rubygems 一路指引我走过这一切。感谢审阅我的 PR 的贡献者(特别是[路易斯](https://github.com/bronzdoc)、[艾伦](https://github.com/duckinator)和[约瑟夫](https://github.com/simi))，以及对 Slack 的支持([阿迪雅](https://github.com/sonalkr132)和[间接](https://github.com/indirect))。

[Saroj (@zoras)](http://github.com/zoras) ，我的导师简直太神奇了！他总是尽可能快地回答我，让我找到路，而不是马上给我答案！这真的很酷！谢谢大家！

最后，我要感谢所有在我身边一直鼓励我的人:我的未婚妻、我的朋友、我在 LAPPIS 的所有伙伴。爱你们！

如果你想成为 GSoC 2020 的一员，**现在就开始贡献吧！**无所畏惧！你能做到的！这里是[给你](https://dev.to/me-and-gsoc/)一些抢先一步的建议。

就这样，伙计们！如果你能坚持到这里，你就是最有价值球员。希望这里能帮到你！如果是的话，告诉我！下次见！再见！

[![](img/daf747e2c353248272edaaa93d9285c9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--MIClSUtL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/http://matheusrich.github.io/assets/img/thanks.gif)