# 开发人员参考

> 原文：<https://dev.to/stereobooster/dev-wtf-developer-s-reference-4j65>

## WTF 开发者在说什么？

现代软件开发是一门复杂的学科。大量的行话、术语、俚语、表情符号、模因和文化参考使新手更难起步。

如果我们共同建立一个现代软件开发人员的术语和行话的参考网站。

我把它想象成面向开发者的城市字典和计算机科学基础百科全书的混合体。

### 替代品

“但是有很多选择”你可能会想，比如:

*   urbandictionary 对于现象行话来说是很棒的，但不是开发者专用的
*   MDN 是一个很好的资源，但是它的范围仅限于浏览器/JS 的特定主题
*   关于表情符号，你想知道的一切
*   [函数式编程术语](https://github.com/hemanth/functional-programming-jargon) -关于函数式术语
*   关于软件开发的一些有趣的观点
*   行话文件 -一个全面的黑客俚语概要，阐明了黑客传统、民间传说和幽默的许多方面

(你知道好的替代品吗？请让我知道)

是也不是。是的，有很多很棒的资源。不，它们没有涵盖所有领域，因此您需要参考多个来源，术语可能会有所不同。我觉得情况可以改善。

## 什么？

### 面向开发者

当开发人员说“PR”时，他们很可能指的是拉动请求，而不是公共关系。这份参考资料将集中于软件开发相关的主题。

### 新手友好

> 幺半群是内函子范畴中的幺半群，有什么问题？
> 
> ——来自[的笑话一部简短、不完整且大多错误的编程语言史](http://james-iry.blogspot.com/2009/05/brief-incomplete-and-mostly-wrong.html)

解释应该以平易近人的方式进行，显然，事情可以简化的范围是有限的。我们到 5 岁还无法恰当地解释量子物理，但我们需要不断尝试。

一个解释复杂主题变得可接近的好例子是 [Khan Academy 用颜色解释公钥密码学](https://www.khanacademy.org/computing/computer-science/cryptography/modern-crypt/v/diffie-hellman-key-exchange-part-1)。

### 基础知识

我很难找到一些解释的基础知识，人们经常跳过解释基础知识，写一些复杂的东西(我猜是他们觉得有趣和有挑战性的东西)。

在这个问题上有一篇很好的文章 Christopher Strachey 的《编程语言中的基本概念》，但它是在 1967 年写的(许多东西仍然相关，但一些术语已经过时)。

### 一致性

不同的编程语言使用不同的术语来表示相同的概念，例如，vector、array、list、queue 都可以表示相同(或不同)的意思。这将是很好的达成一些一致性，并能够列出不同语言之间的对应关系，(所谓的[罗塞塔石碑](http://rosettacode.org/wiki/Rosetta_Code:About))。

### 不是 API 引用

这个想法是为了涵盖基本概念和术语，而不是成为 API 的参考。我们不想替换掉 [devdocs](https://devdocs.io/) 或者 [MDN](https://developer.mozilla.org) 。

### 批判性思维

许多想法都被认为是“最佳”实践，但是没有灵丹妙药，所有的解决方案都需要权衡。这些权衡应该被强调出来，这样读者才能得出他们的结论。

例如:

*   设计模式经常没有列出[缺点](https://www.deconstructconf.com/2017/brian-marick-patterns-failed-why-should-we-care)或[选择](https://github.com/thma/LtuPatternFactory)
*   函数式编程经常被认为是最终的解决方案，但是也有其他范例的用例

### 站在巨人的肩膀上

这不是试图从头开始重做一切。如果其他来源已经很好地解释了一些主题，我们可以引用。

### 正确，但不枯燥

这个想法是为了给计算机科学术语给出正确的定义，但是我们可以在这个过程中获得乐趣，并在这里或那里丢弃迷因和一些艺术。

[![](img/8bfdcb27886447005392cde31d3d8c5f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--OKqLiU54--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/template-sticker-600x600.png)

图:美国运通的贴纸。

## 如何？

这将是一个开源项目。一个静态网站产生的降价文件与前面的事情，相同的概念，如哲基尔，雨果或盖茨比。

目前，[源将被托管在 Github](https://github.com/stereobooster/dev.wtf) 上，因此任何拥有 Github 账户的人都可以贡献内容。

### 实现

主要关注的是内容，执行不太重要。任何与 markdown 文件一起工作的东西都可以工作。我选择用 Hugo 做 MVP(最小可行产品)，不过这个以后可以改。

## 例子

参考可能涉及(也可能不涉及，因为这是一个社区驱动的项目)的一些主题示例:

*   计算机科学术语:数组、闭包
*   开发术语:控制台、服务器
*   缩写:a11y(可访问性)，l10n(本地化)
*   现象行话:自行车脱落，看门，10 倍
*   专业术语:vulns(漏洞)，repo(存储库)
*   网络缩略语和表情符号:thx(感谢)，`(╯°□°)╯︵ ┻━┻`(翻表)

## 详细信息

每篇文章至少由标题和定义组成。可选参数:

*   封面图片:社交网络
    *   可以自动生成。就像他们对戴夫·托做的那样
*   标签
*   相关-相关文章
*   又名-术语的其他名称
    *   可用于生成重定向页面

## 有什么可以帮忙的？

*   [提问](https://github.com/stereobooster/dev.wtf/issues)。如果你发现一些术语混淆了开放问题，要求解释
*   [答题](https://github.com/stereobooster/dev.wtf/new/master/content/posts)。发送带有答案的请求
*   编辑语言。一些作者将感谢帮助修复错别字和错误
*   添加图片。你不必是一个开发者来贡献，你可以通过提供封面图片和帮助设计相关的事情来提供帮助
*   [改进代码](https://github.com/stereobooster/dev.wtf)。你对如何改进网站有什么想法吗？请发 PR。

## 其他想法

*   [多层字典](http://learnthesewordsfirst.com/about/what-is-a-multi-layer-dictionary.html)
*   [互动、直观的解释](https://www.cs.usfca.edu/~galles/visualization/Algorithms.html)

## PS

我需要提到萨拉·维埃拉，我所知道的最好的开发者鳄梨，也有同样的想法。

**概念验证**:【https://devwtf.netlify.com/ T2】。

> 来自 [unsplash](https://unsplash.com/photos/Oaqk7qqNh_c) 的封面图像