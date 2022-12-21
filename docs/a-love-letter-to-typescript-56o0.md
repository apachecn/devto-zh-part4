# 给打字稿的情书

> 原文：<https://dev.to/leoat12/a-love-letter-to-typescript-56o0>

亲爱的 TypeScript，我知道你的 JavaScript 兄弟有时候可能不想看到我们在一起，但我觉得你的品质是值得的，这就是我爱你的原因！

玩笑归玩笑，自从我开始真正研究 TypeScript 的许多特性以及它有多棒以来，这确实是我一直渴望写的一篇文章。封面图片中的代码不仅仅是为了展示，在这样一个简单的例子中，我可以展示我爱上 TypeScript 的许多原因，我希望从现在起尽可能多地使用它。所以，事不宜迟，我们一点一点去写这封情书吧。

当我们谈论 JavaScript 时，我们总是想到灵活性。它赋予了你编写代码的方式很大的灵活性，JavaScript 非常宽松，你可以在对象中添加字段，删除字段，函数可以作为参数传递，数组可以保存任何“类型”的元素(JavaScript 有一个非常松散的类型概念)。然而，所有这些灵活性都是有代价的。当我看到一个用 JavaScript 写的超过 100 行和 1 个文件的应用程序时，我总是觉得一团糟，无意冒犯。不知道东西从哪里来，去哪里。这就像踏入了一个雷区。我的背景来自 Java，在那里你可以控制你编写的几乎所有东西，有时候控制太多了。

这就是 TypeScript 的用武之地，它混合了 JavaScript 的灵活性，但又将它与静态类型语言提供的控制能力隔离开来。对我来说，它拥有两个世界的精华:JavaScript 和 Java(不要混淆它们)。让我们来看例子。首先是`Letter`界面。

```
export interface Letter {
  from: string;
  to: string;
  message: string;
} 
```

好吧，很正常，对吧？我喜欢 TypeScript 接口的一点是，你可以定义属性和方法，实现它们的类需要所有这些属性来满足它。这在 Java 中是不同的，在 Java 中,“属性”是在一个接口的结束常量中，它们不是属性。然而，TypeScript 中的接口与其他类一起显示了它们的威力。所以，我们下面呈现的是`LoveLetter`类。

```
export class LoveLetter {
  constructor(public from: string, public to: string) {}

  get message(): string {
    return `I love you, ${this.to}`;
  }
} 
```

如果你不知道 TypeScript wonderfulness，只看类你可能意识不到，但是这个类完全满足`Letter`接口，100%！首先，构造函数。在 Java 中，添加参数并把它们分配给类属性总是很烦人，这是不必要的样板文件！我喜欢插入`public`、`private`等的打字稿方法。向构造函数的参数声明一个类属性，并一次性将参数值赋给它。因此，`LoveLetter`类具有`Letter`需要的字符串属性`to`和`from`。

“但是`message`怎么样？在`LoveLetter`中是方法，不是字符串属性！”你可能会说。这是 TypeScript 的另一个非常有趣的特性:“getter 方法”。例如，它可以像 Java 中的 getters 一样使用，但是我认为在 TypeScript 中它有一个 Java 没有的问题。当你在函数前添加`get`这个词时，TypeScript 会把它当作一个属性来对待！所以，我们可以这样做:`letter.message`而不是这样:`letter.message()`。因为它被认为是 TypeScript 的一个属性，所以满足`Letter`接口是可以的。当你想以另一种方式塑造数据时，这是非常有用的，例如，为了可视化的目的，就像我对“I love you，#name#”所做的那样，使用字符串插值来插入收件人的姓名。在一个简单的例子中，它可能看起来没有什么大的不同，但是当你试图满足一些大的接口或者有更复杂的用例时，它就派上用场了。

对于一些可能习惯于 Java 的人来说，这听起来可能很奇怪，这个类没有像 Java 中的`class Foo implements Bar`那样的任何正式声明就实现了接口，但这是接口在 TypeScript 中如此强大的原因之一，这个类仅仅通过它的形状就满足了接口，正式声明是不必要的。你可以正式声明，但是:

```
export class LoveLetter implements Letter {
  constructor(public from: string, public to: string) {}

  get message(): string {
    return `I love you, ${this.to}`;
  }
} 
```

声明与 Java 非常相似，如果类对接口不满意，你会得到类级别的错误消息，但是一般来说你不需要这么明确。所以，如果一个类仅仅通过形状来满足一个接口，那么你可以进入我们例子的最后一部分:

```
const sendLetter = (letter: Letter) => {
  console.log(
    `From: ${letter.from} To: ${letter.to}  ${letter.message} `
  );
};

const loveLetter = new LoveLetter('Developer', 'TypeScript');
sendLetter(loveLetter); 
```

这里我们有一个函数，它被赋值给一个常数，并被声明为一个箭头函数。在这种情况下，它没有任何特殊的意义，但是箭头函数可以使代码更加优雅，主要是当 TypeScript/JavaScript 是回调的宿主时。此外，它可以解决一些关于作用域的问题，主要是当涉及到`this`时，但是我们可以在将来讨论这个问题。然后我们创建一个新的`LoveLetter`并将其赋给一个常量，没什么特别的，但是下一行显示了接口的一些功能。虽然该函数接收一个`Letter`作为参数，但是由于`LoveLetter`100%满足接口，所以您也可以使用`LoveLetter`的实例来调用该函数！更好的是，你不需要在任何地方告诉 TypeScript，它会自动知道。同样，界面是通过它的形状来评估的，如果形状是正确的，你就可以走了！

每天学习更多关于 TypeScript 的知识，以及它如何将我已经知道的语言中我最喜欢的东西融合在一种美丽的语言中，这是非常有趣的。还有许多其他值得一提的特性，但我认为通过这个简单的例子，您可以感受到 TypeScript 的伟大之处。我相信 TypeScript 在市场上有光明的前景，我向大家推荐它。别担心，我不会因为我亲爱的打字稿受欢迎而吃醋。