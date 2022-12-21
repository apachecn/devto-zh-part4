# firebase 的安全风暴规则

> 原文：<https://dev.to/jorgealvarez/secure-firestore-rules-for-firebase-52df>

Firestore 规则为我们提供了配置和保护 Firebase 数据库的可能性。在本文中，您将学习如何创建一组易于阅读和维护的规则。

本文中提到的所有代码都可以在[苗条的& Firebase 仓库](https://github.com/jorgegorka/svelte-firebase)中找到，你可以免费下载。

### 目录

*   关于安全的几点思考
*   基本规则
    *   允许/拒绝访问文档
    *   使用函数来提高清晰度
*   高级规则
    *   仅返回文档的子集
    *   允许管理员拥有特殊权限
    *   按当前用户筛选
*   摘要

## 关于安全的一些想法

在 web 应用程序中，我们不能信任客户端。所有在别人电脑上执行的代码都可能被篡改和黑客攻击。

如果我们没有正确配置我们的数据库，任何人都可以从我们的数据库中请求任何数据。

Firestore 规则中的所有检查都发生在 Firebase 服务器中，因此用户没有机会更改它们。

我们唯一可以信任的信息是认证数据。在用户成功登录后，我们的应用程序和 Firebase 数据库之间的所有通信都包含一个带有会话信息的令牌。

这个令牌是唯一有效的信息，不能被用户修改。

令牌为我们提供了保存一些额外信息(用户声明)的可能性，我们可以使用这些信息来改进我们的规则。

让我们来看看这一切是如何运作的:

## 基本规则

这是保护文件的基本结构的一个例子:

```
 match /teams/{teamId} {
    allow read: if isSignedIn();
    allow create: if userAndAdmin();
    allow update, delete: if companyAdmin()
  } 
```

Enter fullscreen mode Exit fullscreen mode

[Firestore 规则](https://firebase.google.com/docs/firestore/security/rules-structure)有基本的*读*和*写*规则。读取规则可以分解为*获取*和*列表*而*写入规则可以分解为*创建*、*更新*和*删除*。*

在前一个例子中，我们为*读取*创建一个规则，为*创建*创建一个规则，为*更新*和*删除*创建一个规则

### 授予/拒绝访问文档

允许访问文件的方式是

```
allow (read/write): if <condition>; 
```

Enter fullscreen mode Exit fullscreen mode

我们只需要定义我们希望允许的操作并添加一个条件。如果条件为真，规则将成功，文档将返回给客户端。如果条件不满足，文档将不会返回给客户端。

如果单个文档有多个规则，那么如果任何一个规则返回 true，那么 Firebase 就会成功。

### 利用函数提高清晰度

帮助您提高清晰度和重用代码的一个好技巧是使用函数来定义您的逻辑，并在规则定义中使用这些函数。

让我们创建我们的第一个规则。我们希望访问者只有登录后才能阅读团队文档的内容。

这就是我们如何创建规则:

```
 match /teams/{teamId} {
    allow read: if isSignedIn();
  } 
```

Enter fullscreen mode Exit fullscreen mode

这是我们创建的函数:

```
 function isSignedIn() {
    return (request.auth.uid != null)
  } 
```

Enter fullscreen mode Exit fullscreen mode

我们正在检查在所有规则中都可用的*请求*对象，以查看是否有 auth uid。如果请求是由登录用户发出的，auth.uid 将返回该用户的用户 id。否则它将是空的。

现在有了这个规则**，只有**登录的用户才能阅读团队文档。

## 高级规则

现在我们知道了如何创建基本规则，让我们添加一些规则来提高数据库的安全性。

### 只返回文档的子集

根据我们到目前为止创建的唯一规则，如果您登录，您可以访问我们数据库中的所有团队。在我们的应用程序中，用户属于一个公司，所以他们只能看到属于他们公司的团队是有意义的。

让我们创建一个函数来检查它。

```
 function userBelongsToCompany() {
    return request.auth.token.companyId == resource.data.companyId
  } 
```

Enter fullscreen mode Exit fullscreen mode

我之前提到过用户声称。这些是我们可以用有用的数据添加到会话令牌中的信息。在我们的例子中，当我们创建一个雇员时，我们添加两条信息:公司的 Id 和角色。查看这段代码，了解如何添加自定义用户声明。

我们正在将*request . auth . token . company id*与 *resource.data.companyId* 进行比较。在 *resource.data* 中，Firestore 让我们可以访问将被退回的每份文件。如果文档的 companyId 与用户的 companyId 不匹配，文档将不会被返回。

现在我们有了 *userBelongsToCompany* 函数，我们可以修改我们的规则来使用它:

```
 match /teams/{teamId} {
    allow read: if isSignedIn() && userBelongsToCompany();
  } 
```

Enter fullscreen mode Exit fullscreen mode

现在，为了阅读文档或文档列表，必须满足两个条件。用户必须登录，并且用户的公司 Id 必须与返回的文档的公司 Id 相匹配。

### 允许管理员的特殊权限

在许多 web 应用程序中，角色是一个非常常见的特性。这就是我们如何将角色应用到我们的规则中:-)。

```
 function userIsAdmin() {
    return request.auth.token.role == 'admin'
  } 
```

Enter fullscreen mode Exit fullscreen mode

我们定义了另一个名为 *role* 的用户自定义声明。现在，我们很容易检查用户是否是管理员。

为了清晰起见，我们添加了另一个函数:

```
 function userAndAdmin() {
    return isSignedIn() && userBelongsToCompany() && userIsAdmin()
  } 
```

Enter fullscreen mode Exit fullscreen mode

现在，如果我们希望只有管理员能够创建新团队，我们添加了这个新规则。

```
 match /teams/{teamId} {
    allow read: if isSignedIn() && userBelongsToCompany();
    allow create: if userAndAdmin();
  } 
```

Enter fullscreen mode Exit fullscreen mode

只有属于我们公司的管理员用户才能创建新团队。普通用户只能阅读它们。

### 按当前用户过滤

如果我们希望普通用户可以编辑他们自己的文档，但不能编辑其他人的文档，而管理员可以编辑任何文档，该怎么办？...拯救规则。

```
 function adminOrOwner() {
    return userBelongsToCompany() && (userAndAdmin() || resource.data.employeeId == request.auth.uid)
  } 
```

Enter fullscreen mode Exit fullscreen mode

我打赌你已经预见到了，对吧？我们检查返回的数据中名为 *employeeId* 的字段，并将其与登录用户的 Id 进行比较。如果它们符合规则，就会成功。如果他们不这样做，如果用户是管理员，它仍然会成功。无论用户是否是管理员，他们必须属于我们公司，所以第一个检查是 *userBelongsToCompany* 功能。

如果我们希望员工(instace)能够编辑他们自己的记录，这就是我们将如何实现该规则。

```
 match /employees/{employeeId} {
    allow update: if adminOrOwner()
  } 
```

Enter fullscreen mode Exit fullscreen mode

## 总结

你需要花时间考虑谁应该有权访问你的 Firestore 数据库。永远不要相信客户端的请求，因为它可能会受到损害。使用 Firestore 规则和会话信息在服务器中进行所有检查。在自定义用户声明和函数的帮助下，保护您的数据库应该非常容易。

如果你想在实际应用中看到这些规则，下载免费的 [Svelte 和 Firebase 模板](https://github.com/jorgegorka/svelte-firebase)。