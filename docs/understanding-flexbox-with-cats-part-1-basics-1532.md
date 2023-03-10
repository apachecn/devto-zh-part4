# 用猫理解 Flexbox:第 1 部分基础知识

> 原文：<https://dev.to/kefimochi/understanding-flexbox-with-cats-part-1-basics-1532>

每个人都喜欢猫，对吗？它们是可爱的毛绒绒的小动物，喜欢砸东西，不服从任何人。嗯，在这个系列中，我们将假设他们会突然听到所有给定了神奇短语`display: flex;`的命令。“用猫来理解 flexbox”将使用`cats and boxes`的类比来教授 CSS box 模型、Flexbox 定位等概念，并提供一些关于容器使用的有价值的提示。请记住，本系列假设读者已经对 HTML 和 CSS 有了一些基本的了解。

在第一部分中，我们将主要关注读者对 flexbox 定位如何与多个容器一起工作的透彻理解。这一部分是为两类人准备的:一类是从未真正理解 flexbox 的人，另一类是喜欢阅读某人如何在联系两件看似无关的事情时创造性地使用类比的人。

<center>~ Please enjoy this article full of kitten logic! ~</center>

![An illustration of a black cat staring at you](img/ad557e0f6f71d89efdb794c9e1151526.png)

### “Flexbox 与猫”竞赛

