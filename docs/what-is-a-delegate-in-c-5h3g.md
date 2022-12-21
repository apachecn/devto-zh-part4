# C#中的委托是什么？

> 原文：<https://dev.to/bradwellsb/what-is-a-delegate-in-c-5h3g>

[来源](https://wellsb.com/csharp/advanced/how-to-use-delegates-in-csharp/)

C#中的委托本质上是指向另一个方法的指针。它允许你的程序在方法和类之间传递函数，就像传递变量一样。

简单地说，委托就像一个指向方法的变量。具体来说，委托类型保存对具有特定返回类型和特定输入参数类型的方法的引用。

## 如何使用委托人

### 一个微不足道的例子

为了开始理解委托，我们将通过一个简单的例子。首先创建一个新的 C#控制台应用程序。在程序类中，您将如下声明一个委托:

```
delegate int MathDelegate(int x, int y); 
```

这个委托可以用来引用任何既接受类型为`int`的两个输入参数又返回数据类型为`int`的方法。例如，委托可能指向以下两种方法中的任何一种:

```
public static int AddNumbers(int x, int y)
{
    return x + y;
}

public static int MultiplyNumbers(int x, int y)
{
    return x * y;
} 
```

注意到`AddNumbers()`和`MultiplyNumbers()`都接受两个整数参数并返回一个整数，就像委托`MathDelegate`的声明一样。现在，在`static void Main()`方法中，您可以实例化委托并将其指向这两个方法中的任何一个。

```
static void Main(string[] args)
{
    MathDelegate myDelegate = AddNumbers;

    Console.WriteLine("Result: {0}", myDelegate(2, 4));
    Console.ReadLine();
} 
```

在这里，我们声明了一个类型为`MathDelegate`的新变量`myDelegate`，并给它分配了一个方法`AddNumbers()`。我们用与 C#中其他数据类型和变量相同的方式来实现这一点。现在，我们可以引用`myDelegate`，就像我们引用`AddNumbers()`方法一样。我们将向它传递两个整数参数，并期望它返回一个`int`。

这个例子有些琐碎，因为我们可以很容易地引用命名的方法，而不是将方法分配给委托。然而，开始理解如何使用委托是一项重要的工作。接下来，我们将扩展这个例子，开始看看 C#委托的威力。

### 一个不平凡的例子

在上面的例子中，委托声明是在与方法定义和执行调用相同的类中进行的。然而，委托的一个有用特性是，委托不关心它所引用的对象的类。委托也可以在另一个类中或者在整个类之外公开声明。这将允许您的程序在类之间传递方法，而不必将这些方法暴露在定义它们的类之外。

让我们通过在所有类之外的名称空间中声明委托来扩展上面的例子。

```
delegate int MathDelegate(int x, int y); 
```

接下来，创建一个名为`Calculations`的类，它列出了可能的数学运算。请注意，这些操作中的每一个都与上面声明的委托签名相匹配。

```
class Calculations
{
    public static int AddNumbers(int x, int y)
    {
        return x + y;
    }

    public static int MultiplyNumbers(int x, int y)
    {
        return x * y;
    }
} 
```

现在，创建另一个名为`MathClass`的类，它包含一个名为`DoMath`的方法。这个方法需要执行在`Calculations`类中定义的操作之一，但是它不知道是哪一个。事实上，`MathClass`中没有一个元素知道要执行哪个操作。我们如何确保调用正确的`Calculation`方法？我们可以简单地将委托传递给`DoMath`方法。这个委托将保存对适当的`Calculation`方法的引用。

```
class MathClass
{
    public static int DoMath(int x, int y, MathDelegate math)
    {
        return math(x, y);
    }
} 
```

最后，在我们的`Program`类的`Main()`方法中，我们可以实例化`MathDelegate`对象，并以编程方式为委托类型的新实例分配一个方法。然后它将把这个委托作为参数传递给`DoMath()`方法，这样程序就知道要执行哪个计算。

```
class Program
{
    static void Main(string[] args)
    {
        MathDelegate myDelegate = Calculations.AddNumbers;
        Console.WriteLine("Result: {0}", MathClass.DoMath(2, 4, myDelegate));
        Console.ReadLine();
    }
} 
```

在第 30 行，我们将委托分配给了`Calculations`类的`addNumbers()`方法。然后，我们使用[复合字符串格式化](https://wellsb.com/csharp/beginners/csharp-string-interpolation-vs-composite-formatting/)技术将结果直接写入控制台。

注意，我们已经向`MathClass`的`DoMath()`方法传递了三个参数——我们希望操作的两个整数和一个委托，它包含了对我们希望使用的方法的引用。我们的委托可以指向`Calculations`类中任何相同签名的操作，我们将永远不必对`MathClass`或它的任何方法做任何改变。`MathClass`与`Calculations`类有效隔离。它不知道任何底层逻辑，也不关心。

## 还可以在哪里使用委托？

委托是扩展现有代码的强大工具。正如您在上面的例子中看到的，它们可以用来调用一个方法，而不需要事先知道实际的方法名，甚至不知道该方法属于哪个类。因为它们允许您在类之间透明地传递方法，所以您也可以使用它们来编写回调和处理事件。

委托在具有重要用户界面的程序中非常有用，例如 WPF 应用程序，在这种程序中，您需要将一些值从后端进程传递到 UI 线程。在这种情况下，可以使用委托将消息或值返回给 UI 线程，而不会违反标准编码原则。

最后，需要注意的是，可以使用+或+=运算符对委托进行多点传送。这意味着单个委托可以同时引用多个方法，假设每个方法与该委托具有相同的签名。例如，单个委托可能用于调用控制台记录器方法和文件记录器方法。

[来源](https://wellsb.com/csharp/advanced/how-to-use-delegates-in-csharp/)