# 可选择的....还有什么？

> 原文：<https://dev.to/brianverm/optional-what-else-42p0>

在某些方面，可选是 Maybe monad 的 Java 实现。不要被“M”字吓到。它只是一种处理特定类型情况的封装，在这种情况下，可能是空值。可以把它看作是一个包装器，强制用户检查值是否存在。

当以声明的方式使用一个可选的，我们能够在它上面执行一个映射函数。仅当可选的填充了值时，才会执行与映射一起提供的 lambda。

```
public void execute() {
   Optional<String> maybeString = Optional.of("foo");
maybeString.map(this::runIfExist);
}

private String runIfExist(String str) {
   System.out.println("only run if optional is filled ");
   return str;
} 
```

Enter fullscreen mode Exit fullscreen mode

当运行`execute()`方法时，控制台输出显然是:

```
only run if optional is filled 
```

Enter fullscreen mode Exit fullscreen mode

如果我们将 maybeString 的代码改为 Optional.empty()并再次执行，如预期的那样，什么都不会发生。

### 另类流量

这很好，但是当可选的为空时，当我们想要一个可选的流时，我们可以从`else()`、`elseGet()`和`elseThrow()`中选择。对于最后一个，我们可以抛出一个异常，但是如果我们想做一些不同的事情呢？

直观地说，下面的代码看起来很明显。

```
public void execute() {
   Optional<String> maybeString = Optional.of("foo");
   String newString = maybeString
           .map(this::runIfExist)
           .orElse(runIfEmpty());
   System.out.println(newString);
}

private String runIfExist(String str) {
   System.out.println("only run if optional is filled ");
   return str;
}

private String runIfEmpty() {
   System.out.println("only run if empty");
   return "empty";
} 
```

Enter fullscreen mode Exit fullscreen mode

通过阅读代码，我们可能期望`newString`将被赋予值`"foo"`，因为`maybeString`是包含值的可选值。当运行`execute()`方法时，您的控制台中将出现以下输出:

```
only run if optional is filled
only run if empty
foo 
```

Enter fullscreen mode Exit fullscreen mode

我们仍然可以断定`newString`将以值`"foo"`结束。我们在这里可以看到的另一件事是，字符串`"only run if empty"`也被打印到控制台上。那是我第一眼没想到的。所以即使可选的包含一个值，在`orElse()`中的方法仍然被执行。

当我们改变`execute()`方法，用`orElseGet()`代替`orElse()`的时候，我们还需要把对 runIfEmpty()的方法调用改成供应商:

```
public void execute() {
   Optional<String> maybeString = Optional.of("foo");
   String newString = maybeString
           .map(this::runIfExist)
           .orElseGet(() -> runIfEmpty());
   System.out.println(newString);
} 
```

Enter fullscreen mode Exit fullscreen mode

如果我们现在运行 execute()方法，我们会看到以下内容:

```
only run if optional is filled
foo 
```

Enter fullscreen mode Exit fullscreen mode

当使用这个实现时，我们看到`orElseGet()`中的供应商没有被执行。直觉上，这是我在使用`orElse()`时通常期望的行为

### 结论

当使用`map()`给一个变量赋值时，当可选的是`empty`时，使用`orElse()`给一个默认值不会有任何问题。你只需要确保`orElse()`中的代码绝对没有副作用。事实上，`orElse()`方法的目的是分配一个默认值，仅此而已。

另一方面，如果您需要基于可选的来控制行为，您应该使用`orElseGet()`构造。您可能不希望无论如何都要执行您的替代流，不管可选的。

由于声明式编程的本质，这种混淆非常容易。此外，您只有在实际执行代码时才能看到结果。所以，当然，我现在可以提倡编写适当的测试代码(你应该这样做)，但是最好事先知道两者的区别。这种差别很微妙，但如果你没有意识到，它会产生很大的影响。