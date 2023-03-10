# 我:“我已经测试过了，而且有效，为什么我还要写测试？”

> 原文：<https://dev.to/briwa/me-i-tested-and-it-works-why-do-i-have-to-write-tests-4292>

我在大学学习的时候就开始用 PHP 和 Javascript 编程了。当时，我对任何种类的软件测试都一无所知。确保代码工作的唯一方法是在浏览器中手动测试它们。我可以想象，拥有更复杂软件的大公司会有十几个人在他们的软件界面前排队，手动测试它们。

我记得甚至有一个电子表格，里面有一排排的用例，在启动前有复选框可以勾选。即使这样，错误还是出现了。当我发布代码时，我对我知道的几种情况进行了手动测试，但我无法测试应用程序允许的所有可能情况。这是我发展生涯中最适得其反的时刻之一。

然后我发现了[软件测试](https://en.wikipedia.org/wiki/Software_testing#Testing_levels)。这真是一种福气。它系统地覆盖用例的方式，保持应用程序的稳定性，以及它如何通过持续集成自动完成，真的让我惊讶。不再有数百万行的电子表格，我可以更有信心地发布代码。

* * *

在我看来，测试的概念对一些人来说仍然很难接受，即使是现在。我可以理解这多半是害怕改变；试图让人们走出“我编码了它，它工作了”的舒适区并不是一件容易的事情。我记得一些反对测试的观点:

*   "我对我的代码有信心，它不会崩溃."
*   “我测试了所有用例，它们都很好。”
*   “你可以检查我的代码，指出缺陷，如果没有，那就好了。”

所有的争论都来自开发人员的“人”的因素:他们的代码信心，他们的手工测试技能，他们完美的代码。这很重要。没有什么可以从中吸取的。另一方面，软件测试涵盖了“人”的因素失效的情况。没有人是完美的，错误是必然发生的，我们从经验中学习。当你写代码时，或者甚至当人们审查代码时，一些用例/缺陷被忽略了，测试可能会指出来。

而不仅仅是维护代码的稳定性和质量。测试定义了代码的规格。我只是一时糊涂，但是我突然想到为什么测试首先被称为“规范”。当编写测试时，从一开始就很清楚代码能做什么和不能做什么。最近，我甚至开始养成浏览一个库的测试文件夹的习惯，当我在他们的 API/demo 页面中找不到它们时，看看它做了什么，这确实很有帮助。

* * *

即使所有关于测试的好东西都摆出来了，让人们参与进来肯定是另一个问题。我可以看到人们仍然将测试视为“允许我合并 pr 的东西”。有些人可能仍然会说“这个特性是有效的，我已经测试过了，我们可以先合并它，我稍后再写测试”。其他人会抱怨“许多测试失败，因为我改变了一行代码”。

在我看来，他们忽略了大局。如果您对代码进行了更改，当然您必须通过所有的测试，因为您要确保您的代码仍然在代码(测试)的规范之内。如果你添加一个新的代码，你必须添加测试，因为你在给软件添加新的规范。如果你发布了一个没有测试的代码，那就意味着你发布了一个没有规范的代码；没有什么是一成不变的，包括臭虫。

大多数开源软件仓库非常认真地对待测试是有原因的:因为他们非常关心代码的稳定性。一个小小的错误就可能让很多人处于危险之中，包括开发者和用户。失败的构建或者没有测试的 PRs 甚至不会被评审。我想说，与我们自己的相比，它不应该有太大的不同(也许是盈利性的？)项目。可能很难让客户/涉众相信测试是重要的，但是至少不应该让开发人员觉得这么难。

* * *

这篇文章可能对你不适用。我知道有很多实践将测试整合到他们的工作流程中(例如[敏捷](https://www.thoughtworks.com/agile-project-management))，并且很多公司已经在这么做了。但是，如果它确实适用于您，我希望这篇文章提供了关于什么是测试以及它为什么重要的另一个观点，也许您最终可以摆脱那个巨大的充满复选框的电子表格。

一如既往，感谢阅读我的文章！

* * *

格伦·卡斯滕斯-彼得斯在 [Unsplash](https://unsplash.com/@glenncarstenspeters?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的封面图片。