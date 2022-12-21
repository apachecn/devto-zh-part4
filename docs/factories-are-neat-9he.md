# 工厂很整洁

> 原文：<https://dev.to/htissink/factories-are-neat-9he>

设计模式现在已经是旧闻了。在我们真正理解写干净的代码意味着什么之前，它们是我们使用的训练轮。在我们真正掌握坚实的原则和**之前，所有这些叫做**。

在构建复杂的应用程序时，我一直喜欢设计模式；他们帮助管理复杂性和解决重复的简单问题。有几个是我每天都用的，有几个是我根本没用过的。

创造模式是最有用的——而*工厂*真的很棒。工厂帮助我们将创造性的代码与我们程序中实际的*肉和土豆*分开。

工厂确保我们总是面向接口编码，而不是实现。它帮助我们将代码从对低级类型的依赖中分离出来，并尽可能地调用通用代码。

我认为唯一的缺点是很难让工厂遵守 T2 的开闭原则。有一些方法可以解决这个问题，但是它们只是增加了复杂性，影响了工厂的简单优雅。所以就接受吧。他们不会是开放封闭的追随者；但是他们会很整洁。

### 让我们 C 一点儿吧

你可能会问，工厂是如何运作的？

让我们用一个巧克力工厂来演示一下:

我们会创造一些麦芽酒。午餐酒吧；奇巧；最后是 Aero。这些物品中的每一个都是巧克力。它们都符合巧克力的定义，都属于巧克力家族。

首先，让我们创建一个名为巧克力的界面(家族)

```
public interface Chocolate 
{
    public void Eat();
} 
```

Enter fullscreen mode Exit fullscreen mode

接下来，让我们创建不同类型(实例)的巧克力。

```
public class Maltezers: Chocolate
{
    public void Eat() 
    {
        Console.WriteLine("Crunch!");
    }
}

public class LunchBar: Chocolate
{
    public void Eat()
    {
        Console.WriteLine("Crisp!");
    }
}

public class KitKat: Chocolate
{
    public void Eat()
    {
        Console.WriteLine("Have a break!");
    }
}

public class Aero: Chocolate
{
    public void Eat()
    {
        Console.WriteLine("Bubbles!");
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

我们需要一些简单的方法来识别它们，不要让我开始使用魔法字符串...

```
public enum ChocolateType 
{
    Maltezers,
    LunchBar,
    KitKat,
    Aero
} 
```

Enter fullscreen mode Exit fullscreen mode

现在轮到工厂了。就风格而言，我喜欢用族来命名创建对象的方法。这只是一个小小的开发者偏好。这个名字通常是一个名词，读起来像英语。

```
public class ChocolateFactory
{
    public Chocolate Chocolate(ChocolateType identifier)
    {
        switch(identifier)
        {
            case ChocolateType.Maltezers:
                return new Maltezers();
            case ChocolateType.LunchBar:
                return new LunchBar();
            case ChocolateType.KitKat:
                return new KitKat();
            case ChocolateType.Aero:
                return new Aero();
            default:
                throw new NotImplementedException();
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

现在你知道了。你想要什么？一个*奇巧*？当然可以，让我...

```
var factory = new ChocolateFactory();
var yourKitKat = factory.Chocolate(ChocolateType.KitKat); 
```

Enter fullscreen mode Exit fullscreen mode

这就是你要的——看起来、摸起来、尝起来绝对像巧克力的奇巧巧克力。它响应为巧克力，而你的调用代码将永远不知道它不是巧克力，将代码与实现 KitKat 解耦，并允许你对接口 chocolate 进行编码。