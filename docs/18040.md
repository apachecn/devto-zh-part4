# 用谷歌行动跟踪我的咖啡

> 原文：<https://dev.to/hurricaneinteractive/keeping-track-of-my-coffees-with-google-actions-3362>

最近，我开始创建一个应用程序，使用谷歌行动来跟踪不同的生活状态。这将是我第一次使用 Google Actions SDK，这既令人兴奋又令人伤脑筋！

> 代码可以在 [Github](https://github.com/myweekinjs/gactions-life-stats) 上找到

## 目标

目标很简单，创建一个小的行动集，增加我每天生活的简单统计。比如说；嘿谷歌，给**咖啡**加 **1** 。

## 方法#1

[Google 上的动作](https://developers.google.com/actions/)提供了两种与 Google Assistant 互动的方式。这些是；对话流和动作 SDK。我的第一个方法是使用 Actions SDK，因为它最有意义。定义你的行动；*在咖啡中加入{ number }*。找回号码，然后把号码加到我的总数上。

```
// Excerpt
app.intent('COFFEE', (conv, number) => {
  // +number to total
  conv.reply('Coffees added')
}) 
```

Enter fullscreen mode Exit fullscreen mode

然而，在谈到这一点之后，我在这里读到[dialog flow 方法更适合于在与 Google Assistant 的交互过程中定义参数来捕获信息。**接近 2 号我来了！**](https://developers.google.com/actions/reference/nodejsv2/overview#access_parameters_and_contexts)

## 方法二

方法二涉及使用 DialogFlow 来设置*意图*并编写 Firebase 云函数来实现这些*意图*，非常类似于动作 SDK。在用一些训练短语设定了我的*意图*之后；

1.  向咖啡中添加 **1**
2.  我今天喝了 5 杯咖啡
3.  等等

*意图*了解到我想取出每个短语中的数字，并保存到一个`coffees`变量中，我可以在我的云函数中使用它。

```
app.intent('Add Coffee', (conv, { coffees }) => {
  conv.close(`You want to add ${coffees} to your total`)
}) 
```

Enter fullscreen mode Exit fullscreen mode

在将我的云功能重新部署到我的 Firebase 项目后，我能够与我可爱的谷歌助手进行以下对话。

*   **我:**聊聊 AJ 的生活方式
*   **谷歌**:你好！我怎么帮你？
*   我:在咖啡中加入 2
*   谷歌:你想在你的总数上加 2

### 进步！！

下一步是获取请求，并将其保存到 Firebase 实时数据库中，以供以后使用。这比我想象的要容易。第一步是授权我的应用程序，这是通过`firebase-admin`包和通过 Firebase 控制台界面生成 SDK 密钥来完成的。在这之后，我可以轻松地编写一个普通的 push 函数来将数字添加到数据库中。

```
// Authorise code
app.intent('Add Coffee', ({ coffees }) => {
  admin.database().ref('/coffee').push({
    coffee: coffees
  })
  .then(/** Success */)
  .catch(/** Fail */)
}) 
```

Enter fullscreen mode Exit fullscreen mode

一旦成功了，我对`push`对象做了一个小小的修改，为数据库中的值添加了更多的“元数据”,当我最终将这些统计数据添加到网站上让每个人都能看到时。第一个变化是将数字存储为一个`int`而不是一个`string`，我还向该对象添加了一个时间戳值，我想用它来按天对我的咖啡消耗量进行排序。

```
admin.database().ref('/coffee').push({
  coffee: parseInt(coffees),
  timestamp: admin.database.ServerValue.TIMESTAMP
}) 
```

Enter fullscreen mode Exit fullscreen mode

我终于让我的谷歌行动发挥作用了！下一步是在网站上显示信息。

> 要查看预览，请访问 myweekinjs 网站上的[统计页面](https://www.myweekinjs.com/stats/)。

## 显示来自火场

为此，我需要建立两个文件。

1.  Firebase 类 [firebase.js](https://github.com/myweekinjs/public-website/blob/master/src/firebase/index.js)
2.  盖茨比静态页面。 [stats.js](https://github.com/myweekinjs/public-website/blob/master/src/pages/stats.js)

### Firebase.js

在了不起的盖茨比社区的帮助下，我设法建立了我的 Firebase 类，可以在整个网站上使用。我不得不做一个小的修改，因为我得到了一个讨厌的*重复应用*错误。我最后只需要检查 firebase 应用程序是否已经初始化。相当容易解决(谷歌搜索后)。

```
if (!firebase.apps.length) {
  firebase.initializeApp({...})
} 
```

Enter fullscreen mode Exit fullscreen mode

至此，我能够导入我的`Firebase`类并从实时数据库中提取数据。万岁！

### Stats.js

统计页面只有一个目标；显示我喝的咖啡总数。React 世界目前最热门的是什么？钩子！这正是我用来创建这个网页。

我需要一些东西来让它工作。一些`state`和一种接受传入的数据库更改并更新`state`的方法。为此，您可以使用 React 提供的`useState`和`useEffect`方法。这是一个**非常**简化版的统计页面组件。

```
// ...imports

const StatsPage = () => {
  const [coffee, setCoffee] = useState(0)

  useEffect(() => {
    database.on('value', (data) => setCoffee(data))
  })

  return (
    <p>{ coffee.total }</p>
  )
} 
```

Enter fullscreen mode Exit fullscreen mode

`useEffect`方法将监听实时数据库的变化，一旦检测到变化，它将更新 coffee `state`,组件将重新呈现以反映新的总数。不幸的是，数据不会经常改变以保证持续的检查，但是如果你决定建立一个类似的项目，使用这种方法并观察神奇的事情发生。

## 我学到了什么

1.  Firebase 云函数比我想象的要简单得多，而且非常强大！肯定会进一步探索这一点。
2.  React Hooks 对于从 Firebase 实时数据库获取数据非常有用。
3.  创建行动和对话流。
4.  我可能喝了太多杯咖啡。

## 一些有用的资源

1.  [创建动作](https://developers.google.com/actions/dialogflow/project-agent)
2.  [使用效果挂钩](https://reactjs.org/docs/hooks-effect.html)
3.  [管理软件开发套件](https://firebase.google.com/docs/database/admin/start)

## 包装完毕

在这之后，我对统计页面做了一些额外的样式更改，使它看起来更好。希望这篇文章对使用 Google Actions 是一个很好的了解。我非常喜欢使用这项技术，让所有的东西都可以互相交流(我认为这是一个双关语)。如果您对代码或过程有任何问题，请通过 [Twitter](https://twitter.com/hurricane_int) 联系我，我很乐意继续讨论这个问题并改进我的代码。

如果你有兴趣看一步一步的教程，教你如何实现同样的事情，让我知道，我会看看我能做什么！

* * *

谢谢你看我的文章，它真的意义重大！❤️，请提供任何反馈或意见，我总是期待改进和有意义的讨论。这篇文章是我的 [#myweekinjs](http://myweekinjs.com) 挑战的一部分，如果你有兴趣了解更多，我还有其他有趣的文章。

### 👋下次见！