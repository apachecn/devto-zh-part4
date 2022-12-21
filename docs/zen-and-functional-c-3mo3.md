# Zen 和函数式 C#

> 原文：<https://dev.to/htissink/zen-and-functional-c-3mo3>

编写函数式编程可以带来禅宗般的体验。简洁的代码块清楚地向你揭示了它们的意图和内部工作方式，functional C# *解开了这些结，清理了空间*。

## 不再有副作用

深吸一口气。保持 5 秒钟。呼气。

是时候进行功能性思考了，是时候和那些讨厌的、没有好处的副作用说再见了。

[![alt text](img/35e1930e94895c2d4fa34842a23eb3a9.png)](https://i.giphy.com/media/11QJgcchgwskq4/giphy.gif)

*   当你在当前作用域之外修改状态时，副作用发生了，不知不觉地在程序的其他地方改变了状态。这会导致不必要的行为和不必要的因果关系。
*   相反，更喜欢没有副作用的功能。这些函数被称为[纯函数](https://en.wikipedia.org/wiki/Pure_function)。(**免责声明**:编写纯函数是相当上瘾的——一旦开始就停不下来)

### 禅宗法典建议道:

**避免**:不纯函数

```
public void UpdatePrice(decimal vat, decimal cost, decimal markup)
{
    this.price = (cost + markup) * (1 + vat);
} 
```

Enter fullscreen mode Exit fullscreen mode

**偏爱**:纯函数

```
public decimal PriceIncludingVat(decimal vat, decimal cost, decimal markup)
{
    return (cost + markup) * (1 + vat);
}

price = PriceIncludingVat(0.2m, 10m, 2m); 
```

Enter fullscreen mode Exit fullscreen mode

调用`PriceIncludingVat`不会修改现有状态；它返回新的状态。您可以任意多次调用它，并且您当前的`price`不会受到影响，直到您实际设置它。

练习用更长的不纯方法创建纯函数。把副作用从方法中分离出来，把它们变成自己的函数，返回新的状态。你可能会发现自己解开了一些真正令人讨厌的混乱——这是一种宣泄，让你更接近禅。

## 永恒性

闭上眼睛。吸气。呼气。拥抱[永恒](https://en.wikipedia.org/wiki/Immutable_object)的宁静。

[![alt text](img/6e1b86940432326e794d84488d96e0cb.png)](https://i.giphy.com/media/yzvVXSvrg7JxC/giphy.gif)

你的国家将不再是易变的。带着`new`进去，带着**出来的老**。通过使你的对象不可变，你确保了在任何范围内**无论什么**都不能改变它们。这完全防止了副作用。在不可变对象上实现已更改属性的唯一方法是用包含更改的新对象替换该对象。

### 禅宗法典建议道:

**避开**:可变对象

```
public class ZenGarden
{
    public int Tranquillity { get; set; } // mutable
    public int Peace { get; set; } // mutable

    public ZenGarden(int tranquillity, int peace)
    {
        Tranquillity = tranquillity;
        Peace = peace;
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

这导致了

```
var garden = new ZenGarden(10, 20);
garden.Peace = -1000;
// or even worse
public int TranquilityAndPeace(ZenGarden garden)
{
    garden.Peace -= 1; // a side effect
    return garden.Tranquillity + garden.Peace;
} 
```

Enter fullscreen mode Exit fullscreen mode

**偏爱** :

```
public class ZenGarden
{
    public readonly int Tranquillity;
    public readonly int Peace;

    public ZenGarden(int tranquillity, int peace)
    {
        Tranquillity = tranquillity;
        Peace = peace;
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

这导致了

```
var garden = new ZenGarden(10, 20);
garden = new ZenGarden(10, 21); // peace goes up, because of immutability :]
// pure function
public int TranquilityAndPeace(ZenGarden garden)
{
    var calculatedGarden = new ZenGarden(garden.Tranquillity, garden.Peace - 1);
    return calculatedGarden .Tranquillity + calculatedGarden .Peace;
} 
```

Enter fullscreen mode Exit fullscreen mode

*   注意，`readonly`是用来代替**私有设置器**的，以确保`class`本身内的*甚至*的值都不能被更改。

不再担心不想要的变化(副作用)——不变性确保没有任何东西会改变同一个状态，友好地告别[竞争条件](https://en.wikipedia.org/wiki/Race_condition)和[线程锁](https://en.wikipedia.org/wiki/Lock_(computer_science))。

## 参照透明

拥抱清晰的禅意。一个[引用透明的](https://en.wikipedia.org/wiki/Referential_transparency)函数可以被它被调用时返回的值代替。不管上下文如何，它都为相同的参数返回相同的值。引用不透明的函数则相反——通常是因为引用了一些可能改变的外部值。

引用透明性有助于代码读起来更清晰。它解开了从其作用域之外获取值的函数，并限制它们在计算返回值时只作用于其参数。

它隔离功能，保护它们不受代码中外部变化的影响。

[![alt text](img/bbba3efb1fd7cb78b558d1862a13e8f6.png)](https://i.giphy.com/media/o4wnEX6N0ENUc/giphy.gif)

### 禅宗法典建议道:

**避免**:不透明功能

```
public double PriceOfFood(double price) 
{
    // if the number of people changes, the price of food changes
    // regardless of price changing
    return this.numberOfPeople * price;
} 
```

Enter fullscreen mode Exit fullscreen mode

**偏好**:透明功能

```
public double PriceOfFood(double price, int numberOfPeople) 
{
    return numberOfPeople * price;
} 
```

Enter fullscreen mode Exit fullscreen mode

## 高阶函数

一路都是一等(功能)宝贝*。让我们捡起这些功能，开始像对待(一等公民)一样对待它们。C#对待函数就像对待[一等公民](https://en.wikipedia.org/wiki/First-class_citizen)。这意味着函数可以用作方法的参数；函数可以赋给变量；函数甚至可以从其他函数返回。*

将函数作为一等公民使用允许我们将代码分解成小的构建块，每个构建块封装了一点点(希望是干净、纯粹和引用透明的)功能。这些小积木可以多种组合使用，以获得不同的功能。当我们的需求改变时，它们可以被换出和互换，而对代码库几乎没有影响。

[![alt text](img/7cbf65c19f94705cb58b55a67e0057c3.png)](https://i.giphy.com/media/yidUzx2B2f0SXheXGE/giphy.gif)

按需生成功能——不要写大量代码。让函数表现得像运动的部件一样*我们知道它们是*。

[高阶函数](https://en.wikipedia.org/wiki/Higher-order_function)接受函数作为参数，或者返回函数。因为 C#将函数视为一等公民，所以它允许创建高阶函数。

### 禅宗法典建议道:

**避开**:不清楚的 lambdas

```
var range = Enumerable.Range(-20, 20);
range.Where(i => i % 2 == 0);
range.Where(i => i % 5 != 0); 
```

Enter fullscreen mode Exit fullscreen mode

**偏好**:命名函数

```
Func<int, bool> isMod(int n) => i => i % n == 0;
range.Where(isMod(true, 2));
range.Where(!isMod(false, 5)); 
```

Enter fullscreen mode Exit fullscreen mode

**避免**:过程函数调用(如果适用)

```
public class Car {...}

public void ChangeOil(Car car) {...}
public void ChangeTyres(Car car) {...}
public void Refuel(Car car) {...}

public void PitStop(Car car)
{
    ChangeOil(car);
    ChangeTyres(car);
    Refuel(car);
}

PitStop(car); 
```

Enter fullscreen mode Exit fullscreen mode

**首选**:动态函数调用(如果适用)

```
public class Car {...}

public void ChangeOil(Car car) {...}
public void ChangeTyres(Car car) {...}
public void Refuel(Car car) {...}

public void PitStop(Car car, IEnumerable<Action<Car>> pitstopRoutines)
{
    foreach(var routine in pitstopRoutines)
        routine(car);
}

PitStop(car, new List<Action<Car>>{ChangeOil, ChangeTypes, Refuel}); 
```

Enter fullscreen mode Exit fullscreen mode

方法`PitStop`更加*可扩展*。它可以很容易地扩展，几乎不需要修改。这可以通过简单地将您想要执行的函数作为参数传入来实现，就像对待一等公民一样对待它们。

# 现在去拥抱禅吧，我的兄弟们

[![alt text](img/acb555fc453d2c90e1014b29e1c2a90d.png)](https://i.giphy.com/media/3o7btSlEkVbvtjkW3K/source.gif)