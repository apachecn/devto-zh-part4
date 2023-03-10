# 为什么我们要在 Azure Logic 应用中远离转换连接器(液体模板)？

> 原文：<https://dev.to/eromanowski/why-are-moving-away-from-the-transform-connector-liquid-templates-in-azure-logic-apps-hk5>

# TLDR

我们正在进行一个项目，该项目要求我们定期将数据从一种形状转换为另一种形状，以便进行集成。我们认为逻辑应用程序/集成帐户中的转换连接器中的流动模板似乎是一个合理的解决方案。我们很快发现开发人员的体验很糟糕。

请注意，Azure Logic Apps Transform connector 中的液体模板语法在 VS 代码中的工具之间有所不同。虽然您的转换可能在 VS 代码中工作，但您会经常发现它在 Azure 中并不像预期的那样工作。

# 我们遇到了什么？

VS 代码中可用的扩展是基于 ruby 风格的 liquid 模板构建的，而 Transform 连接器使用的是 DotLiquid 版本 2.0.254(截至 2019 年 3 月 11 日)。语法和功能都有区别。

*   [https://github . com/dot liquid/dot liquid/wiki/dot liquid-for-Designers](https://github.com/dotliquid/dotliquid/wiki/DotLiquid-for-Designers)

*   您会发现 ruby 风格中有更多 DotLiquid 中没有的特性

*   我从哪里得到 2.0.254 版本？直接来自微软支持团队的回复。问题是，这与 DotLiquid 的 Github 回购协议的任何发布都没有关联。

    *   [https://github.com/dotliquid/dotliquid/releases](https://github.com/dotliquid/dotliquid/releases)
    *   更新:我联系了 DotLiquid 的顶级贡献者，他告诉我他们已经很久没有使用 github 版本了。该版本与 nuget 版本相关。

# 我们的开发/部署过程是什么样的？

*   下载 VSCode 并安装 Shopify Liquid Preview ![Alt Text](img/721aee10c8960e8d1eecd203d84b114b.png)
*   创建一个回购
*   树枝
*   创建 transform.liquid 和 transform.liquid.json 并开始编写转换！到目前为止，这是一次很棒的开发者体验。
*   CTL+SHIFT+P，根据. liquid.json 中的样本数据预览转换
*   提交模板并创建 PR
*   一旦你的 PR 成功掌握，上传到开发 azure 环境，运行你的逻辑应用，开始感受痛苦
*   如果你遇到问题，深入研究 DotLiquid 源代码，发现这个特性不存在或者语法不同
    *   [https://github.com/dotliquid/dotliquid](https://github.com/dotliquid/dotliquid)
*   为你的模板创建另一个“azure”版本，并尝试保持两者同步
*   内心哭泣

# 给你提问

*   还有人感受到逻辑应用中转换连接器的痛苦吗？如果是，你发现了什么样的工具来提高开发人员的生产力？

# 微软的提案

*   创建与转换连接器中使用的 DotLiquid 版本一致的 DotLiquid 预览扩展
*   将新的映射类型部署到集成帐户(即 Liquid-Ruby)并更改配置属性`Liquid.UseRubyDateFormat = true;`
*   开放转换连接器的源代码，这样我们可以进行修改和扩展功能

## 旁注

这是我的第一篇博文。我很想听听您对我如何改进的反馈。欢迎任何反馈。