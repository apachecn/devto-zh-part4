# Jetpack Compose #2 的心理模型:声明式 UI(带代码)和关注点分离

> 原文：<https://dev.to/louis993546/mental-models-of-jetpack-compose-2-declarative-ui-with-code-separation-of-concerns-4636>

<figure>[![](img/d72877ef63bd98eafdd91064bd983103.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--4GIqZqoc--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AyEOKwO2nR_D-uraGqiMNNw.jpeg) 

<figcaption>分离。照片由[杰米街](https://unsplash.com/@jamie452?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)上的 [Unsplash](https://unsplash.com/search/photos/lanes?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)</figcaption>

</figure>

在[最后一章](https://dev.to/louis993546/mental-models-of-jetpack-compose-1-state-programming-models-20eh)中，我们看了两个心智模型:“状态”和“编程模型”。现在，让我们更进一步，看看声明性模型是如何转化为 UI 开发的。🌀

### 声明式 UI(及其谱)

即使目标从“描述建造房子的过程”🏠”(祈使句)来“描述房子”🏠“(陈述性的)，仍然有很多不同的方式来“描述房子”🏠".你可以做一个 CAD，你可以用笔和纸把它画出来，或者你可以写一份 200 页的描述，描述每一面墙的每一种纹理、材料和颜色。而在我看来，对于声明 UI 来说，或多或少是一样的故事。有许多不同的解决方案，您所拥有的灵活性是它们之间最明显的区别之一:

#### 静态类的声明式 UI

基本上大多数标记语言(如 XML、HTML)都属于这一类。在这些语言中，除了展示现有的“组件”或“标签”或“任何你想叫它的东西”之外，你没什么可做的。如果你想做其他任何事情，你将不得不回到命令式编程的世界。

```
<LinearLayout
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:orientation="vertical" \>

    <TextView
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Hello, World!" \>

</LinearLayout> 
```

#### 少静态类的声明式 UI

虽然 XML 不是对人类最友好的语法，但它也远不是“读起来很糟糕”或什么的。如果它仍然可以理解，那么如果我们只是在它上面添加一些额外的特性呢？介绍数据绑定，早在 2015 年就正式介绍给 Android 开发者了。

[https://www.youtube.com/embed/5sCQjeGoE7M](https://www.youtube.com/embed/5sCQjeGoE7M)

它仍然在很大程度上基于所有现有的语法，以及开发人员多年来积累的所有 XML 技能。它基本上只是引入了一系列基本的命令式编程工具，并使得在 XML 本身中保留更多的“UI 关注”变得更加容易。

```
...

<TextView
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:text="@{viewmodel.userName}" \> // <- tada 🎩

... 
```

#### 一种真正动态的声明式用户界面

[https://www.youtube.com/embed/GW0rj4sNH2w](https://www.youtube.com/embed/GW0rj4sNH2w)

就在两年前，在 web 开发领域，一个名为 [React.js](https://reactjs.org/) 的小项目被 facebook 开源，虽然早期的反应并不十分出色，但没过多久业界就以非常快的速度采用了它。不仅仅是 React 本身，类似的框架也开始出现并被业界采用:Vue.js、Angular，以及后来的 Android(和 iOS)世界，React Native、Anko、Litho，以及最近的 Flutter。那么它有什么特别之处呢？

```
class Hello extends React.Component { 
  render() { 
    return <h1>Hello world!</h1>; 
  } 
} 
```

数据绑定将逻辑引入标记语言，而 React 则相反，将标记语言引入命令式编程语言。虽然这听起来可能有违直觉(如果目标是鼓励声明性代码，为什么我们要让编写命令性代码变得如此容易！？)，现实是，制作一个通用的声明式编程框架真的真的真的很难。人们构建了外观和行为都非常不同的各种东西，开发人员最不愿意做的事情就是凌晨 3 点还不睡觉去调试他们自己的代码，而是深入到框架中，并且仍然需要 10 个小时才能意识到框架还没有考虑到边缘情况，并且真正解决这个问题的唯一方法是颠倒框架解释 50%代码的顺序。

基本上说“我们讨厌黑魔法”是很长的路要走，拥有良好的“开发人员人机工程学/体验”是声明式 UI 框架以前的尝试所遗漏的一大块。调试器、断点、日志和您已经习惯的语法等东西非常有用，通过使用 JavaScript 等命令式编程语言，React(以及类似的 UI 框架)对开发人员非常有吸引力。

人们发现反应有吸引力还有许多其他原因，它们将在后面章节中用其他心理模型来描述。所以请保持关注！⏰

### 分离顾虑

我觉得一些开发人员对 React.js 类声明式 UI 的最大担忧之一是“它违反了关注点的分离”。虽然我承认现在所有的东西都可以放在同一个地方，但是“不，我会把所有的东西都放在这个类中”是很诱人的。只是 XML 也不一定是“分离关注点”的好方法。例如，“加载时显示 ProgressBar，加载完成时显示 RecyclerView”，我肯定会将其归类为视图逻辑，但 Java/Kotlin 代码是触发它的代码。像这样的简单情况可以用数据绑定来缓解，但是你也可以[全面使用它](https://medium.com/@hellotimmutton/an-argument-against-data-binding-13e2aaf7a9b1)，并且将业务逻辑添加到 XML 中。

我想说的是，虽然 XML 鼓励关注点的分离，但有时 XML 会给我们一种错觉，好像它实际上就在那里，而实际上并不存在。它仅仅是一种标记语言的局限性使它很难立刻看到全局。在同一个语法中拥有 UI + UI 逻辑+业务逻辑(+ more)使得重构(或者只是简单地重新组织)它们成为它实际上所属的最逻辑的“关注点”变得容易得多。

第二章到此结束，接下来还有更多章节。如果你有任何意见、想法或反馈，请在下面留下评论，或者在 twitter 上找到我！🐦

[蔡路易](https://twitter.com/louis993546)