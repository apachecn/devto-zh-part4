# React Hooks 迷你速成班

> 原文：<https://dev.to/chrisachard/react-hooks-mini-crash-course-4gcb>

*这最初是作为 Twitter 帖子发布的:[https://twitter.com/chrisachard/status/1167132279333957632](https://twitter.com/chrisachard/status/1167132279333957632)T3】*

想学勾手，但是一直忙不过来？⏲

🔥这是专为你准备的迷你速成班！🔥

(结尾的代码链接)

# 1。

通过调用`useState`为函数组件添加状态，并传入初始值。

[![useState in function components](img/55697515bca40dfa7b9ee8651b68bf42.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--lXB5c0cs--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/w6vwtg7h4tua9zjx687q.png)

# 2。

`useState`返回数组中的两个值:

1.  状态的当前值
2.  更新状态的函数

[![useState](img/d17eae40e10a798394aeb7dcae4ec19b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--rugPGjps--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/g05abs40n300ngfpqh5c.png)

# 3。

在函数的顶层调用钩子，而不是在 if 语句或循环中。

这是 React 在内部跟踪钩子值所必需的。

[![call hooks at the top level](img/4c746567fd5fbe09e239eb5da3b381c5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--WehiX05M--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/zylvcq2x83hgqggjis6q.png)

# 4。

在`useEffect`钩子中执行异步动作和有副作用的动作

这样，异步动作仍然可以跨多个渲染工作

[![useEffect](img/10d89f727ed7c5f292d4212668a8104e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--_z_woEBf--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/huupjc0r02bas71zhwox.png)

# 5。

`useEffect`将依赖关系数组作为第二个参数

这很重要！跳过依赖列表会导致无限循环，或者代码在您认为应该运行的时候没有运行

[![useEffect dependencies](img/4dd8b19d40b9db40564f4b40ff5ce8e2.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--cm9UxyA---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/froilphkb07oinpb4btz.png)

# 6。

编写自定义钩子作为以单词`use`开始的函数

然后使用任何你想要的内置挂钩

返回(或不返回)值和函数

[![Custom Hooks](img/672bee17f542250f91203487fe30621e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--b1O5HRUp--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/9gfvypsrfyw001pfjxg0.png)

# 7。

还有许多其他内置挂钩，但它们都遵循类似的模式

在这里获得完整的名单:[https://reactjs.org/docs/hooks-reference.html](https://reactjs.org/docs/hooks-reference.html)

# 8。

就是这样！现在，您可以向函数组件添加状态和长期运行的效果。

类组件不是死的，但是钩子确实有助于清理一些组件逻辑。

# 9。

以下是您可以尝试的代码链接！

**使用状态**

[https://codesandbox.io/embed/react-hooks-crashcourse-usestate-qcjo8](https://codesandbox.io/embed/react-hooks-crashcourse-usestate-qcjo8)

**使用效果**

[https://codesandbox.io/embed/react-hooks-crashcourse-useeffect-e7iwe](https://codesandbox.io/embed/react-hooks-crashcourse-useeffect-e7iwe)

**自定义挂钩**

[https://codesandbox.io/embed/react-hooks-crashcourse-custom-hook-ihfzg](https://codesandbox.io/embed/react-hooks-crashcourse-custom-hook-ihfzg)

**喜欢这个帖子？**
你可以通过:

*   在推特上关注我: [@chrisachard](https://twitter.com/chrisachard)
*   加盟快讯:[chrisachard.com](https://chrisachard.com/)

感谢阅读！