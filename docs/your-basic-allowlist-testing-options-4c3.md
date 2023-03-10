# 您的基本允许列表测试选项

> 原文：<https://dev.to/roesslerj/your-basic-allowlist-testing-options-4c3>

我是 allowlist-testing 的忠实粉丝。我真的认为 allowlist-testing 是测试任何接口的未来。对于那些不熟悉这个术语的人来说:典型的基于断言的测试是拒绝列表测试(拒绝指定的更改，忽略所有其他更改)，允许列表测试防止所有更改，除了您指定为不相关的更改，即允许列表的更改。这种技术的其他名称有白名单/黑名单测试(现在[因种族歧视](https://www.clockwork.com/news/creating-inclusive-naming-conventions-in-technology/)而被驳回)、差异测试、快照测试、金主测试、批准测试或特征测试。

我想拿出一个具有 Allowlist 测试功能的工具的可执行演示。它应该可以让你专注于这个概念，并且用最少的开销来获得一些第一印象。这样的演示可以在研讨会和会议中使用，它可以在推特上发布，也可以很容易地分享。为此，有 3 个简单的要求:

*   这个工具应该是没有麻烦的，安装和使用起来都很简单，以尽可能少的开销和知识与技术方面的先决条件来产生结果。
*   该工具应该演示 allowlist-testing 的许多优点。
*   它应该是独立于厂商的，也就是说，理想的工具应该是开源的，并且至少是商业化的。

这个需求列表很短，也相对简单，并且有许多 allowlist-testing 的实现。然而，在我看来，很难找到符合这些标准的候选人。

# 轻松简单的尝试

这个工具应该简单易用，这使得很多有趣的候选人失去了资格。大多数开源框架都是开发人员在开发过程中使用的测试框架。这意味着您需要为测试执行建立一个带有某种构建机制的项目环境。

### 是

Jest([https://github.com/facebook/jest](https://github.com/facebook/jest))被 GitHub 上超过 1，000，000 个公开回复所使用，是一个非常流行的 allowlist-testing 示例——如果不是最流行的示例的话。然而，它只能在 Node、React 或 Angular 项目环境中使用 JavaScript。设置 yarn 或 node.js 以及任何这样的项目，只是为了执行一些简短演示的测试，这肯定既不简单也不容易。

### 批准测试

这同样适用于另一个允许列表测试的好例子，那就是批准测试([https://approvaltests.com/](https://approvaltests.com/))。Approval Tests 几乎可以在任何平台(Java、C++、NodeJS、Python 等)上作为一个库使用。但是它主要集中在技术接口上，并且可以很好地处理 XML 和其他技术格式。使用它的障碍肯定比使用 Jest 要低得多。您只需要一个可以在任何支持的平台上执行的测试，例如 Java。它是开源的，可以免费使用，所以批准测试也符合其他标准。

### 文本测试

另一个很棒的工具是 text test([http://texttest.sourceforge.net](http://texttest.sourceforge.net))，它运行在 Python 上，可以与许多其他平台结合，包括 Java Swing、SWT 和 Tkinter。要设置它，您需要安装 Python 和其他一些东西，这取决于您选择的平台。然而，它非常适合领域语言编写的测试。对于更直观的测试，例如 GUI(自定义或 Web)测试，需要更多的工具来驱动 GUI。对于 Python 和 Java GUIs，有 StoryText，它是专门为与 TextTest 一起工作而设计的。

### 复检-网页

recheck-web([https://github.com/retest/recheck-web](https://github.com/retest/recheck-web))是开源的，并附带了一个 Chrome 扩展，可以用来轻松地尝试它。Chrome 扩展安装和移除都很简单。为了让 Chrome 扩展在没有任何额外安装成本的情况下运行，它会将数据发送到 retest.org。为了保护您明智的测试数据，您需要在尝试之前创建一个免费帐户。详细的结果是专有格式的，你需要一个开源的 CLI 或免费的 GUI 来打开。GUI 包含在一个自包含的 ZIP 文件中。

# 演示 Allowlist-testing 的优势

虽然像素比较是 Golden Master 测试的一种方式，但工具必须实现某种机制来允许列表更改(即，在更改发生时不通知用户)，以便也算作允许列表测试工具。这一点很重要，因此该工具可以定期用于测试自动化，而不会报告(太多)误报。许多工具在这方面都失败了，例如

*   估([https://github.com/AppraiseQA/appraise](https://github.com/AppraiseQA/appraise))
*   screenster . io([https://screenster . io](https://screenster.io))
*   Diff.io ( [https://diff.io](https://diff.io) )
*   shutter([https://shutter . sh](https://shutter.sh))

从长远来看，单纯的像素比较价值有限。一个好的工具应该既允许大规模接受变更，因为允许列表测试经常会产生冗余，又应该提供方便的忽略选项。

# 做开源而非商业化

其余的工具都是商业化的，没有一个为他们的测试工具提供开源解决方案:

*   screener . io([https://screener . io](https://screener.io))
*   应用工具([https://applitools.com](https://applitools.com))
*   Percy ( [https://percy.io](https://percy.io) )
*   梅布尔([https://www.mabl.com/visual-change-detection](https://www.mabl.com/visual-change-detection))

# 总结

那么，当您想要向一群具有不同背景(例如，习惯于在不同平台上工作)的测试人员演示 allowlist-testing 时，您会选择什么呢？你知道我们忘记的其他工具选项吗？