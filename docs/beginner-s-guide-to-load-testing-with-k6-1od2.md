# k6 负载测试初学者指南

> 原文：<https://dev.to/mostafa/beginner-s-guide-to-load-testing-with-k6-1od2>

让我们从一些问题开始:

*   您是否考虑过如何发现您的基础架构设置是否能够承受高用户负载？
*   您曾经能够在不处理当前测试工具的如此多的配置和陡峭的学习曲线的情况下运行负载测试吗？
*   您是否考虑过通过测试编写脚本的方法，无论是针对负载/性能还是其他方面？
*   你有没有想过负载测试可以被简化，并作为 CI/CD 过程的一部分？

你并不孤单。我记得我是一个团队的首席开发人员，致力于开发一个 API，为我们不断增长的应用程序集提供许多微服务，如内容和用户管理、计费、分析等。那时，我们在源代码控制和 CI/CD 过程中使用软件工程中最新的最佳实践。我们已经使用 docker 和托管 GitLab 建立了 CI/CD 流程，并使用 GitLab CI 处理 CI 流程。经过一段时间的测试和代码推送，我们决定采用 alpha-stage API，它可以满足我们开发新应用的需求。

经过一段时间的开发、测试和改进，我们决定在负载下测试我们的 API，看看它是否能兑现承诺。因为我们已经看到，在 200，000 个用户订阅并且其中 60，000 个用户使用我们的其他应用程序之后，对其他 API 的请求/响应流会变慢，而其他 API 的设置大致相同。

因此，为了不错过在客户到来之前进行(负载)测试的机会，我们决定研究负载测试我们的 API 的方法。在我们调查了 Locust、Apache JMeter 等之后，我们决定使用 Apache JMeter，它曾经是(现在仍然是)这个行业的工具。我们认为这是一个很好的选择。我们花了将近三天的时间才学会使用 GUI 版本执行简单的负载测试。我们能够从一台服务器产生负载测试并分析结果。结果很好，因为它让我们更深入地了解了在并发用户数量和 API 在负载下的响应时间方面，我们应该从 API 中得到什么。

但后来我们没有使用该工具，因为当时没有明确的方法将其集成到我们的 CI/CD 流程中，我们也没有足够的时间来学习和使用它。我们想要一些对开发者更友好，更能融入我们的工作流程，更容易学习的东西。现在，它已经有了很大的改进，并且很容易与 CI/CD 管道集成。

* * *

现在，我在一家公司工作， [Load Impact](https://loadimpact.com/) ，以负载和性能测试为核心业务。他们有一个自由/开源、易于脚本化的工具(JavaScript)，叫做 [k6](https://k6.io/) ，可以用于负载测试。他们还在相同的工具上提供云服务，用于供应和运行来自世界不同地区的测试([云执行](https://loadimpact.com/cloud-execution/))，最终给你一个漂亮的仪表板([洞察](https://loadimpact.com/insights/))，上面有闪亮的图表，显示测试的分析结果。它还与 CI/CD 工具和平台有很好的集成。

[![**Left**: load test script written in JavaScript, **Right**: Cloud service of Load Impact (Insights), **Credits**: [https://loadimpact.com/](https://loadimpact.com/)](img/f8dee5cb593166ddde632b273524740f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--bKefpCs7--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.ibb.co/fkmdMN3/1-Dgp-DE9w-Bwx-VKtm-Tk-BLnz-A.png)

说够了！让我们深入了解什么是负载测试，以及我们如何使用 k6 在我们的基础设施上进行负载测试。我以后也会试着写更多关于云服务的东西。

#### 什么是负载/性能测试？

性能测试是计算机科学实践中性能工程的一个子集，它通过手动或自动对软件施加压力和工作负载来处理软件的质量保证，以观察软件的行为并确保它在该负载下足够响应和稳定。这都是关于期望。例如，您希望您的 API 或网站同时为 10K 用户服务，但是当您对其施加压力时，很快就会发现您的计算与现实不符，事实证明情况正好相反。然后，您应该分析您的设计或设置中的弱点，看看如何从高负载中获益。

在性能测试中，有许多类型的测试满足不同的目的(来源:[https://en.wikipedia.org/wiki/Software_performance_testing](https://en.wikipedia.org/wiki/Software_performance_testing)):

*   **负载测试**:基本上就是给系统加载负载，看看它的表现如何。
*   **压力测试**:负载测试，找出系统可以处理的最大负载量。
*   **浸泡测试**:持续对系统进行负载测试，监控内存泄漏和系统行为。
*   **尖峰测试**:负载突然增加或减少的负载测试。
*   **断点测试**:类似于压力测试，但随着时间的推移，会对系统施加递增的负载，以观察它的表现。
*   **配置测试**:改变配置，查看系统在不同配置下，在负载下的表现。
*   **隔离测试**:隔离故障域，重复测试确认故障。
*   互联网测试:大公司的全球负载测试，以观察不同地区的系统行为。

性能测试最常见的目的是找出系统的可靠性、稳定性、性能和响应能力。像吞吐量和响应时间这样的指标是这种系统的一个很好的度量。

#### k6:编写脚本、运行和监控负载测试

k6 是一个用 Go 编写的免费/开源工具，它可以接收用 JavaScript (ES5.1+)编写的测试，并将其转化为对网站或 API 进行负载测试的请求。

你可以从源代码中编译 k6 或者下载并安装一个官方的二进制发行版。还有[一个官方码头工人形象](https://hub.docker.com/r/loadimpact/k6/)。

不太初学者的方法是构建 k6，显然你应该在你的机器上安装它。为此，首先[下载并安装 Go](https://golang.org/dl/) ，同时在您选择的 shell 上设置`$GOROOT`和`$GOPATH`环境变量，运行这个命令:

```
go get github.com/loadimpact/k6 
```

它会自动下载 k6 并安装到您机器的`$GOPATH/bin`目录中，然后您可以使用以下脚本运行它: