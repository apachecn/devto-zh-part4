# 回顾 C# 7:引用增强

> 原文：<https://dev.to/csmacnz/looking-back-on-c-7-refs-enhancements-2cka>

[![Looking Back on C# 7: refs enhancements](img/4566ebb14f8e59bb44e657ef6b795806.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--TI-DMmkR--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://images.unsplash.com/photo-1530226406379-f84b9edd291b%3Fixlib%3Drb-1.2.1%26q%3D80%26fm%3Djpg%26crop%3Dentropy%26cs%3Dtinysrgb%26w%3D1080%26fit%3Dmax%26ixid%3DeyJhcHBfaWQiOjE2ODI3fQ)

随着 C# 8 的到来，我想这是一个很好的时机来反思一下这种语言最近的新增内容。有些很棒的改进你可能错过了，有些我真的很喜欢使用，有些我认为已经达到了规范使用状态，我认为这些都值得反思。

在系列的上一篇文章中，我们讨论了 out 变量[，但是还有一些其他与`ref`相关的增强。](https://blog.csmac.nz/looking-back-on-csharp7-out-variables)

希望值、指针、栈和堆的概念在概念层面上对你有意义。这些是使用和理解`ref`和`out`的关键概念。

简单回顾一下，堆栈内存是函数和函数调用堆栈的本地内存。对于每个方法调用，该内存在语义上被“推入”，并且在每次返回时被“弹出”(调用堆栈、堆栈溢出等)。堆是分配和存储对象的共享内存空间，由垃圾收集器操作。

值大多是堆栈分配的。它们被复制，这样设置一个值变量不会影响任何其他变量。基元类型和结构是值类型。引用类型是 C#中的类。类总是存储在堆上，类类型的所有变量都是指针或引用。将一个引用赋给一个新变量将指向堆上的同一个值。更改引用类型的字段值将反映在这两个变量中。(将值装箱到堆上也是另一回事。)

我们已经有了`out`,它允许调用者在当前堆栈上声明一个变量，它通过方法约定为您设置的地址(指针、引用或 ref)传递该变量。这从本质上给了我们一些来自堆栈分配值的引用类型的能力。当我们使用`ref`时，我们从堆分配的类类型中获得了引用传递的所有能力。我们实际上是说，调用者可以使用引用的现有值，如果需要的话，还可以为堆栈变量设置一个新值。基本上，`out`强加的所有限制都被取消了。一个 ref 可能不仅仅是一个栈变量，也可能是一个类上的一个字段，你想通过引用直接访问它，而不是不断地去引用它。

Pass by `ref`从一开始就出现在 C#中，但是和`out`参数一样，只在方法签名中有效。然而，从一开始，你就不能声明一个`ref`变量。

在 C# 7 中，`ref`已经被扩展到处理返回类型和变量。可以通过引用返回值。因此，如果返回的结果可以以一种有用的方式分配给某个东西，我们现在也有了`ref`变量类型。像大多数 C#语言特性一样，有内置的安全性。

*   您必须将`ref`关键字添加到方法签名和方法中的所有 return 语句中。
*   一个`ref`返回可以被分配给一个值变量(通过复制)或一个`ref`变量。
*   不能将标准方法返回值赋给`ref`局部变量。
*   你不能将一个`ref`返回给一个生存期没有超过方法执行期的变量。

这些规则确保了你的代码的安全性和可读性，也就是说，它清楚地表明了发生了什么。

为此，我将公然窃取微软的示例，因为我不想发明一个错误的示例。

```
// We declare the method as returning by reference (rather than copy)
public ref int Find(int number, int[] numbers)
{
    for (int i = 0; i < numbers.Length; i++)
    {
        if (numbers[i] == number)
        {
            // All returns must use the `ref` keyword
            return ref numbers[i]; // return the storage location, not the value
        }
    }
    // We can still throw an exception if necessary
    throw new IndexOutOfRangeException($"{nameof(number)} not found");
}

// arrays are already allocated to the heap and passed by reference rather than by value
// (see the `stackalloc` keyword for stack allocating arrays, though)
int[] array = { 1, 15, -39, 0, 7, 14, -12 };

// We have to use `ref` to call the method
// We choose to declare `place` as a reference
ref int place = ref Find(7, array); // aliases 7's place in the array
place = 9; // replaces 7 with 9 in the array
WriteLine(array[4]); // prints 9 
```

在上面的例子中，我们可以选择声明没有关键字`ref`的`place`,返回的值将被复制。然而，在这种情况下，`place = 9;`的赋值将覆盖本地副本，而不会修改原始数组。

为什么要使用这些通过引用传递的附加功能？通过避免堆栈和堆复制或者在某些算法中取消值的引用，可以实现巨大的性能增强。在这里，性能是游戏的名称。

在 C# 7.2 中，当两个操作数(`x`和`y`)都是引用时，条件运算符(`isTrue ? x : y`语法)现在可以计算出引用结果。

`ref var r = ref (arr != null ? ref arr[0] : ref otherArr[0]);`

在 7.2 中，我们还得到了`ref readonly`，它允许返回的引用不允许编译器强制修改。在需要获取最新值的情况下，例如在循环中，这可以节省不断解引用子字段的时间。同样，性能是目标用例。

第一个版本的`ref`变量是不可变的。无论你宣称他们指向的是什么，他们一生都在提及。在 C# 7.3 中，它们被更新了，所以你可以重用一个变量来指向一个不同的引用。

与`ref`相比，为了补充`out`限制的安全性，我们还使用了`in`关键字。

用`in`声明一个方法参数实质上使它成为一个只读引用。该方法不允许修改传入的值，但可以获得按引用传递而不是按值传递(复制)的所有好处。`in`关键字将使编译器确保该方法不被允许修改最初传入的值。如果有必要，它将创建一个防御性的卷影副本，以确保这是真的。

这与另一个新功能`readonly struct`配合得很好。将结构声明为只读意味着编译器将确保您确实是只读的。(比如会不允许`public int Foo { get; private set; }`。)你可以对任何你想再次引用这些结构之一的方法使用`in`关键字，以确保阅读代码时的清晰，但这也是由编译器强制执行的。

上面提到了防御阴影副本。语言和运行时不保证属性或方法的内部实现细节在契约中是不可变的，所以编译器会采取防御措施，在调用任何可能导致变异的东西之前进行复制。这样，语言保证了传递只读引用的期望，但在这个过程中可能不符合性能期望。作为开发人员，通过将类型改为 a `readonly struct`，编译器可以依赖这些保证，并且不会复制任何副本。如果该结构改变了任何内部状态，它就不会编译，所以我们在编译时和运行时有更强的保证。

这些功能当然是强大的功能，当你需要它们时，它们会很有用。但是像大多数高级特性一样，你可能会为了性能和优化而牺牲可读性。少量使用，但是可以先测量，然后撒进去再测量。