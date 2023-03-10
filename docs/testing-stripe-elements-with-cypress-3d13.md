# 用 Cypress 测试条纹元素

> 原文：<https://dev.to/michabahr/testing-stripe-elements-with-cypress-3d13>

在这篇文章中，我们将研究如何测试一个像 Stripe 元素一样使用 iframes 的网站。

我们最近开始使用 [Cypress](https://www.cypress.io/) 作为我们的 E2E 测试框架，并且喜欢它的简单易用。如果你以前没用过[试试看](https://www.cypress.io/)！

虽然 Cypress 在您自己的代码中使用起来很优雅，但是当您在 webapp 中使用第三方组件时，它可能会变得很棘手。在我们的情况下，用户可以用信用卡支付。为了收集支付细节，我们使用 [Stripe 元素](https://stripe.com/docs/web/setup)，它将一个 iframe 加载到我们的网站中。然而 Cypress】还不支持 iframes】。幸运的是 [mightyiam](https://github.com/mightyiam) 找到了一个[变通方法](https://github.com/cypress-io/cypress/issues/136#issuecomment-525994895)来用 Cypress 访问 iframes 及其内容。

**设置**

我们假设您建立了一个 Cypress 项目并运行了示例测试套件。您的项目结构应该如下图所示。

[![Project structure](img/6c8d8f2ca9cf880fc3196036a8883519.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--5CZdZwVn--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2000/1%2A2c0K0Iu7nXsrsEvOiwheww.png)

我们将从扩展 commands.js 文件开始。

将这段代码附加到您的`commands.js`文件中。现在您可以使用`cy.getWithinIframe(‘a selector’)`来定位 iframe 中的任何元素。

如果您需要更大的灵活性，您可以使用左侧所示的`iframeLoaded`和`getInDocument`。你可以在`getInDocument`之后链接很多 Cypress 命令。

**输入条纹元素**

现在我们可以填写信用卡的详细信息了。在您浏览器的[开发工具](https://developers.google.com/web/tools/chrome-devtools/)中，您可以看到由 Stripe 元素加载的 iframe 的结构。

[![Iframe structure of Stripe Elements](img/2945a181eb736ac54e056595fd291443.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--GNa-glIo--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/3300/1%2AuyKds_NEMJzxz3uJqJuE2Q.png)

如您所见，在突出显示的区域，我们可以使用`[name=”cardnumber”]`选择一个输入。有了这些信息，我们可以告诉 Cypress 填写测试信用卡号 4242 4242 4242 4242 以及其他必需的信息。其他输入部分是 exp-date、cvc 和 postal。

将代码集成到您的测试套件中并运行它。您应该看到 Cypress 正在填写信用信息。

[![Cypress fills out credit card details](img/6486f321a8f2e282f92262e59eedf52c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--nzr-D1wJ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://cdn-images-1.medium.com/max/4188/1%2Ad3CSyLxOKn8LH5OxVgVHRw.gif)

就是这样！现在，您可以测试完整的订单流程，而不会遇到任何问题。

**故障排除**

如果你在 Chrome 上运行 Cypress，你可能需要通过在 cypress.json 文件中添加`“chromeWebSecurity”: false`来禁用[网络安全](https://docs.cypress.io/guides/guides/web-security.html#Limitations)。

**未决问题**

我们还没有测试它在多个 iframes 中的表现。