# 简单地说，C#异步等待

> 原文：<https://dev.to/htissink/c-async-await-simply-5dh1>

# 一次一个

执行代码是为了达到某种目的。通常它是通过一系列步骤运行的，最终导致一个最终结果。每个步骤都需要一些时间来执行，并且一个接一个地同步运行它们会花费大量的时间。

[![alt text](img/e6e680a824a473c53eca8d5242c8805c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--9mIA-eWv--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i0.wp.com/www.whatsageek.com/wp-content/uploads/2014/12/Dr-Evil.jpg%3Fresize%3D746%252C436)

说你是邪恶博士-如果你愿意，是邪恶博士。而你想经营一个成功的犯罪企业来接管世界；也许你想赚 100 万美元...一个接一个地执行你所有的计划会是最有效的方法吗？

# 经营一个*邪恶的*帝国

[![alt text](img/c987ed62aa505dd7592a121e8323037a.png)](https://i.giphy.com/media/jd6TVgsph6w7e/source.gif)

作为一名*邪恶策划者*，你首先需要的当然是一个**邪恶巢穴**。

*   `Task.Delay(x)`给出一个`x`毫秒的逻辑延迟，不会阻塞当前线程

```
public static void BuildAnEvilLair()
{
    await Task.Delay(5000);
    Console.WriteLine("Evil Lair Built!");
} 
```

Enter fullscreen mode Exit fullscreen mode

在你建立你的巢穴后，你需要雇佣一些亲信。

```
public static void HireSomeHenchman()
{
    await Task.Delay(2000);
    Console.WriteLine("Henchman Hired!");
} 
```

Enter fullscreen mode Exit fullscreen mode

当然，你需要创造一个你自己的迷你克隆体。

```
public static void MakeMiniClone()
{
    await Task.Delay(3000);
    Console.WriteLine("Mini Clone Created!");
} 
```

Enter fullscreen mode Exit fullscreen mode

一旦这些都准备好了，你就可以开始你的*超级邪恶计划*了！

```
public static void SuperEvilPlan()
{
    await Task.Delay(4000);
    Console.WriteLine("Super Evil Plan Completed!");
} 
```

Enter fullscreen mode Exit fullscreen mode

让我们不要忘记用你的超级邪恶计划威胁世界领导人，并勒索 T2 一百万美元。

[![alt text](img/35ebb39ca239885c47330b9367c1d7f7.png)](https://i.giphy.com/media/BZlNhp9L5WINi/giphy.gif)T3】

```
public static int MakeOneMillionDollars()
{

    await Task.Delay(2000);
    Console.WriteLine("Million Dollars Made!");
    return 1000000;
} 
```

Enter fullscreen mode Exit fullscreen mode

最后，当一切都完成后，我们可以实现**统治世界**。

```
public static void WorldDomination()
{
    await Task.Delay(6000);
    Console.WriteLine("World Domination Achieved!");
} 
```

Enter fullscreen mode Exit fullscreen mode

把我们的计划放在一起

```
BuildAnEvilLair();
HireSomeHenchman();
MakeMiniClone();
SuperEvilPlan();
var money = MakeOneMillionDollars();
WorldDomination(); 
```

Enter fullscreen mode Exit fullscreen mode

同步需要 22000 毫秒(或 22 秒)*。*

 *# 一下子

[![alt text](img/e516bd98e2bd2ab6964263b9c722fc54.png)](https://i.giphy.com/media/l3mZjke44skqBVJBe/source.gif)

这种同步*计划*中的许多步骤可以并行执行，*异步*。那么这是怎么做到的呢？

首先，所有的方法将与`async`描述符异步，它们的执行代码将被等待。

等待异步代码，等待代码被异步处理，`await`之后的代码作为*延续*添加到`await`中。*延续*将在等待*的代码*运行完其持续时间并成功执行后运行。

```
public static async Task BuildAnEvilLair()
{
    await Task.Delay(5000);
    Console.WriteLine("Evil Lair Built!");
}

public static async Task HireSomeHenchman()
{
    await Task.Delay(2000);
    Console.WriteLine("Henchman Hired!");
}

public static async Task MakeMiniClone()
{
    await Task.Delay(3000);
    Console.WriteLine("Mini Clone Created!");
}

public static async Task SuperEvilPlan()
{
    await Task.Delay(4000);
    Console.WriteLine("Super Evil Plan Completed!");
}

public static async Task<int> MakeOneMillionDollars()
{
    await Task.Delay(2000);
    Console.WriteLine("Million Dollars Made!");
    return 1000000;
}

public static async Task WorldDomination()
{
    await Task.Delay(6000);
    Console.WriteLine("World Domination Achieved!");
} 
```

Enter fullscreen mode Exit fullscreen mode

带输出

> 赚了一百万美元！
> 超级邪恶计划完成！
> 迷你克隆人已创建！
> 邪恶巢穴建成！
> 走狗雇佣！
> 统治世界实现了！

[![alt text](img/94bdce7b097c051e798dca2789011d4b.png)](https://i.giphy.com/media/SCaWhPSWvBxZK/giphy.gif)

明明这是一个**乱**。一切都在同时发生**。真的很快——只取决于最慢的方法(6000ms)。很明显，有些呼叫依赖于其他呼叫。现在我们需要*让它们彼此同步*。**

 **# 有效异步

那么，作为一个*邪恶的策划者*，你会运行哪些任务，按什么顺序运行？

首先，我们需要一个邪恶的巢穴。在那之后，我们可以雇用一些亲信，创造一个我们自己的迷你克隆。然后我们可以想出一个超级邪恶的计划，之后我们可以赚一百万美元，实现统治世界。

通过使用`await`和`Task.WaitAll`的正确组合，我们可以改变执行代码。`Task.WaitAll`异步地触发所有的任务，一旦所有的任务都完成了，就结束*等待*那些任务——所以`Task.WaitAll`花费的时间和等待的*等待的时间一样长。*

让我们再试一次。

```
// First we need an Evil Lair
await BuildAnEvilLair();

// Next, hire Henchman and create a Clone (asynchronously)
Task.WaitAll(HireSomeHenchman(), MakeMiniClone());

// Now, come up with the evil plan
await SuperEvilPlan();

// And finally, make One Million Dollars (and achieve World Domination)
Task.WaitAll(MakeOneMillionDollars(), WorldDomination()); 
```

Enter fullscreen mode Exit fullscreen mode

有了输出，

> 邪恶巢穴建成！
> 走狗雇佣！
> 迷你克隆人已创建！
> 超级邪恶计划完成！
> 百万美元赚了！
> 统治世界实现了！

整整 18000 毫秒(18 秒)。比*同步*实现少 4 秒。

[![](img/d62cbc91cba02f4876de0e5581da006f.png)](https://i.giphy.com/media/brDwVn5kGIz3W/source.gif)

所以请记住，下次你想成为一个*邪恶的天才*考虑异步执行和一些时间。如果这取决于那些*该死的*超级间谍，每一秒都很重要！***