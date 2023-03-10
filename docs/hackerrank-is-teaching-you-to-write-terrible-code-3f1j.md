# HackerRank 在教你写可怕的代码

> 原文：<https://dev.to/raddevon/hackerrank-is-teaching-you-to-write-terrible-code-3f1j>

如果你不熟悉 HackerRank，它是一个练习编码的平台，并且越来越多地成为公司测试他们的工程候选人的平台。这就是为什么他们鼓励和他们一起练习或使用他们的平台**面试的人写糟糕的代码**是一个问题。

```
function Rectangle(a, b) {

} 
```

Enter fullscreen mode Exit fullscreen mode

这是他们让你为他们的 10 天 Javascript 写的一个函数的起点。创建一个矩形对象是这 10 天中的第 4 天。大多数编码人员会照原样采用这个起点，并填充括号之间的空间。这可能就是 HackerRank *期望*你对他们的问题所做的:保持他们给你的出发点不变，并填补缺失的部分。

如果他们只给了你这些，你可能会算出`a`是矩形的一边，而`b`是另一边。如果你读了问题描述，他们会明确告诉你`a`是矩形的长度，`b`是宽度。

## 为什么要保密？

[![Batman](img/931edc95b873ded9814a5b1bfa2b459b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ddRhtTZ8--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raddevon.com/wp-content/uploads/2019/09/batman.jpg)

蝙蝠侠需要对自己的身份保密。他晚上都在犯法。即使他这么做是为了正义，他还是有可能被逮捕。为了继续在街上维持治安，他不能让任何人知道他其实是布鲁斯·韦恩。

但是，据我所知，`a`和`b`不需要来隐藏他们的真实身份(长方形的长和宽)。事实上，**在这个函数中他们所要做的就是计算矩形的长度和宽度**。这意味着他们甚至不需要成为`a`和`b`。

Javascript 不保留名称长度和宽度。所以，为什么不直说呢？为什么长度不能是`length`，宽度不能是`width`？

## 是啊，但是有什么坏处呢？

在这个 HackerRank 练习中，您最多将编写几行代码。更不用说创建矩形仅有的两个逻辑参数及其长度和宽度。你可能不会永久的忘记 a 和 b 是什么。即便如此，仍然会有那么一瞬间你不知道`a`和`b`是什么。

这甚至会让你觉得很酷。就像你是一个有秘密代码的有趣俱乐部的成员。麻瓜看着`a`和`b`把手扔到空中，但是你……**你可以在几分之一秒内破译**。感觉像是超能力。如果 HackerRank 这样做，**它一定是正确的正确的方式** …对吗？！😰

在你的职业生涯中，你永远也不会写出这么短、这么少功能的应用程序。想象一下记住矩形函数中的 a 和 b 需要多长时间。现在把它放在参数的含义*没有*那么清楚的背景下。也许你有一个在页面上显示警告的功能。它需要三个参数:`a`、`b`和`c`。知道这些参数是什么吗？是啊，我也是。

现在考虑这个新的命名问题，并将其扩展到一个有 10，000 行代码的应用程序。每个函数都有参数，这些参数的名称都是晦涩的单个字母。既然您知道这是可以的，那么您的变量可能也没有经过深思熟虑地命名。你将花费大部分时间在这个应用程序上，只是想弄清楚它是做什么的。我同情那些不得不拿起这个应用程序并使用它的人。我希望他们有足够的阿司匹林。

你可能会认为我在 HackerRank 上挑选了唯一的例子，或者可能是最糟糕的例子。你就大错特错了。不是所有的练习都使用这种不透明的变量和参数命名，但是很多练习都使用了，有些甚至比我的第一个例子还要糟糕。这是另一个难以理解的练习的起始代码:

```
'use strict';

process.stdin.resume();
process.stdin.setEncoding('utf-8');

let inputString = '';
let currentLine = 0;

process.stdin.on('data', inputStdin => {
    inputString += inputStdin;
});

process.stdin.on('end', _ => {
    inputString = inputString.replace(/\s*$/, '')
        .split('\n')
        .map(str => str.replace(/\s*$/, ''));

    main();
});

function readLine() {
    return inputString[currentLine++];
}

// Complete the minimumBribes function below.
function minimumBribes(q) {

}

function main() {
    const t = parseInt(readLine(), 10);

    for (let tItr = 0; tItr < t; tItr++) {
        const n = parseInt(readLine(), 10);

        const q = readLine().split('  ').map(qTemp => parseInt(qTemp, 10));

        minimumBribes(q);
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

主函数中的变量命名真的让我晕头转向。`t`？`tItr`？`n`？`q`？`qTemp`？为什么这些练习题可读性不强？

嘿，你好。我帮助像你这样的人开始他们的网络开发生涯。首先要确定一个目标。参加[我的免费课程](https://raddevon.com/stick-with-web-development/)开始吧！🎉

## 代码是给人类的

你可能认为代码是给计算机用的。**不是的。**代码是给人类用的。如果它专门用于计算机，我们就不需要像 Javascript 或 Python 这样的高级语言了。

在编写代码时，请牢记这一点。写给别人看，而不是你。变量、参数和函数名应该封装尽可能多的上下文**来帮助读者理解他们正在阅读的内容。`a`不好。`length`好。`lengthInInches`甚至更好(如果，事实上，你希望度量单位是英寸的话)。如果您的代码发生变化，请确保更新您的名称以反映它们现在所代表的内容。**

 **看 HackerRank 很容易，因为他们是一个庞大的、成熟的网站，而且，如果通过他们管理的面试数量是一个指标，一个备受尊重的网站，假设他们正在用好的代码开始你。可惜，**事实并非如此**。

如果你在 HackerRank 上练习，帮你自己一个忙，通过将它们可怕的无意义的名字重构为传达阅读代码所需的含义和上下文的东西来开始你的练习。当你在平台之外的项目中工作时，确保他们的坏习惯不会毒害你的代码，并给你一个写难以理解的代码的开发人员的名声。

通过为跟在你后面的下一个开发人员编写代码，你不仅可以让其他开发人员与你一起工作，还可以让你的客户重新雇佣你，并把你推荐给其他需要你帮助的公司。**