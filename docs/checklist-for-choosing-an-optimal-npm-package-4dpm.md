# 选择最佳 npm 包的清单

> 原文：<https://dev.to/adrianbdesigns/checklist-for-choosing-an-optimal-npm-package-4dpm>

毫无疑问，你可能至少在你的项目中使用过一次 npm。npm 帮助我们找到、安装和更新各种项目依赖项和包。在搜索 npm 软件包和安装 npm 软件包之前，需要记住几件事情:

*   这个包有漏洞吗？
*   包是否得到维护？
*   这个包有会影响我的用例的错误吗？
*   包装尺寸可以接受吗？
*   和同类套餐相比如何？

## 包有没有漏洞？

正如我们之前看到的，npm 包可能成为各种攻击和恶意代码注入的目标。虽然`npm audit`和`npm audit --fix`在跟踪和修复漏洞方面做得足够好，但是你可以在安装软件包之前使用 [snyk.io](https://snyk.io/) 在他们的漏洞数据库中搜索软件包。

[![Alt Text](img/f6bb88104069cef287cce67da02162f7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--VZepHaIj--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/cntv8w11zohppy2xp27n.png)

## 包是否正在维护？

检查软件包是否由作者或社区主动维护的最简单的方法是查看版本历史，看看软件包更新的频率。查看 github 上的问题部分，看看作者是否正在积极修复用户报告的错误，这也是很好的做法。

## 软件包是否有会影响我的用例的 bug？

与前一项类似，您可以查看 github 上的问题部分，看看是否有任何适用于您的用例的活动问题或错误报告。这些问题的几个例子是:与另一个包冲突、边缘情况问题、特定的性能问题等。

## 包装尺寸可以接受吗？

有时，一个包可能会有依赖性，这会显著增加包的大小，进而影响我们的网站/应用程序的大小和性能。为了避免这种情况，您需要检查
[Bundlephobia](https://bundlephobia.com/) 上的捆绑尺寸，看看这是否符合您的尺寸和性能要求。

[![Alt Text](img/6ca865dc5018d9ab89b61aec67fdf0b4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--bSk2n3tE--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/th3cdm44oyygudrmccxw.png)

## 与同类套餐相比如何？

通常，您可能会找到多个符合您要求的包。在这种情况下，您可能希望对它们进行并排比较，并确定哪个软件包真正符合您的需求、得到定期维护并得到广泛使用。

其中一个工具是 [npm 比较](https://npmcompare.com/)。它显示了两个软件包的非常详细的并排比较，并突出显示了每个指标(报告的问题、平均更新时间、大小等)中哪个软件包是最好的。).

[![Alt Text](img/64c643c76965f4c86ab49c57ea3b6ffe.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ApJThNIq--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/favubotmsu95j9jsv70r.png)

另一个有用的工具是 npm trends [npm trends](https://www.npmtrends.com) ，它显示了一段时间内包装的受欢迎程度以及简单、不太详细的比较。

[![Alt Text](img/f5f6daa1772f4744f70ac516c15ff521.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--OlH51nj_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/c12n0wchk4fqhlp8emp1.png)

## 还有什么需要注意的吗？

如果您在搜索和选择 npm 包时考虑了其他一些标准，请随意发表评论。

[![idrisrampurawala profile image](img/39a15fb848ae097b1dbcb554eadd7b34.png) ](/idrisrampurawala) [ Idris Rampurawala ](/idrisrampurawala) • [<time datetime="2019-08-23T04:16:56Z"> Aug 23 '19 </time>](https://dev.to/idrisrampurawala/comment/ee7n) 

总结了所有决策要点的精彩帖子:)

只想再补充一点:

### 版本兼容性

有时我们还需要检查包和依赖项是否不会与我们的项目依赖项版本冲突。

* * *

这些文章以咖啡为燃料。所以，如果你喜欢我的工作，觉得它很有用，请考虑给我买杯咖啡吧！我真的很感激。

[![Buy Me A Coffee](img/f7d2fd6a693d2b9dc44399aa250c8165.png)](https://www.buymeacoffee.com/ubnZ8GgDJ)

感谢你花时间阅读这篇文章。如果你觉得这很有用，请给它一个❤️或🦄，分享评论。