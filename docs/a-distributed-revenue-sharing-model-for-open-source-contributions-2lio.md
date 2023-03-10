# 开源赞助的分布式收入共享模型

> 原文：<https://dev.to/webextensions/a-distributed-revenue-sharing-model-for-open-source-contributions-2lio>

我已经创建了几个基于 JavaScript 和 CSS 的开源项目。就我而言，我的目标从来都不是通过它赚钱。但是，如果我们考虑整个社区，从开源项目中赚不到钱限制了它的潜力。

最近，一项基于广告的创收实验被停止了([https://feross.org/funding-experiment-recap/](https://feross.org/funding-experiment-recap/))。尽管执行面临一些利益相关者的强烈反对，但大多数人都支持实验背后的想法。

这一事件让我将我脑海中的一个想法转化为纸上的概念证明，以供讨论。

我已经分享了一个开源贡献的分布式收入共享模式的建议，网址:[https://github . com/web extensions/distributed-open-source-contributions/blob/master/readme . MD](https://github.com/webextensions/distributed-open-source-contributions/blob/master/README.md)

这是要点。

# 问题陈述

开源开发者在开发和维护开源项目上花费了大量的时间。但是，目前流行的货币补偿模式只偏向于少数受欢迎并被赞助商直接使用的开源项目。由于这个原因，那些开源项目的传递性依赖不能够吸引足够的金钱利益。

**注意:** *这些可传递依赖关系中的一些可能是可检测的，例如，通过 Node.js 项目的 package.json。而其他一些可能会在后台使用，因此无法自动检测到，例如像 GIMP 这样的工具。*

# 提出解决方案

每一个开源项目都应该能够与其他开源项目分享一定比例的赞助金额。

## 示例工作流程

*   假设我们有一个基于 Node.js 的开源项目，名为`awesome-contributions`
*   一家善解人意的公司利用了`awesome-contributions`项目，决定每周出资 200 美元赞助该项目。
*   项目背后的团队已经使用了许多其他开源项目，没有这些项目,`awesome-contributions`就不可能建成。该团队希望与那些开源项目(姑且称之为“可传递项目”)分享他们所收到的贡献。
*   `awesome-contributions` project 在已实施本提案的平台上使用以下“贡献分享规则”:

```
 {
      "contributionsShare": {
          // Out of all the sponsorship amount received via "GitHub Sponsors", this
          // percentage of the amount would be shared to other projects listed in
          // "shareContributionsWeightages". The remaining sponsorship amount would be
          // transferred to the accounts listed in the object "projectPartnersWeightages"
          "sharePercententageOfContributionsReceived": 50,

          // These "weightages" are just another way of representing share "percentages"
          "projectPartnersWeightages": {
              // Using "@" symbol to indicate that it is a project-partner account
              "@webextensions": 60,

              // "*" indicates remaining project-partners, who are not listed in the
              // object "projectPartnersWeightages"
              "*": 40
          },

          // These "weightages" are just another way of representing "percentages"
          "shareContributionsWeightages": {
              "eslint/eslint": 1,
              "facebook/react": 0.95,
              "GoogleChrome/puppeteer": 80,
              "jquery/jquery": 0.05,
              "webextensions/live-css-editor": 8,

              // "*" indicates remaining projects listed in "package.json"
              "*": 10
          }
      }
  } 
```

*   这意味着，从那 200 美元，在一周内:

    *   50%归`awesome-contributions`的项目合作伙伴，即 100 美元
    *   50%将给予`shareContributionsWeightages`项下提及的过渡项目的过渡项目合作伙伴，即 100 美元
*   这实际上意味着，从这 200 美元中，在一周内:

    *   `eslint/eslint`将获得 1 美元
    *   `facebook/react`将获得 0.95 美元
    *   会得到 80 美元
    *   `jquery/jquery`将获得 0.05 美元
    *   会得到 8 美元
    *   其他过渡项目将总共获得 10 美元，这 10 美元将在他们之间平均分配
    *   一周内会得到 60 美元
    *   其他项目合作伙伴将在一周内总共获得 40 美元，这 40 美元将在他们之间平均分配
*   其他项目也有他们自己的“贡献分享规则”,这将进一步分配赞助金额。

# 更

这篇文章展示了收入共享模型的超级简化版本，它可以自动扩展。更多细节和边缘情况在详细文档中处理。

详细文档可从以下网址获得:[https://github . com/web extensions/distributed-open-source-contributions/blob/master/readme . MD](https://github.com/webextensions/distributed-open-source-contributions/blob/master/README.md)

欢迎在此评论。
欢迎在文档的 GitHub 页面提出问题和请求:-)

PS:在写这篇文章的时候，我了解到 NPM 最近的一项声明，他们将采取一项措施，使通过他们的注册来资助项目变得更加容易。我希望他们的解决方案与这种模式相融合，并在以后扩展到所有开源项目，就像在这个提案中描述的那样。