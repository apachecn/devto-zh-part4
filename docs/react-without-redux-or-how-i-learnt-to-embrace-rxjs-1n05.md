# 没有 Redux 的反应，或者我如何学会拥抱 RxJS

> 原文：<https://dev.to/grahamcox82/react-without-redux-or-how-i-learnt-to-embrace-rxjs-1n05>

每当我启动一个新的 Webapp 时，我几乎都有一套完全相同的库。React 和 Redux 在该列表中名列前茅。

然而，很多时候我发现 Redux store 被用于非常特定于 UI 的一个特定区域的东西，而不是用于更全局的状态。因此，最近我决定尝试一种不同的方法。即上下文 API 和 RxJS。

现在，我几乎还没有开始，但我已经感觉它有潜力了。

我在这里的第一个任务是身份验证。这是一个应用程序，你必须登录才能做任何事情，所以这很重要。为了简化工作，我采用了将电子邮件地址条目与登录/注册表单分开的方法，这样系统就可以检测出你是否已经注册，并显示正确的表单。

这意味着我有以下 React 组件层次结构:

*   应用
    *   主页
    *   LoginRegister
        *   emsilentry
        *   注册
        *   注册

`EmailEntry`组件显示一个简单的表单，要求输入电子邮件地址。当用户提交一个时，它触发一个动作在服务器中查找电子邮件，然后使`LoginRegister`组件适当地呈现`Login`或`Register`组件。简而言之，状态转换是:

*   未定义= >电子邮件条目
*   PENDING => EmailEntry，但是带有加载指示，表明它正在工作
*   存在= >登录
*   未知= >寄存器

所以，这一切都进入 Redux 和它所有的工作。`EmailEntry`组件分派了`checkEmailAddress`动作。这导致 Redux Saga 触发:

*   调度`checkEmailAddress_STARTED`动作
*   进行 API 调用
*   分派负载为`true`或`false`的`checkEmailAddress_SUCCESS`动作
*   调度`checkEmailAddress_FINISHED`动作

然后为`checkEmailAddress_STARTED`和`checkEmailAddress_SUCCESS`动作设置减速器，以适当地更新`emailValue`和`emailStatus`的存储值。

然后，`LoginRegister`组件被设置为对`emailStatus`值做出反应，并进行适当的渲染。

这都是很简单的 Redux。但也是很多代码。几乎所有这些都是特定于这个特定的组件层次结构的。应用程序中没有任何东西关心我们正在检查一个电子邮件地址，这个电子邮件地址是什么或者检查的状态是什么。然而，它在全球商店中随处可见。

所以，我重写了一遍。我完全去掉了 Redux，而是写了以下内容:

*   一个名为`checkEmailService`的简单模块，它只有一个方法- `checkEmail`。这将获取电子邮件地址，并为结果返回一个`Observable`。
*   当`EmailEntry`表格上的表格提交后，我们将:
    *   更新本地状态以显示表单处于挂起状态
    *   用输入的地址调用`checkEmail`方法
    *   订阅返回的`Observable`。当它解析时，我们调用一个由`LoginRegister`提供的回调，其中包含电子邮件地址和 API 调用的结果
*   当`LoginRegister`回调被触发时，我们用提供的电子邮件地址及其状态更新本地状态
*   然后,`LoginRegister`组件使用这个本地状态来决定呈现哪个组件。

这意味着:

*   挂起标志仅对`EmailEntry`组件是本地的
*   电子邮件地址和状态仅对`LoginRegister`组件是本地的
*   根本不存在*全局状态*

感觉已经干净多了。我们已经摆脱了任何全局状态，这是一个巨大的优势(我们都知道全局变量有多糟糕。为什么全球状态更好？)

虽然有时我们确实有对应用程序更重要的值。例如，当前用户可能很重要，或者是经过身份验证的访问令牌。我还没有实现这些，但是我已经想到了两种方法。

对于实际的全局值，我将使用一个`Subject`——特别是一个`BehaviorSubject`——而不是一个`Observable`。服务调用可以在需要时更新这个值，任何东西都可以订阅当前值。访问令牌就是这样一个值——它从`undefined`开始，但是在认证时它将被赋予一个值。任何需要当前值的东西都可以使用`getValue`从`Subject`中获得，或者可以订阅以便在它改变时得到通知。

对于以 UI 为中心的问题，我正在考虑将它与上下文 API 结合起来，并让组件树的适当部分中的一个组件充当提供者并订阅`Subject`。每当`Subject`改变时，该组件更新它的本地值，并将其传递给上下文 API。任何需要它的更低层次的东西都可以从上下文 API 访问它，而不需要知道生成它的 API 调用。这意味着`Subject`只有一个订阅者需要更新，其余的由 React 处理。

所有这些似乎给了我 Redux 的大部分功能，而不需要 Redux 本身。

缺少的是编排。事实上，一个分派的动作可以导致存储的多个位作出反应。这也相对容易实现，只需让服务 API 调用其他服务 API。例如，认证的行为是:

*   向服务器发送电子邮件和密码，并取回访问令牌和用户 ID
*   存储访问令牌
*   将用户 ID 存储为当前用户 ID
*   调用服务器以获取当前用户 ID 的用户详细信息

Redux 允许通过商店的不同部分对相同的动作做出反应来实现这一点。例如:

*   `authenticate_SUCCESS`使访问令牌缩减器存储访问令牌
*   `authenticate_SUCCESS`使当前用户还原器存储用户 ID
*   `authenticate_SUCCESS`导致 Saga 使用给定的用户 ID 分派`getUser`动作
*   `getUser_SUCCESS`使用户资料缩减器存储用户资料

都被一个单一的动作所束缚。这是可行的，但是很难在代码中跟踪它。相反，我计划有一个`authenticationService`,它:

*   调用`accessTokenService`来获取访问令牌
*   调用`currentUserService`来存储用户 ID
*   调用`getUserService`来获取(并缓存)用户详细信息

这提供了可读性很强的编排，并使调试和测试变得非常简单。

行得通吗？我还不知道。

会比 Redux 好吗？我还不知道。

但是我很想看看会怎么样。