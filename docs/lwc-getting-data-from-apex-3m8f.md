# LWC-从 Apex 获取数据

> 原文：<https://dev.to/brettmn/lwc-getting-data-from-apex-3m8f>

[https://www.youtube.com/embed/-_Ru-A6455A](https://www.youtube.com/embed/-_Ru-A6455A)

嗨，我是布雷特和 WIPDeveloper.com。上次我们学习了使用网络服务获取数据，这样我们就可以从 Salesforce 获取信息，而无需编写任何服务器端代码或 Apex。这一次，我们将通过调用 Apex 类并让它以我们想要的格式返回数据来了解从 Salesforce 检索数据。

首先，我复制了我们在使用有线服务结束时拥有的组件。它看起来是一样的，因为它现在完全一样。我们将从改变两者之间唯一的 HTML 变化开始。我们将从使用电汇服务改为使用 Apex。

我们将保存部署，确保页面上有两个不同的组件。

看起来已经完成了。

所以我们开始了，底部的一个正在使用 Apex，或者它将是。

所以我们不需要对 HTML 做任何其他的改变。所以我们会关闭它。在我们开始使用 JavaScript 文件之前，让我们看一下我们将要使用的类，我们将使用一个名为 firstComponentController 的类。

它声明了一个名为`init`的静态方法，并启用了 aura，使用了 cachable equals true。

它将返回这个映射，它是一个字符串和对象，我们查询提供的用户 ID。或者我们查询从用户信息中获得的用户 ID 来获得用户 ID。如果我们找到了用户，我们将它作为用户添加到返回对象中，然后我们将成功设置为 true，并返回用户对象。这就是顶点，非常简单。在您自己的代码中，您可能需要更多。

现在，要在 lightning web 组件中访问它，我们将去掉`getRecord`,因为我们不会使用我们将使用的，

我们将进口

`init`实际上来自 Salesforce Apex

第一个组件初始化。

就在那里，你看到我使用了 Visual Studio 代码插件的自动完成功能。这样我就不用把所有的东西都打出来了，因为有人替我做了一些工作。我们使用`init`而不是 he `getrecord`处理程序。

我们不需要，我们实际上要摆脱这一切。

我们会把它传给我们的电报员。然后我们会有那个修饰方法。我们将称之为`handleInit`。

我们会让它解构结果，这样我们就可以得到错误

还有数据。

呜呜，没有被打出数据。

在这个函数中，我们将

我们想做什么？我喜欢补充一点。既然我们在玩它，我将在这里添加一个窗口控制台日志。

因此，当我们在调试语句中查看它时，我们可以知道它在哪里。

我要传入空气和数据，这样它们就被记录下来了。

现在，我们应该做的第一件事是检查是否有错误。

所以我们将使用

如果哪个错误，哪个 JavaScript 是。

如果那个物体存在，它会表现出来。它会表现得像一个真的语句，进入这段代码。我们将把它记录到控制台。

如果我会拼写会有帮助。

然后我们会的。

我们要做 else/if。

否则，如果有数据，我们将设置数据。

我们还没有用户。我们可以去掉用户标识，但是

this.user

数据点用户

如果这是不对的，我们该怎么办？

让我们补充一点最后声明，

只是让我们知道我们得到了错误的代码部分。

在这里做，否则就拼错了。

我们走吧。因此，现在当服务调用 Apex 代码中的`init`时，处理程序将

在方法开始时记录错误消息和数据对象。然后我们将检查是否有错误消息，因为很有可能，这将在控制台中以 null 结束。但是它会记录，浏览器控制台窗口出错。如果没有出错，并且有数据，我们将 data dot 用户设置到这个类中。让我们现在就去掉用户 id。

我们在这个类中设置用户，这样我们就可以在模板中访问它。然后我们，如果没有错误，没有数据，开始想知道发生了什么。最有可能的是，这永远不会发生。

有一点不同

我们实际上并不需要所有这些检查，因为我们没有名为 value 的字段的用户数据。我们想做的只是检查

我们现在要做的是检查

这个用户存在吗？如果我们返回用户名，否则，我们返回一个空字符串

我们这样做是为了

我们只是检查这一点，这样我们就不会在用户填充之前意外地尝试使用名称，因为这样可能会引发错误，如果我们不这样做的话会更容易。现在让我们看看我们有没有。

将此部署到 Salesforce。

看看能不能找到一个看起来不像名字的名字。我们做错了什么？

哦，让我们想想我们需要跟踪这个。所以用户身上的跟踪装饰器。这意味着我们必须进口它。

所以我们从 LWC 进口。我们装饰用户，并部署它

先看看这个能不能加载。

在这里，我们有我们的用户名，我们没有其余的值，因为现在我们仍然在检查所有其他用户的用户数据字段。

我将从名称中复制并粘贴 return 语句，这样它就是

不，让我一遍又一遍地复制粘贴，而不是每次都打出来。

现在，这意味着他们都将被命名，但我们不想这样

换一个邮箱。如果我能正确拼写电子邮件和公司名称

你猜到了那家公司的名字。也不要为了悬念而坚持。但是 phone 将返回用户手机。

部署它，返回我们的页面并刷新它。

给你。我们在顶部有我们公司的名字。我们有用户名和假电话号码。

WIPdeveloper.com 是公司名称，也是用户的电子邮件地址。因此，我们使用相同的模板从 Apex 控制器返回相同的值，就像我们在屏幕上看到的一样，只是更新了我们用于网络服务的标签。所以这很巧妙。当然，我们不再使用 Salesforce 的内置功能。因此，我们必须负责维护我们对如何处理该逻辑所做的任何更改。

## 现在就这样。

记得报名参加 **[每周一次的单口相声！你可以得到我们关于 WIPDeveloper.com 的任何最新信息。](https://wipdeveloper.com/newsletter/)**

帖子[LWC——从 Apex](https://wipdeveloper.com/lwc-getting-data-from-apex/) 获取数据最早出现在[WIPDeveloper.com](https://wipdeveloper.com)上。