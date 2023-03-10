# 理解 Javascript 回调函数

> 原文：<https://dev.to/raddevon/understanding-javascript-callback-functions-9n5>

在本系列的前一篇文章中，我们讨论了声明函数的[。最后解释了箭头函数，它非常适合用作回调函数。现在，我们将深入了解什么是回调函数以及如何使用它们。](https://raddevon.com/articles/declaring-javascript-functions)

## 在特定场景下调用播放

我一直在用一个比喻，一个功能就像足球中的一场比赛，因为它是一系列的步骤，只要有人叫它的名字，它就会被执行，非常像一场比赛。有时候，能够触发戏剧也不错。

我从来没有做过任何运动的教练，但我可以想象给一个值得信任的球员一些余地，如果他们看到一个特定的场景，可以改变比赛。“这场比赛在对抗这种特殊类型的防守体系时非常出色。如果你看到另一个队是这样设置的，那就换成这种打法。”有时候球员能看到教练看不到的东西。对他们来说，知道哪个场景适合哪个剧本是有好处的，这样他们就可以随时改变。

## 更好的比喻

体育比赛的类比对于描述回调函数来说有点牵强，但是我确实喜欢一些类比。这里还有一个更好用的。

你生病了，所以你去看医生。医生诊断你，可以告诉你有感染。他们会用抗生素来治疗。

医生会给你一剂药物，并要求你在每天的特定时间回来服用另一剂药物，还是会给你一张处方，并附有何时服用的说明？除非你有比我好得多的保险，否则永远是后者。

如果医生能给你开药，却没有办法告诉你什么时候该吃药，那该怎么办？你服药的唯一方法是医生在正确的时间打电话给你让你服药。医生知道你需要在早餐后和睡前服药，但不知道这些时间对你来说是什么时候，更不用说它们可能每天都在变化。你是执行这个计划的最佳人选，因为你最了解你自己和你的时间表。

## 回调函数的真实用例

一个**回调函数**是你传递给另一个函数的一组指令，告诉它在以后的某个时间做什么。回调函数经常在函数完成时运行**做它将做的任何事情，或者在某个事件发生时运行**。作为程序员，你不一定知道这些事情什么时候会发生，所以你传递回调函数的函数会为你留意这些。****

 ****让我们把这个说得更具体些。假设您正在编写一个应用程序，其中的视图可以用您的用户名进行定制。您添加了一个文本字段，用户可以在其中输入他们的姓名，还添加了一个按钮，用户输入姓名后可以单击该按钮。当他们点击时，它会把他们的名字添加到页面上。

你无法知道用户什么时候会输入他们的名字，所以你不能只输入代码来获取他们的名字，然后把它插入到页面上你的代码的正确位置。在输入他们的名字之前，他们可以在页面上坐 10 分钟。他们可能永远不会进入 T2。

相反，您将使用一个**事件监听器**。这是一个您调用的函数，它将监视用户点击按钮。当它们这样做时，它将调用你传递给它的任何函数。这个函数就是回调函数。这可能是这样的:

[https://codepen.io/raddevon/embed/qzGgBj/?height=600&default-tab=js,result&embed-version=2](https://codepen.io/raddevon/embed/qzGgBj/?height=600&default-tab=js,result&embed-version=2)

这里的关键从第 4 行开始。

```
document.querySelector('#personalize-button').addEventListener('click', (event) => {
  nameBlank.textContent = nameField.value;
}); 
```

Enter fullscreen mode Exit fullscreen mode

使用`document.querySelector('#personalize-button')`，我选择了标记为“个性化”的按钮元素这是单击发生的地方，所以我将在这里放置事件侦听器。

我去掉了它，添加了事件监听器。`addEventListener`方法将监听的事件作为它的第一个参数。我已经过了``click``因为那是我关心的事件。

`addEventListener`的下一个参数是回调函数。如果我已经[声明了函数](https://raddevon.com/articles/declaring-javascript-functions)，我可以传入声明函数的名字。因为这是我唯一会用到这个函数的地方，所以我不需要声明它。我可以直接把函数作为匿名函数传入。(那只是意味着函数没有名字。)这是我在这里用过的技巧。

结果是，当用户点击“个性化”按钮时，事件监听器调用我们传递给它的回调函数。该函数具有用用户输入的名称替换文本中空白的代码。

回到前面的医学类比，**回调函数就像我们的处方**。事件监听器知道什么时候该吃药(用户点击按钮后)，但是它*没有*需要吃的药(回调函数)。

我已经完成了我作为开发人员最适合做的部分:我已经告诉事件监听器当事件发生时该做什么。事件监听器做*它唯一适合做的事情:它决定调用我给它的回调函数的正确时间。*

## 回调函数的其他用途

回调函数不仅仅用于响应事件。它们被用在许多数组方法中，如 [reduce](https://raddevon.com/articles/how-to-use-javascripts-array-reduce-method/) 和 [map](https://raddevon.com/articles/how-to-use-javascripts-array-map-method/) 。它们也用于响应异步函数。这些函数继续在后台运行，而您的其他代码也在运行。响应这些操作完成的一种方法是传入一个回调函数。

既然您已经知道了函数的常见用法以及如何声明它们，那么让我们通过实现函数来更好地重构一些现有代码。如果你想凑热闹，请在 Dev.to 上跟我来，或者前往 [Rad Devon](https://raddevon.com/) 。在那里，你可以注册，以便在重构文章发布时得到通知，但你也有机会获得一份免费的**🎁30 分钟的辅导课程🎓与我👋**。我们将制定出您需要采取的后续步骤，以继续从💩网络开发人员的工作！

## 本系列文章

1.  [你的应用手册:为什么要使用 Javascript 函数](https://raddevon.com/articles/your-apps-playbook-why-use-functions-in-javascript/)
2.  [声明 Javascript 函数](https://raddevon.com/articles/declaring-javascript-functions/)
3.  理解 Javascript 回调函数
4.  [Marie Kondo 您的带有函数的 Javascript 代码](https://raddevon.com/articles/marie-kondo-your-javascript-code-with-functions/)****