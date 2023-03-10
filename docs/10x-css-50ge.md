# 10 倍 CSS

> 原文：<https://dev.to/amiangie/10x-css-50ge>

让我们直说吧:CSS 很乱。这是一种美丽的语言，它让网络繁荣发展——但它也可能很快变得非常肮脏。原因之一是这种语言本身的性质:它富有弹性，非常宽容——而且几乎不会对你强加任何规则。

您可以使用制表符。可以用空格。你可以把所有东西都写在一行里。您可以一遍又一遍地重复相同的规则集，每次添加一个新的声明。你甚至不需要有所有的分号。这是你的世界，你想做什么就做什么。

但是你应该吗？

不像画画，我不认为你的 CSS 代码是一个可以疯狂的地方。

多年来，人们一直在谈论大规模 CSS 架构，现在我们有各种各样的方法，如 BEM、SMACSS，以及我个人选择的武器 Atomic OOBEMITSCSS。但是没有一个涉及较小的规模，我们大部分的实际工作都是在那里完成的。

我说的是单一规则集。

如前所述，没有什么可以阻止你以任何你想要的方式或顺序编写声明。有人可能会说，如果能完成任务，这没关系；但是对于任何其他语言来说，CSS 中更好的代码风格会带来更好的 DX、更好的代码质量和更少的错误。

所以，你瞧，我个人对低级 CSS 结构的方法:

```
.classname {
    /* is this thing even visible? */
    display: block;

    /* where is it on the page? */
    position: absolute;
    top: 0;
    left: 0;
    width: 20%;
    height: 300px;

    /* what does its content look like? */
    font-family: 'Roboto', sans-serif;
    font-size: 1.2rem;
    font-weight: bold;
    font-style: italic;
    text-align: center;
    text-transform: uppercase;
    color: rebeccapurple;

    /* what's its box model? */
    padding: 10px 0;
    margin: 20px;
    border: 2px dashed salmon;
    border-radius: 5px;

    /* backgrounds, 'cause declarations tend to get so very long */
    background: url(someawesomepic.jpg) center center no-repeat;
    background-size: cover;

    /* purely a e s t h e t i c s */
    transition: all .25s;
} 
```

虽然这个例子并不完整，但是它涵盖了人们通常需要定义的最常见的属性。不管怎样，一些声明粘在一起，因为*在一起它们更有意义*:宽度和高度、边框和边框半径、字体和文本属性、定位属性等等。

我差不多已经遵循这个结构十年了，它让我的生活变得简单多了。我只需要浏览一下任何给定的规则集，就可以了解发生了什么，缺少了什么，并找出一些小错误，如重复或声明，这些都不会真正应用。

看起来好像我在说我的系统很棒——虽然它显然很棒，但我认为这里最重要的是真正拥有一个。当一个故事无处不在时，很难跟踪它，用这样的代码工作也很难。

编码快乐，上帝保佑。