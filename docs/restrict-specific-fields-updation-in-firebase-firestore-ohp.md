# 限制 Firebase Firestore 中特定字段的更新

> 原文：<https://dev.to/chinchang/restrict-specific-fields-updation-in-firebase-firestore-ohp>

假设您有一个`users`集合，该集合中的每个文档都包含用户的公共信息。大概是这样:

```
users/
 user_id_1: {
  name: "John Doe",
  country: "Thailand",
  website: "https://example.com",
  username: "j_deo"
 } 
```

Enter fullscreen mode Exit fullscreen mode

当然，由于这是用户的信息，用户将拥有对其相应文档的编辑权限。这可以通过以下安全规则来保证:

```
match /users/{userId} {
 allow write: if userId == request.auth.uid;
} 
```

Enter fullscreen mode Exit fullscreen mode

一直到现在都很好。这里有一点需要注意:上面文档中的所有字段都应该允许它的用户写吗？想想`username`字段。一个人的用户名通常是一个应用程序中的唯一值。也就是说，如果存在一个用户名为“ramesh”的用户，就不可能(至少不应该)有另一个用户名为“ramesh”的用户。你会问，在 Firestore，你如何确保？嗯，这可能是另一篇博客文章，但总而言之，您将有一个云功能端点来更改用户名，而不是直接点击 Firestore 来更新用户名。在函数内部，您可以使用逻辑来检查是否存在用户名冲突，并采取适当的措施。

这是确保用户名不冲突的一半解决方案。请记住，我们上面提到的针对`users`集合中任何文档的安全规则并不能阻止任何人更新`username`字段。所以现在我们需要确保用户不能在文档中更新他们的用户名。

## 资源&请求

为此，我们需要知道这两个变量:`resource.data`和`request.resource.data`。

### 资源.数据

这很简单。这是您的文档在写之前的当前状态。更准确地说，它是一个带有当前文档键的映射。

### 请求.资源.数据

这个比较棘手。假设有一天你发送一个`update`请求来更新你的文档的`name`字段，比如:

```
db.collection("users/user_id_1")
 .update({ name: "Something else" }); 
```

Enter fullscreen mode Exit fullscreen mode

您可能希望`request.resource.data`是`{ name: 'Something else' }`，这只是我们在请求中发送的数据。但事实并非如此！

`request.resource.data`是文档的未来状态，即写入后的状态。所以在这种情况下，如果写入成功，我们的文档将变成这样:

```
{
 name: "Something else",
 country: "Thailand",
 website: "https://example.com",
 username: "j_deo"
} 
```

Enter fullscreen mode Exit fullscreen mode

这就是`request.resource.data`的真正含义——一个包含所有未来文档关键字的地图。

### 回讫缺失字段

一旦你理解了`resource.data`和`request.resource.data`是什么，你很快就会意识到为什么我们不能简单地检查`username`在我们的`request.resource.data`中应该是`undefined`。因为`request.resource.data`是你送的对象。如果发生写操作，它就是你的文档将成为的对象，并且在那个对象中`username`仍然存在。那我们现在怎么办？

让我们举一个小例子:

当前文档:

```
// resource.data
{
 name: "John Doe",
 username: "j_deo"
} 
```

Enter fullscreen mode Exit fullscreen mode

并且我们像这样发送更新请求:`db.collection('users/user_id_1').update({ name: 'John Doe - the great' })`。这是我们未来文档的样子，也就是我们的`request.resource.data` :

```
// request.resource.data
{
 name: "John Doe - the great",
 username: "j_deo"
} 
```

Enter fullscreen mode Exit fullscreen mode

如果您比较当前和未来的状态，您会注意到当我们不发送`username`字段时，用户名保持不变。这意味着验证用户没有发送`username`字段，我们可以使用下面的安全规则检查`username`在之前和之后的状态是否相同:

```
match /users/{userId} {
 allow write: if userId == request.auth.uid
  && resource.data.username == request.resource.username;
} 
```

Enter fullscreen mode Exit fullscreen mode

完事了吗？不，还有一件小事。可能有这样一种情况，当前文档中不存在`username`字段。在这种情况下，上面的安全规则排除了一个奇怪的错误`Property username is undefined on object`(可能不应该，但它确实发生了)！为了绕过这个错误，我们可以简单地检查字段`username`在未来状态中是否不存在(`request.resource.data`)。因为如果最初没有`username`，我们不更新它，它也不会出现在未来状态。我们是这样做的:

```
match /users/{userId} {
 allow write: if userId == request.auth.uid
  && !('username' in request.resource.data)
  || resource.data.username == request.resource.username;
} 
```

Enter fullscreen mode Exit fullscreen mode

## 奖金重构

您可能有更多这样的需要限制写入的字段。对所有这样的字段重复上述条件会使规则难以阅读。我们可以对函数中的逻辑进行抽象，使其易于管理:

```
function notUpdating(field) {
 return !(field in request.resource.data)
  || resource.data[field] == request.resource.data[field]
}

match /users/{userId} {
 allow write: if userId == request.auth.uid
  && notUpdating('username');
} 
```

Enter fullscreen mode Exit fullscreen mode

整洁，对不对？

这是所有的乡亲。如果你有任何问题或评论，请在评论中回复或在 Twitter 上提问 [@chinchang457](https://twitter.com/chinchang457) 。