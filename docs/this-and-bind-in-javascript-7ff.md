# 这和 Javascript 中的 bind()

> 原文：<https://dev.to/prateek_gogia/this-and-bind-in-javascript-7ff>

***之前发布于[我的网站](https://reeversedev.com/this-and-bind-in-javascript<br>%0A)

[![this-and-bind-in-javascript](img/ac7d9b8e062358cb37d7df065c14df4b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Umw6v1I4--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn.jwplayer.com/thumbs/trS42zpd-720.jpg)

> “当处理 JavaScript 时，事情会变得非常混乱，特别是当你必须处理面向对象的 JavaScript(基于 OOP)时。虽然，在面向对象的 JavaScript 中，所有的东西都是以函数和对象的形式出现的，这对于理解这门语言来说要简单得多(真的吗？)".

### 什么是`this`？

Javascript 中的关键字`this`就像英语中的单词‘this’一样被使用，这意味着每当我们在某处使用`this`时，我们都在谈论一些上下文。

让我们通过参考(我在这里用老派的说法)来挖掘更多，那时全世界都有王国，每个人都想统治彼此。战争时期的典型对话是这样的:

**国王** : *“我们需要在 05:00 之前占领这座山峰(用手指着地图)，以便更好地控制我们的局势”*。

**陆军参谋长** : *“很抱歉我这么笨，我的国王，但是到底是哪座山峰？”*

**王** : *本峰。(再次用手指着地图)*

陆军参谋长 : *“但是，老板，我甚至不知道你所说的高峰的任何适当背景，我该如何指挥我的军队采取行动？”*

**国王** : *(在意识到他是个字面上的哑巴之后)我们需要占领 XYZ 山的这座山峰(现在拿起军队首领的手指指向地图上的点)*

**陆军参谋长** : *“我马上指挥军队”*

```
<img src="http://clipart-library.cimg/piozGxaiE.jpg" width="200"> 
```

在上面的对话中，两个人之间的事情变得非常混乱，因为两个人都在各自的语境中交谈，这进一步造成了混乱。

同样，在 Javascript 中，当我们处理**对象**、**函数**和`this`时，事情变得非常混乱，因为我们经常在上下文中出错。

### 关于 Javascript 的东西

这里有一个关于 Javascript 的东西，它不仅是面向对象的编程语言，也是一种函数式编程语言，这使得用它编写代码的开发人员更加容易。像 map()、reduce()和 filter()这样的高阶函数确实减少了用循环操作代码以及最终与结果相关的计算的痛苦。

事实上，Javascript 作为一个整体越来越多地与对象和函数有关。不管我们想用它构建什么，我们都需要利用对象和函数。

### 在 Javascript 中使用`this`

对于面向对象编程语言来说，`this`关键字是语言中最重要的元素之一，没有它，语言是不完整的。

让我们用一些代码来更好地理解。

```
let person = {
    name: 'HODOR',
    dialogue: 'HODOR!',
    talk: function() {
        console.log(this.dialogue)
    }
} 
```

我们有一个名为“person”的对象，它具有名称、对话和谈话属性。现在我们称之为。

```
person.talk() // "HODOR!" 
```

这是预料中的事。对吗？现在，让我们换一种方式称呼它。

```
let talkFunction = person.talk
talkFunction() // undefined 
```

呀！这就是这里正在发生的事情。

当我们声明变量 *talkFunction* 时，它不再是一个方法，而是一个函数。但是，为什么它会给出`undefined`？这是因为，

> `this`关键字是指**被称为**的上下文，而不是**被指定为**的上下文。

#### 那么，我们如何使用这个函数呢？

这里，`bind()`开始行动了。一个`bind()`负责将一个对象附加到一个调用它的函数上。

让我们再来看看代码，

```
let person = {
    name: 'Jon Snow',
    dialogue: 'We the North!',
    talk: function() {
        console.log(this.dialogue)
    }
}

let talkFunction = person.talk
let bindedFunction = talkFunction.bind(person)
bindedFunction() // "We the North!" 
```

这里，`bind()`将“人”对象附加到 *talkFunction* 上，因此，talkFunction 能够得到`this.dialogue`并打印对话，**“我们北方！”**