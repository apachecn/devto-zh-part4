# 救命，我已经从 environment.prod 导入了环境！

> 原文：<https://dev.to/gparlakov/help-i-ve-imported-environment-from-environment-prod-450h>

即在构建时被替换的角度环境文件。

[![](img/6596baf71e4bd48e644aaeeaec97366f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--zlALPA0i--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AYN5ZOFuixpYzH4wW6jgQGA.png)

当人们错误地导入环境时，如何自动执行“不再从环境导入环境”和保持警惕和愤怒？因为 VS 代码和现代 Typescript 使得犯这个错误变得非常简单。只需 Ctrl +。在弯曲的下层环境之上，我们得到了环境。通常，我们会得到

从'导入{环境}../environments/环境'；

但是有时候我们会

从'导入{环境}../environments/environment . prod '；

你可能会问，这有什么不同呢？在**的第二种情况**中，我们希望使用**环境**来做出逻辑决策的模块/组件，我们总是选择生产——无论是在生产、试运行还是开发/质量保证阶段。更糟糕的是，当它来自../environments/environment.qa '那么这个问题只会在生产中表现出来！不酷！

好吧，所以答案是——进口时要小心——是吗？嗯，这可能在 99%的情况下有效。而当有人忘记、遗漏了那 1%的时候，就会带来悲伤和愤怒。

那么，我们如何实现自动化呢？

答案是 [**tslint**](https://palantir.github.io/tslint/) **。**如果你还没有使用它，也许是时候开始使用了。它对您的代码进行静态分析，并可以帮助自动化各种检查。包括[黑名单进口](https://palantir.github.io/tslint/rules/import-blacklist/)。