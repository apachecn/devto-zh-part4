# 面向操作的 C#

> 原文：<https://dev.to/techelevator/action-oriented-c-1373>

五年前，我到达了一个平台期。我的代码达到了一定的质量和灵活性水平，并停止了改进。以下是我如何利用函数式编程的各个方面来不断攀登的。

我的代码非常可靠，但是仍然有很多非常相似的代码，尽管尽可能积极地尝试消除重复。这并不是完全的重复，但是整个代码中清晰的模式使得维护变得比它应该有的更麻烦。

然后我发现了如何应用`Actions`和`Funcs`来进一步改进我的代码。

我们来看一个假设的例子:

```
// Associates Degree
if (resume.HasAssociatesDegree)
{
    try
    {
        resume.AssociatesDegreeScore = CalculateAssociatesDegreeScore();
    }
    catch (InvalidOperationException ex)
    {
        Console.WriteLine($"Could not calculate associates degree score: {ex.Message}");
        resume.AssociatesDegreeScore = 0;
    }
}
else
{
    resume.AssociatesDegreeScore = 0;
}

// Bachelors Degree
if (resume.HasBachelorsDegree)
{
    try
    {
        resume.BachelorsDegreeScore = CalculateBachelorsDegreeScore();
    }
    catch (InvalidOperationException ex)
    {
        Console.WriteLine($"Could not calculate bachelors degree score: {ex.Message}");
        resume.BachelorsDegreeScore = 0;
    }
}
else
{
    resume.BachelorsDegreeScore = 0;
}

// Masters Degree
if (resume.HasMastersDegree)
{
    try
    {
        resume.MastersDegreeScore = CalculateMastersDegreeScore();
    }
    catch (InvalidOperationException ex)
    {
        Console.WriteLine($"Could not calculate masters degree score: {ex.Message}");
        resume.MastersDegreeScore = 0;
    }
}
else
{
    resume.MastersDegreeScore = 0;
} 
```

Enter fullscreen mode Exit fullscreen mode

虽然这是一个人为的例子，但这说明了问题。在这个场景中，您有一个重复的模式:检查一个属性，然后执行一些自定义逻辑(如果该属性为 false，则不应调用这些逻辑)，然后将结果存储在对象的自定义属性中。

这是一个简单的小例程，但是重复是显而易见的，并且它拒绝提取方法，因为如果简历没有相关的学位，就不能调用`CalculateX`方法。

此外，假设发生了一个错误，需要更改代码。现在，您需要在 3 个地方进行相同的更改。如果你错过了一个，很可能会导致错误。此外，相似性诱使您进行*复制/粘贴驱动的开发*,这是一种反模式，如果不是所有需要修改的行都被修改了，这也是潜在的错误源。

* * *

我所学到的改变了我的方法，那就是传递 [Action](https://docs.microsoft.com/en-us/dotnet/api/system.action-1?view=netframework-4.8) 和 [Func](https://docs.microsoft.com/en-us/dotnet/api/system.func-2?view=netframework-4.8) 类型，通过赋予它们可配置的行为，让你的方法更加灵活。

`Action`是在没有返回类型的情况下调用的方法的通用签名。

例如，接受一个布尔和整数参数的签名应该是这样的:`Action<bool, int> myAction;`

除了返回一个值之外，`Func`与`Action`非常相似。返回值的类型是最后一个泛型类型参数。例如，`Func<bool, int>`会接受一个布尔值并返回一个整数。

因此，我们可以在我们的例子中使用一个`Func`来提取一个具有一些可配置行为的方法:

```
private decimal EvaluateEducation(
  bool hasAppropriateDegree, 
  string degreeName, 
  Func<decimal> calculateFunc)
{
    if (hasAppropriateDegree)
    {
        try
        {
            // Invoke the function provided and return its result
            return calculateFunc(); 
        }
        catch (InvalidOperationException ex)
        {
            Console.WriteLine($"Could not calculate {degreeName} score: {ex.Message}");
        }
    }

    return 0;
} 
```

Enter fullscreen mode Exit fullscreen mode

这将我们的调用代码改进为以下简短的片段:

```
resume.AssociatesDegreeScore = EvaluateEducation(resume.HasAssociatesDegree, 
  "associates", 
  () => CalculateAssociatesDegreeScore());

resume.BachelorsDegreeScore = EvaluateEducation(resume.HasBachelorsDegree, 
  "bachelors", 
  () => CalculateBachelorsDegreeScore());

resume.MastersDegreeScore = EvaluateEducation(resume.HasMastersDegree, 
  "masters", 
  () => CalculateMastersDegreeScore()); 
```

Enter fullscreen mode Exit fullscreen mode

这要简单得多，尽管语法有点难懂。我们在该方法的第三个参数中所做的是声明一个 *lambda 表达式*，类似于您用于 LINQ 查询的表达式。

如果你需要使用一个使用参数的函数(比如说，你有一个`Func<int, decimal>`签名，你可以把你的逻辑改成如下:
`(myInt) => myInt + CalculateMastersDegreeScore();`

* * *

虽然这是一个相当不自然的例子，但希望它说明了将`Func`和`Action`传递给各种方法的能力。这是*函数式编程*建立的基础，但是在小范围内，这对于面向对象编程也非常有帮助。

虽然这种语法使代码有点难以阅读，但可维护性方面的好处是真实的，引入重复相关缺陷的可能性也低得多。

试着做一个附带的项目或复制的特定区域，让我知道你的想法。