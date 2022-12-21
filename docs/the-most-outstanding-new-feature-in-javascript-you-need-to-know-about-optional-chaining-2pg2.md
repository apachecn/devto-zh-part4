# 😲🤯您需要了解的 Javascript 中最突出的新特性:可选链接

> 原文：<https://dev.to/lampewebdev/the-most-outstanding-new-feature-in-javascript-you-need-to-know-about-optional-chaining-2pg2>

可选链接对于使用 Javascript 的每个人来说都是一个游戏规则的改变者。它和粗箭头函数或“let”和“const”一样重要。让我们来讨论一下它解决了什么问题，它是如何工作的，以及它将如何使你的生活变得更容易。

### 问题

想象一下:
您正在处理一段从 API 加载数据的代码。您正在获取的对象是深度嵌套的，这意味着您需要沿着对象属性的长路径前进。

```
// API response object
const person = {
    details: {
        name: {
            firstName: "Michael",
            lastName: "Lampe",
        }
    },
    jobs: [
        "Senior Full Stack Web Developer",
        "Freelancer"
    ]
}
// Getting the firstName
const personFirstName = person.details.name.firstName; 
```

现在就让代码这样并不是一个坏习惯。更好的解决方案可能是这样的:

```
// Checking if firstName exists
if( person &&
    person.details &&
    person.details.name ) {
        const personFirstName = person.details.name.firstName || 'stranger';
} 
```

正如你在这个例子中看到的，即使是像获得一个人的名字这样简单的事情也很难做对。
这就是为什么我们有像`lodash`这样的框架来处理这样的事情。

```
_.get(person, 'details.name.firstName', 'stranger'); 
```

“loadash”使代码可读性更好，但是现在你已经在代码库中引入了一个很大的依赖项。你需要更新它，如果你在一个团队中工作，你必须向团队传播知识和使用它。所以这也不是理想的解决方案。

### 解

可选链接可以解决所有这些问题(除了团队知识问题)。

#### 它是如何工作的

可选链接引入了新的语法，起初你会觉得奇怪，但是几分钟后你就会习惯了。

```
const personFirstName = person?.details?.name?.firstName; 
```

好吧，所以你现在可能有很多疑问(双关语)。所以这里的新事物是`?`。这是你必须考虑的问题。如果在一个属性的开头有一个`?.`，那就如同你会问你的自我人是否存在？或者用更 javascript 的方式来说，`person`的值是`null`还是`undefined`？如果是，那么我不会返回一个错误，而只是`undefined`。因此`personFirstName`将具有`undefined`的值。这个问题将对`details?`和`name?`重复。如果这些值中的任何一个为‘null’或`undefined`，那么`personFirstName`也将为`undefined`。这叫`Short-circuiting`。一旦 javascript 找到一个`null`或`undefined`，它就会短路并停止深入。

#### 默认值

我们还需要了解`Nullish coalescing operator`。好吧，这听起来很难学。但实际上，并不是。我们来看下面这个例子:

```
const personFirstName = person?.details?.name?.firstName ?? 'stranger'; 
```

将`Nullish coalescing operator`表示为`??`。它也很容易阅读。如果左边是`undefined`，那么`personFirstName`将从`??`获得右边的值。很简单。

#### 动态属性

有时你想访问一个动态值。它可以是一个数组值或者只是一个对象的动态属性。

```
const jobNumber = 1;
const secondJob = person?.jobs?.[jobNumber] ?? 'none'; 
```

这里需要理解的重要一点是`jobs?.[jobNumber]`和`jobs[jobNumber]`是一样的，但是它不会抛出错误；相反，它将返回“无”。

#### 函数或方法调用

有时你会在不知道对象是否有方法的地方工作。这里我们可以使用`?.()`语法或者带参数的`?.({ some: 'args'})`。它的工作原理和你想象的一样。如果这个方法在那个对象上不存在，它将返回`undefined`。

```
const currentJob = person?.jobs.getCurrentJob?.() ?? 'none'; 
```

如果没有`getCurrentJob`函数，那么`currentJob`将是`none`。

#### 今天就开始用吧

现在还没有浏览器支持这种开箱即用的功能——巴别塔拯救世界。已经有一个`babel.js`插件，如果你已经有了 Babel 设置，它很容易集成。

[巴别塔-插件-提议-可选-链接](https://babeljs.io/docs/en/babel-plugin-proposal-optional-chaining)

#### 压轴词

我认为这将使大量的 Javascript 代码更容易阅读，也更不容易出错。如果你愿意，也可以看看[提案](https://github.com/tc39/proposal-optional-chaining)。我希望这篇文章能让你变得聪明一点，现在你想把可选的链接集成到你的工作流程中了！

如果你能为我做以下事情，那将对我有所帮助。
去 [Twitch](https://twitch.tv/lampewebdev) 给我留个关注！如果只有少数人会这样做，那么这对我意味着整个世界！❤❤❤😊

**👋说你好！**[insta gram](https://www.instagram.com/lampewebdev/)|[Twitter](https://twitter.com/lampewebdev)|[LinkedIn](https://www.linkedin.com/in/michael-lazarski-25725a87)|[Medium](https://medium.com/@lampewebdevelopment)|[Twitch](https://dev.to/twitch_live_streams/lampewebdev)|[YouTube](https://www.youtube.com/channel/UCYCe4Cnracnq91J0CgoyKAQ)