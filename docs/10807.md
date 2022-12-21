# 如何使用 twurl 快速测试 Twitter API

> 原文：<https://dev.to/twitterdev/how-to-use-twurl-to-quickly-test-the-twitter-api-4n48>

您是否曾经需要快速尝试针对推特应用编程接口的查询？那么...满足 [twurl](https://github.com/twitter/twurl) 。

### 瑞士军刀为碎碎念

我💙`twurl`。这是一个开源的命令行工具(用 Ruby 编写),使您能够快速地从 Twitter API 中获取数据，并尝试查询语法。我每天用它来验证错误报告，帮助验证通过我们的[开发者论坛](https://twittercommunity.com)提交的测试案例，或者帮助回答栈溢出`twitter`标签上发布的问题。

为什么叫“twurl”？简单——有点像 Twitter API 的 curl！🤓

使用`twurl`，您可以测试单个端点和查询，并检查响应。因为它是一个命令行工具，你可以用一个方便的 JSON 工具来链接它，比如 [jq](https://github.com/stedolan/jq) 。jq 让您能够以一种漂亮的方式快速格式化 JSON 文档和 API 响应，并查询或重新格式化它们。哦，当然，它对于编写脚本非常方便📄你可以在[网站](http://stedolan.github.io/jq/)上找到更多信息，包括很多如何有效使用它的例子。

*   这里需要注意的一点是，因为 jq 的大整数有一些问题，它会破坏 Tweet 对象中的数字长整数 id，所以你应该总是使用`_str`变量。

### 入门

你首先需要安装一个可以工作的 Ruby 解释器。在我自己的 Mac 上，我使用的是最近通过 rvm 安装的 Ruby(历史上我更喜欢 rbenv，但我认为两者都是有用的版本和安装管理器)；在 Linux 安装中，很有可能已经预装了 Ruby，但如果没有，您的软件包管理器可能会帮助您；在 Windows 上，你可能可以使用 [RubyInstaller](https://rubyinstaller.org/) 。

你需要做的另一件事是注册一个 [Twitter 开发者账户](https://t.co/apply-for-access)，然后在你的仪表盘上创建一个 Twitter 应用程序。一旦你这样做了，你将需要从应用程序的细节- >密钥和令牌标签获取你的消费者 API 密钥和 API 秘密。无需在仪表板上生成访问密钥和密码，因为您将在以下步骤中授权您的应用程序。

### 下一步

`$ gem install twurl`

一旦你的`twurl` gem 安装完毕，你应该会发现运行`twurl`命令行会显示帮助信息(通过`which twurl`确认)。

很好！我们开始了。现在让我们授权给 Twitter。您可以使用`twurl -T`调用教程，`twurl -h`调用选项列表，或者您可以继续使用完整的`twurl authorize`语法并提供您的消费者 API 密匙和 API 秘密。

### 准备好了吗？

一旦获得授权，这里有几个简单的命令供您尝试。

**获得[@杰克](https://dev.to/jack)的第一条推文:**

`$ twurl "/1.1/statuses/show/20.json"`

**获取@twitterdev 的用户资料:**

`$ twurl "/1.1/users/show.json?screen_name=twitterdev"`

*   小费！不要被你的壳困住。在第二种情况下，我使用了带有`?param1=`语法的 URL 参数。如果我没有在整个 URL 两边加上双引号，我的 shell(在我的例子中是 zsh)就会用`zsh: no matches found`来响应。如果您在`&param2=`语法中包含额外的 URL 参数，情况会变得更糟，因为在许多 shells 中，`&`将在后台运行命令。如有疑问，加引号！

**发一条推文:**

`$ twurl -X POST "/1.1/statuses/update.json?status=this is a new Tweet sent using twurl"`

**从实验室端点获取带有投票的推文:**

如果你已经注册测试[新的 Twitter 开发者实验室 API](https://developer.twitter.com/en/labs)，你也可以使用`twurl`来测试它们。这里有一个例子，获取最近的一条带有民意调查的 Tweet，请求详细信息并扩展民意调查数据和作者用户对象。

`$ twurl "/labs/1/tweets?ids=1155833627743272960&format=detailed&expansions=author_id,attachments.poll_ids"`

请记住，如果您想打印出漂亮的结果，您可以做一些类似于将输出传送到`jq`的事情。

### 想了解更多？

如果你喜欢从例子中学习，你会发现一个要点和一堆方便的`twurl`查询[这里](https://gist.github.com/andypiper/32bdb4c7f0b8d65385fc7c8fa3988083)，让你感受一下它有多强大。

还有关于如何将`twurl`与 [Twitter 广告 API](https://developer.twitter.com/en/docs/ads/general/overview) 一起使用的[指令](https://developer.twitter.com/en/docs/tutorials/using-twurl.html)(注意，广告 API 要求你的开发者账户有额外的白名单)；我们的朋友 Sam Schmir 也有他自己的关于媒体的[不错的教程](https://medium.com/@SamSchmir/a-guide-to-the-twitter-api-and-twurl-8711466a0635)，同样更多的是面向 Ads API，但是里面也有一些有用的提示。

如果有任何问题，请告诉我！