# 不健康的代码:原始过度使用

> 原文：<https://dev.to/jamesmh/unhealthy-code-primitive-overuse-7mh>

经典的“代码气味”之一叫做原始过度使用。

这看似简单。

* * *

注意:这是我的书[重构类型脚本:保持你的代码健康的摘录。](https://leanpub.com/refactoringtypescript)T3】

 [![Refactoring TypeScript book](img/916f4c11822f9685814d2db721846004.png)
T4】](https://leanpub.com/refactoringtypescript)

* * *

# 鉴定原始过度使用

以这段代码为例:

```
const email: string = user.email;

if(email !== null && email !== "") {
    // Do something with the email.
} 
```

Enter fullscreen mode Exit fullscreen mode

注意，我们正在处理电子邮件的原始数据？

或者，考虑一下这个:

```
const firstname = user.firstname || "";
const lastname = user.lastname || "";
const fullName: string = firstname + "  " + lastname; 
```

Enter fullscreen mode Exit fullscreen mode

注意所有的额外检查，以确保用户的名字不是`null`？毫无疑问，您见过这样的代码。

## 这里怎么了？

这个代码有什么问题？有几件事需要考虑:

*   这种逻辑是不可共享的，因此会到处重复

*   在更复杂的场景中，很难看出底层的业务概念代表了什么(这会导致代码难以理解)

*   如果有一个潜在的商业概念，它是隐性的，而不是显性的

## 偶然的商业概念

上面代码示例中的业务概念类似于用户的*显示名*或*全名*。

然而，那个概念只是暂时存在于一个恰好被正确命名的变量*中。*在其他地方会被冠以同样的名称吗？如果你的团队中有其他开发人员- **可能不是**。

从业务角度来看，我们的代码可能难以掌握，在复杂的场景中难以理解，并且不能在应用程序的其他地方共享。

我们该如何处理这件事？

# 欺骗性的布尔

原始类型应该是我们在代码中创建更有用的面向业务的概念/抽象的构建块。

这有助于每个特定的业务概念在一个地方拥有其所有的逻辑(这意味着我们可以更容易地共享它和推理它)，实现更健壮的错误处理，减少错误，等等。

我想看看我经历过的原始过度使用的最常见原因。我总是看到它。

## 场景

假设我们正在开发一个 web 应用程序，帮助客户在线出售他们用过的物品。

我们被要求在系统的用户认证部分添加一些额外的规则。

现在，系统只检查用户是否成功通过了身份验证。

```
const isAuthenticated: boolean = await userIsAuthenticated(username, password);

if(isAuthenticated) {
    redirectToUserDashboard();
} else {
    returnErrorOnLoginPage("Credentials are not valid.");
} 
```

Enter fullscreen mode Exit fullscreen mode

## 新增业务规则

我们公司现在希望我们检查用户是否活跃。非活动用户将无法登录。

很多开发者都会做这样的事情:

```
const user: User = await userIsAuthenticated(username, password);
const isAuthenticated: boolean = user !== null;

if(isAuthenticated) {
    if(user.isActive) {
        redirectToUserDashboard();
    } else {
        returnErrorOnLoginPage("User is not active.");
    }
} else {
    returnErrorOnLoginPage("Credentials are not valid.");
} 
```

Enter fullscreen mode Exit fullscreen mode

哦，不。我们已经引入了代码气味，我们知道这将导致可维护性问题！

我们现在已经有了一些空检查和嵌套条件(这些都是不健康代码的迹象，在[重构类型脚本](https://leanpub.com/refactoringtypescript)一书中有所论述。)

所以，让我们首先通过应用(a)特例模式和(b)保护子句来重构它(这两种技术在[这本书](https://leanpub.com/refactoringtypescript)中也有详细解释。)

```
// This will now always return a User, but it may be a special case type
// of User that will return false for "user.isAuthenticated()", etc.
const user: User = await userIsAuthenticated(username, password);

// We've created guard clauses here.
if(!user.isAuthenticated()) {
    returnErrorOnLoginPage("Credentials are not valid.");
}

if(!user.isActive()) {
    returnErrorOnLoginPage("User is not active.");
}

redirectToUserDashboard(); 
```

Enter fullscreen mode Exit fullscreen mode

好多了。

## 更规则...

既然您的经理已经看到了您添加新业务规则的速度，他们还需要一些新规则。

1.  如果用户的会话已经存在，则将用户发送到一个特殊的主页。

2.  如果用户由于登录尝试次数过多而锁定了帐户，请将他们发送到一个特殊页面。

3.  如果这是用户的第一次登录，那么将他们发送到一个特殊的欢迎页面。

哎呀！

> 如果你已经在这个行业呆了几年，那么你就知道这种情况有多普遍！

乍一看，我们可能会做一些天真的事情:

```
// This will now always return a User, but it may be a special case type
// of User that will return false for "user.isAuthenticated()", etc.
const user: User = await userIsAuthenticated(username, password);

// We've created guard clauses here.
if(!user.isAuthenticated()) {
    returnErrorOnLoginPage("Credentials are not valid.");
}

if(!user.isActive()) {
    returnErrorOnLoginPage("User is not active.");
}

if(user.alreadyHadSession()) {
    redirectToHomePage();
}

if(user.isLockedOut()) {
    redirectToUserLockedOutPage();
}

if(user.isFirstLogin()) {
    redirectToWelcomePage();
}

redirectToUserDashboard(); 
```

Enter fullscreen mode Exit fullscreen mode

注意，因为我们引入了保护子句，所以在这里添加新的逻辑要容易得多？这是使你的代码高质量的一个令人敬畏的好处——它导致未来的变化更容易改变和添加新的逻辑。

但是，在这种情况下，有一个问题。你能发现它吗？

**我们的`User`类正在成为我们所有认证逻辑的垃圾场。**

## 真的有那么糟糕吗？

有那么糟糕吗？是的。

想一想:你的 app 里还有哪些地方会需要这些数据？哪里都没有——都是认证逻辑。

一个重构是创建一个名为`AuthenticatedUser`的新类，并只将与认证相关的逻辑放入该类中。

这将遵循单一责任原则。

但是，对于这个特定的场景，我们可以做一个简单得多的修复。

## 只用枚举

每当我看到这种模式(一个方法的结果是一个布尔值或者是一个具有立即被检查/测试的布尔值的对象)，用一个枚举替换布尔值是一个好得多的实践。

从上面的最后一段代码中，让我们将方法`userIsAuthenticated`改为更准确地描述我们要做的事情的方法:`tryAuthenticateUser`。

并且，不是返回一个`boolean`或者一个`User`——我们将发送回一个 enum，告诉我们确切的结果是什么(因为这是我们唯一感兴趣知道的)。

```
enum AuthenticationResult {
    InvalidCredentials,
    UserIsNotActive,
    HasExistingSession,
    IsLockedOut,
    IsFirstLogin,
    Successful
} 
```

Enter fullscreen mode Exit fullscreen mode

我们的新 enum 将指定对用户进行身份验证的所有可能结果。

接下来，我们将使用那个枚举:

```
const result: AuthenticationResult = await tryAuthenticateUser(username, password);

if(result === AuthenticationResult.InvalidCredentials) {
    returnErrorOnLoginPage("Credentials are not valid.");
}

if(result === AuthenticationResult.UserIsNotActive) {
    returnErrorOnLoginPage("User is not active.");
}

if(result === AuthenticationResult.HasExistingSession) {
    redirectToHomePage();
}

if(result === AuthenticationResult.IsLockedOut) {
    redirectToUserLockedOutPage();
}

if(result === AuthenticationResult.IsFirstLogin) {
    redirectToWelcomePage();
}

if(result === AuthenticationResult.Successful) {
    redirectToUserDashboard();
} 
```

Enter fullscreen mode Exit fullscreen mode

注意到这有多易读了吗？而且，我们不会再用一堆不必要的额外数据来污染我们的`User`类！

我们将返回一个值。这是简化代码的好方法。

这是我最喜欢的重构之一！我希望你也会觉得有用。

## 奖励:策略模式

每当我使用这种重构，我自动知道策略模式可能会帮助我们更多。

想象一下上面的代码有*多*条业务规则和路径。

我们可以通过使用策略模式的一种形式来进一步简化它:

```
const strategies: any = [];

strategies[AuthenticationResult.InvalidCredentials] = 
    () => returnErrorOnLoginPage("Credentials are not valid.");
strategies[AuthenticationResult.UserIsNotActive] = 
    () => returnErrorOnLoginPage("User is not active.");
strategies[AuthenticationResult.HasExistingSession] = 
    () => redirectToHomePage();
strategies[AuthenticationResult.IsLockedOut] = 
    () => redirectToUserLockedOutPage();
strategies[AuthenticationResult.IsFirstLogin] = 
    () => redirectToWelcomePage();
strategies[AuthenticationResult.Successful] = 
    () => redirectToUserDashboard();

strategies[result](); 
```

Enter fullscreen mode Exit fullscreen mode

# 如何保持你的代码健康

这篇文章摘自 [Refactoring TypeScript](https://leanpub.com/refactoringtypescript) ，它被设计成一个可接近的实用工具，帮助开发人员更好地构建高质量的软件。

[![Refactoring TypeScript book](img/916f4c11822f9685814d2db721846004.png)
T3】](https://leanpub.com/refactoringtypescript)

# 保持联系

不要忘记通过以下方式与我联系:

*   [推特](https://twitter.com/jamesmh_dev)
*   [LinkedIn](https://www.linkedin.com/in/jamesmhickey/)

# 浏览您的软件开发职业简讯

一封电子邮件简讯，将帮助您提升软件开发人员的职业水平！有没有想过:

✔:软件开发人员的一般阶段是什么？✔:我怎么知道自己处于哪个阶段？我如何进入下一阶段？
✔什么是技术领导者，我如何成为一名技术领导者？✔，有人愿意和我一起走走，回答我的问题吗？

听起来有趣吗？加入社区吧！