# 欺骗 10 个快手

> 原文：<https://dev.to/phuchieu/cheating-10fastfingers-54j1>

10fastfingers.com 是一个很棒的应用程序，可以让你和朋友一起玩打字游戏。没有什么比用你极快的打字速度打败你的朋友更有趣的了。但是如果你不能快速打字，一点作弊是无害的。此外，在作弊的同时学习新事物是学习新知识的好方法。现在让我们开始

# 试音【10fastfingers.com】T2

第一步，我们必须访问[10fastfingers.com](//10fastfingers.com)并使用它。这个游戏的规则很简单，我们必须尽可能快和正确地打字来打败你的朋友。得分最高的人将赢得这场比赛。如果你像我一样打字不快，我们需要一点小技巧。为什么不让计算机替我们打字呢？谁能打败计算机的速度？

要欺骗这个游戏，我们需要两件基本的东西:如何获取或检索文本以便输入，以及如何将文本发送到输入字段

# 检索文本进行键入

通过使用我们最好的朋友“开发工具”，我们可以检查当前单词高亮显示的元素，如
[![Code](img/7526473136881a1b303b527a44a995e1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--x-OS586D--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i0.wp.com/frontend.cloudaccess.host/wp-content/uploads/2019/07/1.jpeg%3Fw%3D1518%26ssl%3D1) 
，我们可以很容易地发现我们想要检索的文本“children”被放在一个具有类名**高亮显示**的 div 元素中。要得到这个值，只需简单地写下

```
let text = document.querySelector(".highlight").textContent; 
```

Enter fullscreen mode Exit fullscreen mode

尝试在控制台窗口中运行此命令:
[![](img/6a38cf44645f9b1f91d335f42b2997e3.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--FKUYnlH2--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i2.wp.com/frontend.cloudaccess.host/wp-content/uploads/2019/07/image-17.png%3Fw%3D1052%26ssl%3D1)

我们已经完成了单词 job 的检索。现在进入第 2 步

# 将文本发送到输入字段

除了将检索到的文本发送到 input，我们还需要触发和输入事件，以便让系统识别文本并继续处理后面的文本。简单来说就是这样

```
let text = document.querySelector(".highlight").textContent + "  ";
document.querySelector("input").value = text;
var inputEvent = new InputEvent("input");
document.querySelector("input").dispatchEvent(inputEvent); 
```

Enter fullscreen mode Exit fullscreen mode

尝试在控制台窗口中运行这段代码。如果幸运的话，您将得到错误消息，而不是自动填充的输入。
[![Code](img/5d1949abc339024a55dc5be3fed040b0.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s---LDpmj2s--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i0.wp.com/frontend.cloudaccess.host/wp-content/uploads/2019/07/image-19.png%3Fw%3D1066%26ssl%3D1)

为什么会这样？因为你不走运。我只是开玩笑:)，问题是因为我们试图访问的*高亮*和*输入*元素不是我们运行代码的同一个框架。我们的代码此刻运行在“顶部”帧，但是【10ff.net】的*高亮*和*输入*都呈现在另一个名为*的帧下。要解决这个问题，只需在控制台窗口中选择*10ff.net*作为上下文，然后再试一次*

[![](img/d7649d03834fa70af6e680d09b64a886.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--gpjb1fjS--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://i0.wp.com/frontend.cloudaccess.host/wp-content/uploads/2019/07/cheating.gif%3Fzoom%3D2%26fit%3D759%252C331%26ssl%3D1) 
是啊，很管用。但是我们需要粘贴代码和输入这么多次，这个过程甚至比你打字还慢，我们需要让这个代码持续运行，我们怎么能打败其他人呢？

# 连续运行代码

除了连续运行代码，我们还需要在每一轮之间等待一会儿，让系统为我们呈现下一个单词。我们可以在不同的时间使用 setTimeout。大概是这样:

```
var i = 0;
while (i < 100) {
    i++;
    setTimeout(() => {
        let text = document.querySelector(".highlight").textContent + "  ";
        document.querySelector("input").value = text
        var inputEvent = new InputEvent("input");
        document.querySelector("input").dispatchEvent(inputEvent)
    }, i * 100) //Each turn is 100 miliseconds apart 
} 
```

Enter fullscreen mode Exit fullscreen mode

[![](img/04231e10ee5bd1995b574bc03e96e5c0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--4f7t75rr--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://i2.wp.com/frontend.cloudaccess.host/wp-content/uploads/2019/07/cheating-2.gif%3Fzoom%3D2%26fit%3D759%252C396%26ssl%3D1)

我们赢了！！但是我们仍然有一个问题，我们在开始的时候设置了想要运行代码的圈数，在这个例子中，我们设置了 100 次。开始时，我们创建 100 个超时函数，间隔 100 毫秒，这种方式效果很好。然而，这还不够好，因为在某些情况下，字数不是 100，所以我们的代码可能不会运行足够的时间或运行超过我们需要的次数。所以我们需要准确地确定在执行时间内，而不是在开始时间内，我们想要运行的圈数。我们如何做到这一点？承诺我们的答案。javascript 中的承诺给出了两种运行下一轮的可能性:resolve 让我们运行下一轮，reject 让我们打破循环。让我们把代码转换成使用 Promise

```
var run = () => new Promise((resolve, reject) => {
    if (!document.querySelector(".highlight")) {
        console.log("stop")
        reject("stop")
    }
    let text = document.querySelector(".highlight").textContent + "  ";
    document.querySelector("input").value = text
    var inputEvent = new InputEvent("input");
    document.querySelector("input").dispatchEvent(inputEvent)
    setTimeout(() => resolve("next"), 10)
})
while (true) {
    try {
        await run();
    } catch (e) {
        console.error("stop");
        break;
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

享受你的冠军！
[![](img/54d0a0ea70ed99d5ede02a2429d02e7b.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--FeGKDHkI--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://i2.wp.com/frontend.cloudaccess.host/wp-content/uploads/2019/07/cheating-3.gif%3Fzoom%3D2%26fit%3D759%252C439%26ssl%3D1)

# 总结起来

在本教程中，我们刚刚学习了检查元素、检索数据以及触发输入事件来模拟用户动作的方法。此外，我们还学习了如何通过使用 Promise comes with await 来创建一个循环，该循环在每个回合之间有间隙。要深入了解诺言，你可以访问[诺言](https://frontend.cloudaccess.host/promise-in-javascript)和[等待和异步](https://frontend.cloudaccess.host/await-and-async-%e2%80%8bin-javascript)。请慢慢来，自己做这个作弊，学习新的东西。下一篇文章再见