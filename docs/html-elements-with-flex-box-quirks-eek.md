# 带有柔性框的 HTML 元素

> 原文：<https://dev.to/khrome83/html-elements-with-flex-box-quirks-eek>

我采访过很多前端开发者。我在候选人筛选过程中使用的方法之一是进行 CSS pair 编程考试。这个考试让我的团队有一个准确的方法来排除那些没有很强的 HTML 和 CSS 知识的候选人。鉴于当前业界的一个趋势是关注框架，我发现缺乏基本的 web 基础知识。对细节的关注也是年轻开发人员的一个问题。

面试的准备很简单——面试的候选人会和面试官进行电话交谈。我们会给他们一个到密码笔的协作链接。在解释了几分钟我们所期待的之后，他们将开始编码。

任务很简单；我们要求他们为一个简单的登录表单创建样式。我们提供了 HTML，他们不能修改它。使用解决方案的图片作为指导，他们将开始。

## 问题

在用这种方法进行的大约 50 次采访中，我们不断看到同样的问题。年轻的开发人员会严重依赖 flex-box，并且无法在一个`fieldset`中垂直对齐内容。即使是有天赋的开发人员也很难确定所有的颜色并理解细节。

事实证明，当涉及到 flex-box 时，浏览器有一些令人讨厌的怪癖。

## 真相

Flex-box 有缺陷。Philip Walton 在他的 GitHub 上有一个很棒的自述文件，它打破了浏览器供应商使用 flex-box 的所有复杂怪癖。虽然 flex-box 在 2009 年问世，但规范在 2011 年才最终确定。多年后，许多错误仍然存在。

### 字段集

在上面的考试中，`fieldset`元素是最大的惊喜挑战。在 Chrome 和 Edge 中，浏览器不会应用`display: flex`属性。这个问题多年来一直困扰着开发人员。几年后，谷歌和微软要么拒绝了这个错误，要么将其标记为“故意的”。

因此避免在`fieldset`上使用柔性盒。除非你用的是 Firefox 或 Safari，否则他们已经修复了这个 bug。

### 按钮

在 Chrome 中，`button`元素不支持`align-items`属性。Safari 不支持`justify-content`。好消息是，你可以将内容放在`div`或`span`中。

### 总结

即使 Safari 修复了`fieldset`，`summary`元素仍然不能是 flex 容器。

## 奖励- CSS 挑战

下面可以看到 CSS 考试的解答。想测试自己的 CSS 技能？派生 CodeSandbox 并移除 CSS。看看不使用 flexbox 能否复制原件。

[https://codesandbox.io/embed/q61ge](https://codesandbox.io/embed/q61ge)

*最初发布于[KH Rome . dev](https://khrome.dev/html-elements-with-flex-box-quirks)T3】*