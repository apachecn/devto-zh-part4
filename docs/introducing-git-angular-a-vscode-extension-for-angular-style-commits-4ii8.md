# Git Angular 简介:Angular 风格提交的 VSCode 扩展

> 原文：<https://dev.to/jhechtf/introducing-git-angular-a-vscode-extension-for-angular-style-commits-4ii8>

## 为什么会有这种延伸？

我参与的很多项目都有一些不同的模式，你需要遵循这些模式才能有所贡献。这很大程度上是因为提交消息将成为发行说明和/或以后的变更日志的一部分。

我之前的公司有一个非常粗糙的 Git 消息流程，我的大多数个人项目都倾向于提交给“未来的 Jim”阅读。正因为如此，我个人采用了 Git-Emoji 提交风格，因为我喜欢在我的提交中加入一点香料。

然而，我现在的公司对他们的承诺结构非常挑剔。他们使用了一个版本的 [Angular 的提交消息指南](https://github.com/angular/angular/blob/master/CONTRIBUTING.md#commit)。问题是我正经历着*最艰难的*时期，所以我写了这个小小的扩展来帮助我自己，我想我会和其他人分享它。

## 它有什么作用？

很简单的一件事:*帮助您构建角度式提交的起点*。

## ...好吗？

我知道，这听起来不那么可怕，如果我诚实地说，这个扩展并不意味着像 [GitLens](https://marketplace.visualstudio.com/items?itemName=eamodio.gitlens) 那样的东西，它只是一个简单的助手，可以插入到 VSCode 的默认 Git 扩展中。

Git Angular 最大的特点是它是可配置的，无论是全局配置还是通过工作空间配置。这样做是因为不是每个使用 Angular 提交风格的人都会使用 *Angular 的*提交风格(很像我现在的公司)。您和您的团队可以使用不同的类型或范围，Git Angular 允许这样做。

## 我怎么用？

不幸的是，VSCode 有大约一百万个标题中带有“Angular”的扩展，所以最好跟随[这个链接](https://marketplace.visualstudio.com/items?itemName=Jhecht.git-angular)。否则，我发现找到它的最简单的方法就是在 VSCode 扩展搜索框中搜索“Git Angular”(包括引号)。

它提供了一个命令`gitAngular.commit`，您可以将其绑定到一个键绑定或从命令调板中使用。我个人把这个绑定到`ctrl+shift+c`

## 包装完毕

如果你认为这个扩展缺少了很多 Angular 风格的提交用户想要的东西，请告诉我。