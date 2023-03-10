# C#中的委托方法。实际例子

> 原文：<https://dev.to/leanwit/delegate-methods-in-c-a-practical-example-5fde>

我正在阅读由 [CodelyTv](https://codely.tv) 创建的领域驱动设计 PHP [库](https://github.com/CodelyTV/php-ddd-skeleton)，这是一个使用这种方法创建新解决方案的模型，这时我发现了一些有趣的东西，可以在未来的场景中执行。我读了下一段代码:

```
use function Lambdish\Phunctional\repeat;

final class Repeater
{
    public static function repeat(callable $function, $quantity): array
    {
        return repeat($function, $quantity);
    }

    public static function repeatLessThan(callable $function, $max): array
    {
        return self::repeat($function, IntegerMother::lessThan($max));
    }

    public static function random(callable $function): array
    {
        return self::repeat($function, IntegerMother::lessThan(5));
    }
} 
```

首先，这段代码是一个在单元测试用例中使用的助手类。 [Repeater](https://github.com/CodelyTV/php-ddd-skeleton/blob/master/tests/src/Shared/Domain/Repeater.php) 类使用一个可调用函数作为参数创建一个随机对象列表，结果被添加到列表中 *N* 次。

在此之前，我们可以执行如下随机方法:

```
Repeater::random(CourseIdMother::creator());
Repeater::random(IntegerMother::random()); 
```

在第一行中，结果是一个包含由`CourseIdMother::creator()`产生的 *N* 个元素的列表，在第二行中，有一个包含 int 类型的 *N* 个元素的列表。它还存在另一个名为`repeatLessThan`的方法，通过这个方法我们可以指定我们想要存储到列表中的对象的最大数量。

回到我的介绍，我在读这段代码时，我认为这是一个解释我们如何使用委托或可调用方法的好场景。

# 委派方法-简介。

委托方法是一种引用类型变量，它保存对方法的引用，并且可以在运行时更改。它特别用于实现事件和回调方法。

定义和使用委托有三个步骤:

1.  我们代表的声明。
2.  实例化，创建委托的对象。
3.  调用，我们调用被引用的方法。

```
//Declaration
public delegate void WriterDelegate(string text);
class Program
{
    public static void Write(string text)
    {
        Console.WriteLine(text);
    }

    static void Main(string[] args)
    {
        //Instantiation
        WriterDelegate writerDelegate = new WriterDelegate(Write);

        //Invocation
        writerDelegate("Some example text.");
    }
} 
```

同样，C#有两个内置的委托:`Func<T>`和`Action<T>`

Action 是一种委托方法，它有多达 16 个参数，并且不返回任何结果。因此，我们可以将返回类型为 void 的方法赋给这个委托。

否则，Func 最多有 16 个参数，它返回一个结果。换句话说，我们只对结果类型不是 void 的方法使用 Func 委托。

我不想用理论来扩展这篇文章，但是我建议查看这个[文档](https://code-maze.com/csharp-delegates/#action<t>delegate)，以便理解我们如何以简单的方式使用 Action 和 Func 方法。

# 委托方法-示例。

借助 C#中的[泛型](https://code-maze.com/csharp-generics/)，我们可以创建一个类似的随机方法，如本文第一部分所示，使用 Func sending 作为参数。

```
private static IEnumerable<T> Repeat(Func<T> method, int quantity)
{
   for (int i = 0; i < quantity; i++)
   {
       yield return method();
   }
} 
```

使用`Repeat(Func<T> method, int quantity)`我们可以执行 *N 次*Func，并将结果存储到`IEnumerable<T>`中。全班`Repeater<T>`成绩:

```
using System;
using System.Collections.Generic;
using System.Linq;

public class Repeater<T>
{
    private static IEnumerable<T> Repeat(Func<T> method, int quantity)
    {
        for (int i = 0; i < quantity; i++)
        {
            yield return method();
        }
    }

    public static IEnumerable<T> RepeateLessThan(Func<T> method, int max)
    {
        return Repeat(method, IntegerMother.LessThan(max));
    }

    public static IEnumerable<T> Random(Func<T> method)
    {
        return Repeat(method, IntegerMother.LessThan(5));
    }
} 
```

它看起来与原始代码非常相似。最后，我们可以通过以下方式使用 helper `Repeater<T>`类:

```
Repeater<CourseId>.Random(CourseIdMother.Random);
Repeater<int>.Random(IntegerMother.Random);
Repeater<string>.RepeateLessThan(StringMother.Random,20); 
```

正如我们所见，该方法生成了一个执行`Func<T>`的对象列表。它总是返回契约中指示的相同类型的对象(第一个示例中的 CourseId)。

另一个常用的 delegate 例子是带有表达式的[存储库](https://code-maze.com/async-generic-repository-pattern/)模式。我们可以创建一个 Get 方法来执行由另一个方法定义的 where 语句，并返回一个 IQueryable 对象。

```
public abstract class RepositoryBase<T> : IRepositoryBase<T> where T : class
{
    protected RepositoryContext RepositoryContext { get; set; }

    public RepositoryBase(RepositoryContext repositoryContext)
    {
        this.RepositoryContext = repositoryContext;
    }

    public IQueryable<T> FindAll()
    {
        return this.RepositoryContext.Set<T>();
    }

    public IQueryable<T> FindByCondition(Expression<Func<T, bool>> expression)
    {
        return this.RepositoryContext.Set<T>()
            .Where(expression);
    }
} 
```

使用这种方法，我们可以将由 [LinQ](https://docs.microsoft.com/en-us/dotnet/csharp/programming-guide/concepts/linq/introduction-to-linq-queries) 表达式创建的 where 语句作为参数发送，如下:

```
await FindByCondition(o => o.Id.Equals(ownerId))             
             .SingleAsync();

await FindByCondition(o => o.Age > 10 && o.Name.Contains(ownerName))             
             .SingleAsync(); 
```

C#中的委托方法有许多用途和潜在的场景，但是本文展示了一个简单的例子来理解我们如何使用这种语言提供的工具。

# 推荐资源。

[微软-使用委托人](https://docs.microsoft.com/en-us/dotnet/csharp/programming-guide/delegates/using-delegates)
[为什么委托人](https://buildplease.com/pages/why-delegates/)
[代码迷宫-委托人](https://code-maze.com/csharp-delegates/)