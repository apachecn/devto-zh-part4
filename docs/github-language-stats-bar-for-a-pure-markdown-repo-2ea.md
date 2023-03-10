# Github 语言统计栏为一个纯粹的降价回购

> 原文：<https://dev.to/joshuatz/github-language-stats-bar-for-a-pure-markdown-repo-2ea>

如果你曾经使用过 Github，你可能已经注意到了语言细节统计栏(或者“语言分解栏”):

[![The Github Language Statistics Bar](img/1bb674ad514a45da97a77c565100dea0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--X1OAfRRW--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/3jhzlolxgpxtnqn0xq1y.png)

它实际上不会对任何事情产生影响，但它可以一眼告诉其他开发人员你的回购中可能有什么样的技术，并且在 Github 上搜索回购时，它确实会影响搜索结果。

不幸的是，如果您创建了一个 repo，并且只填充了降价文件(.md)，语言分解栏要么根本不会出现，要么如果你引入了一些非 markdown 文件，它只会显示那些。怎么回事？

问题和[解决方案](#solution)都是用一点点背景知识揭示的。

# 语言统计栏是如何计算的？

在内部，Github 使用一个叫做[语言学家](https://github.com/github/linguist)的工具来检测和分类代码文件。你可能不知道的是，语言学家也有很多规则来阻止一个文件对统计数据的贡献。如果某个文件被归类为以下任何一种类型，则该文件将被排除在统计之外:

*   卖方代码
*   生成代码
*   证明文件
*   数据
*   散文

有关详细信息，请参见自述文件的本节。

# 为什么 Markdown 不出现？

Markdown 没有出现的第一个原因是因为它立即被归类为“散文”，我们从上面知道，当谈到回购语言统计时，它被忽略。

第二个原因可能适用于你，也可能不适用；文件名和文件结构。例如，如果你所有的降价文件都被命名为“readme.md”或只是在一个名为“Documentation”的文件夹中，它们将被归类为“Documentation”并被排除在外。从“ [documentation.yml](https://github.com/github/linguist/blob/master/lib/linguist/documentation.yml) ”文件中的正则表达式模式可以看出这一点。

# 解

以前是没有解决这个问题的办法。然而，随着时间的推移，Github 向语言学家添加了“覆盖”，因此现在用户可以通过用 git 属性覆盖来修改文件的默认分类(参见[问题#3964](https://github.com/github/linguist/issues/3964) ，该问题已由 [PR #3807](https://github.com/github/linguist/pull/3807) 解决，并在 [#3806](https://github.com/github/linguist/pull/3806) 中给出了示例)。

我们如何实现覆盖？它实际上是令人惊讶的无痛。

**第一个**，创建一个”。gitattributes "文件放在您的 repo 的根目录下。然后，在该文件中使用[标准的 git 属性语法](https://git-scm.com/docs/gitattributes)来基于文件名或模式修改文件属性。Git 属性是一种为特定文件分配特定、自定义和任意属性的方式。

您可以覆盖的语言学家属性有:

*   语言学家-文献
*   语言学家-语言
*   语言学家-vendored
*   语言学家生成的
*   语言学家可察觉的

例如，这里有一个. gitattributes 文件，我已经强制 Github 将所有的降价文件计入我的语言统计，*readme . MD:
除了*

```
# Linguist overrides
*.md linguist-detectable=true
README.md linguist-detectable=false
readme.md linguist-detectable=false 
```

从技术上来说，我认为切换“语言学家可检测”应该是改变一个文件是否被忽略所需要的，因为它充当了最后的覆盖。如果您想 100%确定 markdown 没有被排除，您也可以覆盖任何可能阻止它被识别的属性，就像这样:

```
*.md linguist-vendored=false
*.md linguist-generated=false
*.md linguist-documentation=false
*.md linguist-detectable=true 
```

# 期末注释:

请记住，这里找到的解决方案适用于 Github stats 的许多问题；您可以很容易地用 Git 属性覆盖任何语言学家的分类。

另外， **Pro 提示:**您可以轻松检查您的文件模式是否正确。使用“git check-attr”命令创建 gitattributes 文件。例如，在我的文件夹“cheatsheets”中，要检查文件名为“web-dev.md”的 markdown 文件的属性，我会使用:

```
git check-attr -a cheatsheets/web-dev.md 
```