# 揭开 ASP.NET 核心请求管道的神秘面纱:前言

> 原文：<https://dev.to/jimbuck/demystifying-the-asp-net-core-request-pipeline-preface-2mnp>

> 这是 ASP.NET 核心请求管道 3 部分系列的第 0 部分。点击这里查看后续文章的链接！

* * *

[![Demystifying the ASP.NET Core Request Pipeline: Preface](img/b6a5a98ef1576610d81881448dedf2cf.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--HatNh-FW--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://images.unsplash.com/photo-1513828646384-e4d8ec30d2bb%3Fixlib%3Drb-1.2.1%26q%3D80%26fm%3Djpg%26crop%3Dentropy%26cs%3Dtinysrgb%26w%3D1080%26fit%3Dmax%26ixid%3DeyJhcHBfaWQiOjExNzczfQ)

我们每天都在使用我们认为是“黑箱”的产品和服务。以手机为例。大多数人(包括我自己)完全不知道相隔千里的两个人是如何联系在一起的。Scott Hanselman 雄辩地将它描述为**“每一层新的抽象都变得与魔法难以区分”**。如果我们只能使用我们在技术上完全了解的东西，生活将会非常艰难。

但是有些时候**学习这种“魔法”可以提供大量的价值**。当问题出现时，可以简单到更好地理解，这可以节省你的时间和挫折。或者它可以帮助你的逻辑远离潜在的问题场景，**提高性能，避免安全噩梦**。

当与 ASP.NET 一起工作时，很容易开始，只需轻轻掠过架构细节。在这个系列中，我将从开始到结束深入 ASP.NET 核心请求管道。我将涵盖从托管模型和管道基础到构建您自己的定制中间件的所有内容。虽然我将重点关注 ASP.NET 核心 2.2 和更高版本，**同样的一般提示也应该适用于任何基于 OWIN 的应用**，甚至在 ASP.NET mv C5。

我希望你喜欢这个系列，并开始学习新的东西！如果您有任何意见或问题，请在 [Twitter](https://twitter.com/jimbuckio) 上联系我！请关注我以获得下一部分的通知！