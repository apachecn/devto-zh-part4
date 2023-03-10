# 冲刺图:烧掉还是烧掉？

> 原文：<https://dev.to/nicolus/sprint-charts-burn-down-or-burn-up-37e>

所以我的团队已经使用 scrum(或者至少一些受 Scrum 启发的内部方法)工作了几个月，我意识到我对 gitlab(或者大多数其他工具)提供给我们的燃尽图并不完全满意。我觉得他们并不总是讲述整个故事。

我发现 burn *up* 图表更有趣，但我没看到多少人使用它们。所以也许只是我？或者也许我们没有按照我们应该的方式计划？我来解释一下我的观点，在评论里说说你的看法！

## 向“理想”冲刺

<figure>

[![](img/f23644c6cce83fdd946665951f832b95.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--82MysaYA--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/bd1trbyfxk5l0s9af03y.png)

<figcaption>An ideal sprint : burn down chart on the left, burn up on the right</figcaption>

</figure>

让我们考虑一个非常完美的冲刺。我们确定了我们的团队速度，并估计作为一个团队，我们一周可以处理大约 100 个点。然后在 sprint 规划期间，我们评估了一些任务，并在我们的 sprint 中放入了 100 点的任务。在这个例子中，它工作得非常好，我们能够以非常稳定的速度按时完成所有任务，耶！

在这里，两个图表实际上并没有太大的不同:左边的燃尽图从左上角的 100 点开始，红线代表剩余要完成的任务数(或者更确切地说是点数)，目标是通过尽可能接近灰色虚线来降低燃尽图。燃尽图的工作方式正相反:顶部的蓝线代表要完成的点数，目标是完成所有点数，并使红线上升。

在这种理想的情况下，燃尽图并没有真正的优势，但老实说，我还没有在我们的团队中看到这样的图表，所以让我们转向其他情况

## “我们可以只添加这个微小的任务吗？”冲刺

<figure>

[![](img/e733567bda9779d66ab836c00f9fb3ec.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--uUfkwYdg--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/bgcbd1al80dy4gz6pepd.png)

<figcaption>A sprint where we added a task : burndown chart on the left, burn up on the right</figcaption>

</figure>

这就是事情开始变糟的时候。在我们的每一次冲刺中，总会有一些我们没有预料到的事情，我们不得不面对。因此，我们自然会在 sprint 中添加一项任务，并尽我们所能处理好它。有时行得通，有时行不通。

在这种情况下，正如我们在图表上看到的那样，它并没有完全发挥作用。但有趣的是，通过查看传统的燃尽图，我们不知道发生了什么。只是看起来由于某种原因，团队在冲刺的最后阶段效率降低了(如果我们遇到了障碍，这确实会发生)。燃尽图在这里更有趣，因为我们清楚地看到团队处理了计划的工作量，但我们错过了我们的目标，因为我们不得不添加。

## “也许我们不需要那个”冲刺

<figure>

[![](img/0db68ae95ae7010a802b1302d4502d4c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--lxJ4_djv--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/tsc9uxawrmqnsf1rdyqw.png)

<figcaption>A sprint where we removed a tasks : burndown chart on the left, burn up on the right</figcaption>

</figure>

这与前面的例子相反:在冲刺的某个时刻，一些东西被丢弃了。也许我们不得不等待团队外部的东西，并决定将其转移到未来的 sprint 中，或者有人改变了对某个特性的想法，或者也许我们只是意识到我们在那个 sprint 中迟到了，并决定欺骗一下并删除不太重要的任务...

无论如何，结果是，有了燃尽图，一切看起来都很好:我们完成了 100 分！但是消耗图表再次显示了整个故事，我们清楚地看到我们只做了 80 分的任务。

## 为什么有关系？

正如我们从例子中看到的，消耗图表只有在冲刺阶段添加或删除任务时才会有所不同，所以如果这种情况从未在你的团队中发生，也不会有太大的不同。如果这种事情确实发生了，它可以让你更好地了解实际发生了什么。

这里的目标不是在没有达到预期时责怪某人，而是为了能够更好地调整我们的速度以进行下一次冲刺，并提高团队的效率。我觉得如果我们能更好地想象出哪里出了错(或对)，我们就能更好地计划接下来的冲刺。

你觉得怎么样？燃尽图可以吗，还是使用燃尽图更好？或者你有一个完全不同的评估 sprint 的方法吗？