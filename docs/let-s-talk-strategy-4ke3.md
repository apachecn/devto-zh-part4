# 让我们谈谈战略模式

> 原文：<https://dev.to/htissink/let-s-talk-strategy-4ke3>

### Porygon

Porygon 是一个普通类型的口袋妖怪，完全由*计算机代码*创造。它可以将自己转换成代码在网络空间传播。它有两个招牌动作，*转换*和*转换 2* 。在口袋妖怪战斗中，这些会改变 Porygon 的类型。所以 Porygon 是一些在战斗中(在**运行时**)改变其类型(或**执行**)的代码。这实质上就是**战略模式**。

## 动态调度

**策略模式**建立在这个*漂亮的*小东西上，叫做动态调度。**动态分派**是指程序决定在*运行时*执行什么代码。动态调度的反义词是**静态调度**。这是当一个程序知道它将在*编译时*执行什么代码。

## 功能正常

这种模式构成了*函数式编程*的基础部分——在运行时创建和使用**函数族**的能力对于编写好的函数式代码至关重要。一系列函数共享相同的**类型——签名**——这意味着它们接受相同的参数，并返回相同的类型。

策略模式有许多不同的变体。将讨论两个主要的实现。

第一个是我最喜欢的。它是代码量最少的*最整洁的*。为此，我们将构建一个简单的计算器。

## 这是计算出来的

首先，让我们定义功能。

```
public enum Operation
{
    Addition,
    Subtraction,
    Division,
    Multiplication
} 
```

Enter fullscreen mode Exit fullscreen mode

太好了，现在功能已经定义好了，使用 *Func* 委托和*字典*将会产生一点功能。

```
var Operations = new Dictionary<Operation, Func<int, int, int>>
{
    { Operation.Addition, (x, y) => x + y },
    { Operation.Subtraction, (x, y) => x - y },
    { Operation.Division, (x, y) => x / y },
    { Operation.Multiplication, (x, y) => x * y }
}; 
```

Enter fullscreen mode Exit fullscreen mode

在运行时，程序(或用户)可以决定调用哪个操作。每个函数都属于同一个系列，具有**类型签名** (int，int) - > int。一个函数字典给出了一个有趣的*语法糖*，用它来添加两个数字(在运行时)，如下:

```
var result = Operations[Operation.Addition](1, 2) // result = 3 
```

Enter fullscreen mode Exit fullscreen mode

更正式地说，这些函数可以在运行时根据用户选择的操作来调用，并允许根据字典中的内容来添加和删除功能。

## 色彩让我疯狂

接下来，我们将看看使用*接口*实现的策略模式。为此，我们将制作一个简单的形状绘制应用程序。假设应用程序已经可以绘制形状，形状将使用策略模式进行着色和着色。首先，为形状创建一个界面。

```
public interface IShape
{
    string Color { get; set; }
    string Shade { get; set; }
} 
```

Enter fullscreen mode Exit fullscreen mode

很好，现在创建一个形状。

```
public class Circle: IShape
{
    public string Color { get; set; }
    public string Shade { get; set; }
} 
```

Enter fullscreen mode Exit fullscreen mode

*用油脂烹饪！*现在让我们把重点放在策略上——着色和阴影。

```
public interface IColoring
{
    void ColorAndShade(IShape shape);
}

public class LightGreen : IColoring
{
    public void ColorAndShade(IShape shape)
    {
        shape.Color = "Green";
        shape.Shade = "Light";
    }
}

public class DarkOrange : IColoring
{
    public void ColorAndShade(IShape shape)
    {
        shape.Color = "Orange";
        shape.Shade = "Dark";
    }
}

public class MediumBlue : IColoring
{
    public void ColorAndShade(IShape shape)
    {
        shape.Color = "Blue";
        shape.Shade = "Medium";
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

所以我们现在有一个阴影和着色的策略，我们有三种颜色和阴影。有两种方法可以解决这个问题。

### 1。策略属性

稍微修改一下 shape 类。

```
public interface IShape
{
    string Color { get; set; }
    string Shade { get; set; }
    IColoring Filling { get; set; }
    void Fill();
}

public class Circle: IShape
{
    public string Color { get; set; }
    public string Shade { get; set; }
    public IColoring Filling { get; set; }
    public void Fill() => Filling?.ColorAndShade(this);
} 
```

Enter fullscreen mode Exit fullscreen mode

现在，创建一个圆，它可以在运行时着色和着色

```
var circle = new Circle();
circle.Filling = new DarkOrange();
circle.Fill(); // Circle is new Dark Orange 
```

Enter fullscreen mode Exit fullscreen mode

### 2。策略工厂

```
public enum ShadeColor
{
    LightGreen,
    DarkOrange,
    MediumBlue
}

public static class ColoringStrategy
{
    public static IColoring Coloring(ShadeColor coloring)
    {
        switch (coloring)
        {
            case ShadeColor.DarkOrange:
                return new DarkOrange();
            case ShadeColor.LightGreen:
                return new LightGreen();
            case ShadeColor.MediumBlue:
                return new MediumBlue();
            default:
                throw new NotImplementedException();
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

像这样创建一个圆并涂上颜色，

```
var circle = new Circle();
var coloring = ColoringStrategy.Coloring(ShadeColor.DarkOrange);
coloring.ColorAndShade(circle); 
```

Enter fullscreen mode Exit fullscreen mode

### 在关闭

这是一个微不足道的例子，但它很容易说明这一点。类似的实现可以用来选择正确的排序算法，或者过滤出不同的对象进行修改。策略可以以多种不同的方式使用，以满足**动态调度**的需求。

策略是 *rad* 的，它们有助于从调用代码中分离出执行代码的复杂性。在第一种情况下，所有的代码关心的是类型签名，在第二种情况下，它是接口。在这两种情况下，代码都是从实际执行中分离出来的，从而产生更干净、更易于维护的代码。