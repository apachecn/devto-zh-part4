# 我在 React 中构建表单的心得:第 1 部分

> 原文：<https://dev.to/vbaknation/my-learnings-from-building-forms-in-react-part-1-3n1k>

在过去的一个月左右的时间里，我花了大量的时间来构建、思考、排查和搜索 React 如何处理表单。这篇文章，可能还会有更多文章，是一个让我大声思考我所学到的东西的地方！

> 作为一条规则，我的任何帖子都不意味着被解释为最佳实践或教程。它们应该被理解为沉思和漫谈。如果遇到效率低或不正确的事情，帮我一个忙，把它说出来，这样我可以纠正我的理解。

我一直在做一个志愿者项目，有以下要求:

*   用户应该能够创建一个帐户，并提交不同字段类型的表单

*   管理员应该能够查看注册用户的表单数据，并在仪表板上更新他们的帐户状态

# 注册功能

用户遵循一个简单的(对他们来说)流程来创建帐户并提交表单:

*   注册
*   编辑表单
*   预览表单
*   提交表单

# 编辑或预览

从编辑页面上的表单输入字段接收的数据可能与预览页面上呈现的数据不同。

例如，用户在编辑页面上的三个单独的字段中输入他们的名、中间名和姓，但是在预览页面上呈现单个姓名字符串。

用于说明的代码笔(vanilla JS):

[https://codepen.io/vishalbakshi/embed/gVYOwE?height=600&default-tab=result&embed-version=2](https://codepen.io/vishalbakshi/embed/gVYOwE?height=600&default-tab=result&embed-version=2)

用户将数据作为单个字符串(`first_name`、`middle_name`、`last_name`)输入到单独的字段中，预览元素以不同的方式显示该数据(`first_name`+“+`middle_name`+“+`last_name`)。

# 刷新

如果用户错误地刷新了编辑页面(众所周知，在使用移动应用程序时，我会不小心刷新页面)，他们会讨厌看到表单数据被删除。

出于开发目的，并且为了快速观察和测试`state`和编辑/预览页面表单元素之间的关系而不引入数据库，我使用`localStorage`方法来保持应用程序的`state`更新和保存。

这里有一个示例代码来说明(使用 React 钩子):

[https://codepen.io/vishalbakshi/embed/KOKKmw?height=600&default-tab=result&embed-version=2](https://codepen.io/vishalbakshi/embed/KOKKmw?height=600&default-tab=result&embed-version=2)

下面的视频展示了本地存储如何根据用户输入进行更新:

[https://www.youtube.com/embed/3fMh8Ex6FrU](https://www.youtube.com/embed/3fMh8Ex6FrU)

请注意，我第一次提供输入时，相关的键被添加到本地存储对象中，随后的更新会更新该值。下面的屏幕截图显示了在点击左侧复选框之后，在更新`state`之前`state`的值。请注意，第一次单击后,`left-handed`属性消失了。我用`debugger`在那一行暂停 chrome。

[![](img/1a10eafbd0c52c3b205f8eb10a7f22e7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--8WnMUKh_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/n6utrjlb5l7zqj9kstnl.png)

# 动态表单元素

这是一个看似无害的*添加联系人*按钮，但我花了几个小时实现了第一次未经测试的尝试。

这里有一个示例代码来说明(使用 React 钩子):

[https://codepen.io/vishalbakshi/embed/ZgEQVj?height=600&default-tab=result&embed-version=2](https://codepen.io/vishalbakshi/embed/ZgEQVj?height=600&default-tab=result&embed-version=2)

热辣迪吉！点击按钮后，新的组件被渲染，并且`contactInfo`对象相应地更新。

# 展望未来

我希望明天能添加 Firebase 的功能，并期待尽快写另一篇关于我的心得的文章。

感谢阅读！

## 跟着我

[YouTube](https://www.youtube.com/channel/UCqvA0CVB3QR3TLpGVzkD35g)
T3】insta gramT5[Twitter](https://twitter.com/vbaknation)