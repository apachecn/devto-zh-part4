# 不是每个函数都需要名字

> 原文：<https://dev.to/macsikora/not-every-function-need-a-name-4hpg>

让我们来谈谈匿名函数。用不用？使用未命名的函数是一种耻辱吗？这是唯一的原因吗？懒惰和缺乏创造力？我认为不是，我认为继续使用这种语言结构是有意义的。

## 动机

这篇文章是对 Kyle Simpson 推特的回应，也是对他的书[“Functional Light Programming”](https://github.com/getify/Functional-Light-JS)中提出的观点的回应，他在书中声称根本不应该使用匿名函数。我个人认为这些观点很激进，也不公平。

> ![getify profile image](img/54063020922d6b93360ef16043ade853.png)getify[@ getify](https://dev.to/getify)![twitter logo](img/4c8a2313941dda016bf4d78d103264aa.png)这种匿名 lambda 的福音是如此普遍...但这是不对的。绝对没有一种编程语言语法比函数名更能描述函数体的用途。
> 
> 对此意见零例外。2019 年 6 月 29 日 21 点 47 分[![Twitter reply action](img/44d8b042100e231770330321e5b63d65.png)](https://twitter.com/intent/tweet?in_reply_to=1145086534305177601)[![Twitter retweet action](img/93d9c70ccc54851d2e8e881b53c21dae.png)](https://twitter.com/intent/retweet?tweet_id=1145086534305177601)[![Twitter like action](img/2e93f7775eadefab8bcd34a4542cc5a7.png)](https://twitter.com/intent/like?tweet_id=1145086534305177601)

## 举例，给我举个例子

假设有数据转换的需求。我们有一个电影列表，这个列表需要被过滤和映射。

```
// anonymous functions
movies
  .filter(movie => movie.allowedAge <= age)
  .map(movie => movie.name) 
```

Enter fullscreen mode Exit fullscreen mode

如你所见，我已经声明了两个内嵌的匿名箭头函数来实现这个目标。现在我将展示如何用命名函数
实现同样的功能

```
// named functions
const ageIsAllowed = movie => movie.allowedAge <= age
const propName = movie => movie.name

movies
  .filter(ageIsAllowed)
  .map(propName) 
```

Enter fullscreen mode Exit fullscreen mode

这很简单，这也是我的目标，但是我认为命名这些函数没有什么好处。我可以说更多，我有一个真正的问题，命名第一个，我觉得没有研究实现，你仍然没有足够的信息来确定这是在做什么。

然而，我认为在这个阶段，这两种解决方案几乎是相等的，如果我在代码中看到后者，我会说——没问题。但是更进一步，从词法范围中去掉这样的函数是非常流行的，然后这开始变得不一样了。让我给你看一下

```
const ageIsAllowed = age => movie => movie.allowedAge <= age
const propName = movie => movie.name

/* other code - not related */

const allowedByAgeMovieNames = (movies, age) => {
  movies
  .filter(ageIsAllowed(age)) // additional complexity by partial application
  .map(propName)
} 
```

Enter fullscreen mode Exit fullscreen mode

问题是缺乏凝聚力。为了跟踪你在`allowedByAgeMovieNames`中做了什么，你需要遍历文件，最糟糕的是把这些函数放在文件之外。那么你也需要跳到那里。在这个过程中，函数失去了对词法范围的访问。现在我不能使用 closure，因为函数是在外面声明的，我需要使用 partial application 来提供`age`参数。这并不坏，虽然这是一个额外的事情要做。

## 简单的功能，还有什么？

我认为匿名功能在很多地方都是最好的工具:

*   简单的功能和自我解释的实现
*   函数是没有任何命令性代码的组合
*   作为其他函数代理的函数
*   父函数已经足够描述了

### 简单功能(已经解释过了)

```
map(x => x + 1) 
```

Enter fullscreen mode Exit fullscreen mode

### 作文

匿名函数是其他命名函数的组合。

```
map(element => getFirstLetter(getName(element))) 
```

Enter fullscreen mode Exit fullscreen mode

身体没有命令逻辑，它是自明的。不需要命名，命名并不会带来更好的结果，像`getFirstLetterFromName`这样的东西并不比上面的更易读。

### 代理

常见的情况是，代码库中存在符合需要的函数，但是接口与想要的接口不匹配。例如，需要事件处理程序，但我们的函数有不同的参数。

```
onClick(ev => movePlayer(ev.clientX, ev.clientY)) 
```

Enter fullscreen mode Exit fullscreen mode

只有最少数量的参数也是好的。您的任何函数都有可能需要整个事件对象。

### 父函数已经足够描述了

```
const getActiveUsers = users => users.filter(user => user.isActive) 
```

Enter fullscreen mode Exit fullscreen mode

正如你所看到的，父函数正确地解释了整个函数。不需要命名 filter 中使用的谓词。在这种情况下，甚至更复杂的谓词实现也可以接受匿名函数的形式。

## 我应该一直使用匿名函数吗？不要！

我在这里的要求是，如果函数体包含命令性的而不是琐碎的实现要处理，你就应该命名它。同样的规则我适用于条件，如果我的条件不是微不足道的，我给它一个名字。所以代替

```
if (x < y && z > w || v === true) 
```

Enter fullscreen mode Exit fullscreen mode

我更喜欢

```
const someExplanation = x < y && z > w || v === true

if (someExplanation) 
```

Enter fullscreen mode Exit fullscreen mode

但我觉得不应该给简单的条件起名，也不应该给琐碎的函数起名。因为命名可能比理解函数本身更难。

## 点自由风格呢？

没有，因为以上任何例子都在 PFS 中。因为 JS 没有工具来实现 PFS。为了做到这一点，您需要或创建工具，或使用外部库，如 Ramda。PFS 中最重要的事情是编写操作符/函数和 currying。我将在以后的文章中尝试触及这个主题。但是我想说清楚，如果有人试图在原始论点中，找到点自由的东西，那么我说这没有任何意义。

## 结论不是非黑即白，而是灰色

无论如何，我不想说服任何人停止命名函数。我的声明很简单:不要陷入这种虚假的限制。有时候，给简单的事物命名是没有实际意义的，最糟糕的情况是用错误的名字给简单的事物命名。匿名函数是一个工具，当不需要代码共享时，如果实现只是绑定到上下文并生活在其中，这是一个很好的工具。给它命名，或者不命名。这个选择我留给你。