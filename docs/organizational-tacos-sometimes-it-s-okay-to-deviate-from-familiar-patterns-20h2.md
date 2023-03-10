# 组织玉米饼:有时偏离熟悉的模式是可以的

> 原文：<https://dev.to/wolfhoundjesse/organizational-tacos-sometimes-it-s-okay-to-deviate-from-familiar-patterns-20h2>

今天我们讨论了一个应用程序，它有一个非常基本的设计:

```
<TacoContainer>
  {isTacoDetailsActive ?
    <TacoDetails /> :
    <TacoList /> }
</TacoContainer> 
```

Enter fullscreen mode Exit fullscreen mode

## 表示性和容器组件模式

这篇文章不是关于上述模式的，所以如果你已经精通它的许多名字——例如，这里有一篇 2015 年由 Dan Abramov 写的关于 Medium 的文章，同名——你可以跳过这个简短的部分。

在这个应用程序的上下文中，`TacoContainer`是所有动作发生的组件。它获取数据并维护`isTacoDetailsActive`的状态，这个变量我们在这个[三元语句](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Conditional_Operator)中使用，它将决定哪个组件可见，`TacoDetails`或`TacoList`。

其他两个组件只是从`TacoContainer`接收数据并显示，或者*将它们呈现给用户*。这并不是说他们不能管理自己的州，但为了讨论的目的，让我们假设他们除了显示我们即将享用的许多玉米卷或单个玉米卷的完整细节之外什么也不做。如果你去马里兰州的安纳波利斯，我会带你去最正宗的小餐馆，那里有最好的……实际上，让我回到正题。

## 最终，还是由你来决定

我记得当我学习 Angular 时，我花了几个小时在这些细节上。做任何事情都有一种有棱角的方式，我想创造漂亮的、符合计划的代码。

最后，有些事情对我们两个人的团队来说没有意义，所以我们做了一些改变。最终我明白了，最终是我来决定 T1。这并没有保护我免于做出错误的决定，但它确实提供了通过尝试常规之外的事情来学习和成长的自由。

## 当天的问题

我们的讨论是基于这个问题:如果`TacoDetails`和`TacoList`没有共享任何状态，并且如果它们都使用独立的数据调用进行操作，那么将这个逻辑从`TacoContainer`移到它们各自的组件中有什么问题吗？

我的回答:*“最终还是要由你来决定。*

诱惑是一件有趣的事情。有些人尽可能快地奔向它，这取决于它是什么，并且，在获得一点经验之后，其他人会认识到诱惑是通向考虑的大门。当然，这取决于具体情况。如果我想重构我的代码，我会停下来考虑对我之后的开发人员来说会是什么样。我从来不会停下来考虑我是否应该吃玉米卷。🤷‍♂️🌮🌮🌮