*   [第 1 部分:Flexbox 基础知识](https://dev.to/kefimochi/understanding-flexbox-with-cats-part-1-basics-1532)
    *   对 CSS 的盒子模型和 div 定位的初学者友好的介绍
*   第二部分:用“盒子”思考
    *   一篇描述在使用提供的设计时如何识别容器(“div”)以及如何实现所需定位的文章。
*   第 3 部分:高级 Flexbox 和 CSS
    *   涵盖一切，从使用 flexbox 使网站反应迅速到如何创建自己的设计。我们还将了解在本系列中学到的所有内容的真实实现。

[![A black text divider](img/972f2f7b9d6d904ebdbfac1f03f96b6f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--nPKCudV7--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/bzr47frhmg7w17i22gw9.png)

## 《猫和盒子》简介

<figure>

[![Cat's face looking deeply into your soul from inside of a box](img/d8f2d8f1bc5bed1576d8e104ac0ab0b3.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--8Bzmbftr--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/04tkt4ec6pmgu5zxv731.jpg)

<figcaption>Cat's face looking deeply into your soul from inside of a box</figcaption>

</figure>

让我们想象一种情况，你突然发现自己要对三只小猫负责。当你看到你的公寓慢慢变得杂乱无章，因为那些充满活力的小猫到处乱走——在它们不应该抓的表面上抓痒——你慢慢开始恐慌。对所有这些混乱有什么可能的解决办法？🤔

如果你想过限制猫的空间，你是对的！就像在现实生活中一样，在将任何`cats`(任何内容的表示，如图像、文本、列表等)放入之前，我们首先必须设置一个`box`(也称为`<div>`元素)。首先把小猫放在地上，然后在它们上面加一个盒子是没有意义的，所以在使用 flexbox 时也要尽量避免这样做！

## 设置默认框

记住这一点，让我们设置我们的 HTML。现在重要的是要注意到`cats` (3 张图片)已经被放在了一个`box`(带有一个`cat-box`类的 div)中。你可能会奇怪为什么他们都聚集在一个地方，好吧，是时候介绍一些小猫逻辑了！

[https://codepen.io/KateEfimova/embed/NQxNVp?height=600&default-tab=result&embed-version=2](https://codepen.io/KateEfimova/embed/NQxNVp?height=600&default-tab=result&embed-version=2)

你看——作为它们的默认行为，猫喜欢拥抱。尤其是考虑到这些小猫在毁坏了整个公寓后，几秒钟前刚刚被安置在一个陌生的环境中。他们不知道该怎么做，所以他们总是在左上角紧紧挨着，互相取暖，感到安全。

<figure>

[![All cat images are inside of a div container and are aligned in the top left corner](img/d556009eab53dc0b27a19bd80adb3429.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--1DSfrxjJ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/gpa56eyvjawy3zy42y1o.png)

<figcaption>All cat images are inside of a div container and are aligned in the top left corner</figcaption>

</figure>

## Flexbox 简介

猫通常认为自己很受尊重，所以为了让它们服从，我们必须给小猫所在的盒子下达`display: flex;`命令。有人可能会问，“为什么不把它单独给每一只猫？”这是因为盒子比猫拥有更高的权威，因为它限制了猫的活动空间。而猫——作为骄傲的贵族——*只听更高权威的话。如果您尝试将`display: flex;`添加到任何猫图像，将不会发生任何事情；他们很固执(更上了`why`之后，就和`thinking outside of the box`有关了！*一语双关**

display flex 将做的是告诉猫图像将自己定位在一行中，并开始听取其他命令。正如你刚才所想的，它们最初被放置在一排，所以唯一会被注意到的区别是图片之间缺少空间。

<figure>

[![On the left there's a small space between pictures of cats vs on the right there's no space between cat pictures](img/f8b4fe1d80e010b5dc7aa9691309a8d0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--qo0bBI4d--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/g1ovqqyv19e7vxvwemda.png)

<figcaption>On the left there's a small space between pictures of cats vs on the right there's no space between cat pictures due to added `display: flex;`</figcaption>

</figure>

现在，让我们假设一个完美主义的朋友来到你的公寓，想要根据房间的中心排列所有的小猫和盒子。我们需要在两个方向上对齐它们:水平方向(主轴)和垂直方向(横轴)。下面是轴的一个很好的可视化表示:

<figure>

[![Cross(x) vs main(y) Axis when it comes to web development](img/5d7e1852de79ca13f26c3225afb6f535.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--J-z7B283--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/g27l033hgkwruldy3wlq.jpg)

<figcaption>Cross(x) vs main(y) Axis when it comes to web development</figcaption>

</figure>

首先，让我们通过将`align-items: center;`连接到`.cat-box`来根据 y 轴对齐它们。然后，让我们也通过添加`justify-content: center;`在 x 轴上正确定位它们

<figure>

[![Cats that are aligned centrally on the y-axis](img/71dfebb60868eebf434ec613a57c3ff1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--PKZy_eif--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/1fe1y0tiu2ir8c56latc.png)

<figcaption>Cats that are aligned centrally on the y-axis</figcaption>

</figure>

<figure>

[![Cats that are aligned centrally on both y-axis and x-axis](img/cabc352bd96bc4cfc60615482496a8f5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--RbAZVH29--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/rpw3nrm0gu7o6cl08iks.png)

<figcaption>Cats that are aligned centrally on both y-axis and x-axis</figcaption>

</figure>

此时，这些 CSS 属性被添加到`.cat-box`:

```
  display: flex;
  align-items: center;
  justify-content: center;

```

正如你可能已经注意到的，即使猫现在被排列在中间，装它们的盒子仍然不在它应该在的地方。完美主义者的朋友变得不安，不再信任你承担如此重要的责任，开始扰乱自己的猫定位。

这是他试图通过附加我们刚刚附加到`.cat-box`的相同属性来对齐 CSS 的`body`上的所有容器所得到的结果

<figure>

[![Despite using same properties on the `body`, the box only gets aligned centrally according to x-axis](img/dc24868afbde86665b3e4c53140c8004.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--wa0m_8pY--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/n2kvatihe14xrwyv9rw8.png)

<figcaption>Despite using same properties on the `body`, the box only gets aligned centrally according to x-axis</figcaption>

</figure>

他还没有意识到的是，所有的家具和其他物品，如任天堂 Switch 和 MacBook Pro，突然也放在了公寓的中央，这绝对是一种不受欢迎的行为！将 flexbox 属性附加到主体通常被认为是一种不好的做法。

<figure>

[![A MacBook on the left of kittens and Nintendo Switch on the right of the kittens get aligned centrally on y-axis](img/092b5661ba2155f7e70bc719589ed0dc.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--FDcMxP5e--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/luue5r1h4yn4tc5o0k90.png)

<figcaption>A MacBook on the left of kittens and Nintendo Switch on the right of the kittens get aligned centrally on y-axis</figcaption>

</figure>

因此，为了将小猫*和*框对齐到屏幕中央，我们必须创建另一个带有类`.outer-container`的`<div>`，并将其包装在现有内容周围。我们为什么需要它？让我们通过分解一个例子来深入探究为什么！

看看这个 dev.to 组件，可以在他们的主页上找到。右边是一个演示，展示了当前使用了多少个容器(盒子)来使其工作。注意它们是如何嵌套的——意思是在彼此内部。看起来像荧光笔的浅色用来描绘每个容器的内部填充物。

<figure>

[![Dev.to components with navigation links is broken down to several colored boxes](img/5ecad88a62508781ce5cdc403a4145f9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--WTaLG6Zb--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/0yn25gekow9k33tjrxnl.png)

<figcaption>Dev.to components with navigation links is broken down into several colored boxes</figcaption>

</figure>

所有 flexbox 操作都**总是**一层，这意味着它只影响最近的子盒。例如，紫色容器只能操作三个内部容器，但不能操作其中的任何`<h>`或`<a>`元素。而深蓝色容器能够通过将 flexbox 附加到自身来对其锚点做一些事情(注意，当您想要操作容器内部的元素时，`display: flex`将被附加到*容器*)。

如果你还没有注意到这个模式:我们想要对齐小猫= >给离它们最近的外盒一个命令；想要操作同一个盒子= >在盒子外面创建了一个容器来包装全部内容。你将无法从`.outer-container`开始控制猫。因此，试着记住一句话**跳出框框**，因为如果你想操作某些元素，你需要找到最近的外部容器，以便给它分配 flexbox 属性。

请记住，深蓝色容器中的所有锚点被放置在一列中的原因是因为它们通过从紫色父容器继承来获得 flexbox 属性`flex-direction: column;`。看看如果 flexbox 方向被更改为紫色容器上的 row，这个 dev.to 组件会发生什么。没有线条和高光，你还能认出同样的盒子吗？😉

<figure>

[![Components look broken when flex-direction of the most outer container is changed to row](img/310a70ab0e596abc95bd040d369d02a8.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--p83yLIFe--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/zsmqxhuzmi79w3h0qlv6.png)

<figcaption>Components look broken when flex-direction of the most outer container is changed to row</figcaption>

</figure>

回到猫的视觉例子，现在应该明白为什么我们需要一个额外的包装容器:否则没有办法控制装猫的盒子。剩下要做的就是在 HTML 上添加一个额外的`<div>`，给它一个类，声明 flexbox 中心定位，并在那个类上添加一个*有限的*大小。有很多方法可以让一个容器根据里面内容的拉伸和挤压来改变它的大小，但是我们将在本系列的第 2 部分和第 3 部分深入探讨这个问题。你可以在这个 CodePen 上随意查看完成的代码:

[https://codepen.io/KateEfimova/embed/JgbEGr?height=600&default-tab=result&embed-version=2](https://codepen.io/KateEfimova/embed/JgbEGr?height=600&default-tab=result&embed-version=2)

<figure>

[![Both cats and the box are positing in the center of the screen](img/2864a813e1df6bd7a11572b0cc2c997a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ngdmu5lh--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/y3ajsb89jf9igo06s0iy.png)

<figcaption>Both cats and the box are positing in the center of the screen</figcaption>

</figure>

我们可以和各种各样的人玩得很开心`justify-content`，这里有一个使用这个 gif 来改变事情的可视化表示:

<figure>

[![A gif showing properties like `justify-content` flex-start, flex-end, space-evenly and space-between](img/5bbba801e7d6a6af7909871d2af2956d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--MDzVpLlg--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/d1kvha5eo6zlu2zmchyi.gif)

<figcaption>A gif showing properties like `justify-content` flex-start, flex-end, space-evenly and space-between</figcaption>

</figure>

[![Demonstrates difference between various `justify-content` properties](img/68acab855f07490600e0887142bea201.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--LfdZtPM---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/gh7fdb1a4rssmr1c368a.png)

[![A black text divider](img/972f2f7b9d6d904ebdbfac1f03f96b6f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--nPKCudV7--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/bzr47frhmg7w17i22gw9.png)

## 结论

希望使用`cats and boxes`的类比能帮助你更好地理解 flexbox 的概念。所有的术语都是有意简化的，只关注理解的重要性。此外，我很想听听你对第 1 部分的看法:哪些进展顺利，哪些还可以做得更好？请记住，这是我写的第一篇文章！这里有一个包含这篇文章的 markdown 的资源库，欢迎您创建任何和所有的 pull 请求！
[https://github . com/kefi mochi/flex box-with-cats/blob/master/Part % 201/basics . MD](https://github.com/kefimochi/Flexbox-with-cats/blob/master/Part%201/Basics.md)

<center>~ Thank you for reading! ~</center>