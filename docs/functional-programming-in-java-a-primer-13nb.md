# Java 中的 7 种函数式编程技术

> 原文：<https://dev.to/deepu105/functional-programming-in-java-a-primer-13nb>

*最初发布于[deepu . tech](https://deepu.tech/functional-programming-in-java-for-beginners/)T3。*

围绕函数式编程(FP)有很多炒作，很多酷孩子都在做，但它不是银弹。像其他编程范例/风格一样，函数式编程也有其优点和缺点，人们可能更喜欢一种范例而不是另一种。如果你是一名 Java 开发人员，想尝试函数式编程，不要担心，你不必学习面向函数式编程的语言，如 Haskell 或 Clojure(甚至 Scala 或 JavaScript，尽管它们不是纯粹的函数式编程语言)，因为 Java 已经涵盖了你，这篇文章就是为你准备的。

我不会深入所有函数式编程概念的细节，相反，我会把重点放在你可以用 Java 做的符合函数式编程概念的事情上。我也不会讨论函数式编程的利弊。

* * *

## 什么是函数式编程？

As per Wikipedia,

> 函数式编程是一种编程范式——一种构建计算机程序的结构和元素的风格——将计算视为数学函数的评估，并避免改变状态和可变数据。

因此，在函数式编程中，有两条非常重要的规则

*   **无数据突变**:表示一个数据对象在创建后不应该被改变。
*   **无隐式状态**:应避免隐藏/隐式状态。在函数式编程中，状态并没有被消除，相反，它变得可见和显式

这意味着:

*   **无副作用**:一个功能或操作不应该改变其功能范围之外的任何状态。也就是说，一个函数应该只返回一个值给调用者，而不应该影响任何外部状态。这意味着程序更容易理解。
*   **纯函数唯一**:函数代码是幂等的。函数应该只根据传递的参数返回值，不应该影响(副作用)或依赖全局状态。这样的函数对于相同的参数总是产生相同的结果。

除此之外，下面还有一些函数式编程的概念可以在 Java 中应用，我们将会进一步讨论这些概念。

*   [高阶函数](https://en.wikipedia.org/wiki/Higher-order_function)
*   [关闭](https://en.wikipedia.org/wiki/Closure_(computer_programming))
*   [阿谀奉承](https://en.wikipedia.org/wiki/Currying)
*   [递归](https://en.wikipedia.org/wiki/Recursion_(computer_science))
*   [懒惰评估](https://en.wikipedia.org/wiki/Evaluation_strategy)
*   [参照透明度](https://en.wikipedia.org/wiki/Referential_transparency)

使用函数式编程并不意味着要么全部要么什么都没有，你总是可以使用函数式编程概念来补充面向对象的概念，尤其是在 Java 中。无论您使用哪种范式或语言，只要有可能，就可以利用函数式编程的好处。这正是我们将要看到的。

* * *

## Java 中的函数编程

因此，让我们看看如何在 Java 中应用上面的一些函数式编程概念。我们将使用 Java 11，因为它目前是 LTS 版本。

### 一阶和高阶函数

一等函数(作为一等公民的函数)意味着你可以将函数赋给变量，将一个函数作为参数传递给另一个函数，或者从另一个函数返回一个函数。不幸的是，Java 不支持这一点，因此使得像闭包、currying 和高阶函数这样的概念不太方便编写。

Java 中最接近一级函数的是 [Lambda 表达式](https://www.geeksforgeeks.org/lambda-expressions-java-8/)。在`java.util.function`包下还有一些内置的功能接口，如`Function`、`Consumer`、`Predicate`、`Supplier`等，可以用于功能编程。

只有当一个函数接受一个或多个函数作为参数，或者返回另一个函数作为结果时，它才可以被认为是高阶函数。我们在 Java 中能得到的最接近高阶函数的方法是使用 Lambda 表达式和内置函数接口。

这不是执行高阶函数的最佳方式，但这是 Java 中的方式，在我看来也没那么糟糕。

```
public class HocSample {
    public static void main(String[] args) {
        var list = Arrays.asList("Orange", "Apple", "Banana", "Grape");

        // we are passing an array and an anonymous inner class instance of FnFactory as arguments to mapForEach method.
        var out = mapForEach(list, new FnFactory<String, Object>() {
            @Override
            public Object execute(final String it) {
                return it.length();
            }
        });
        System.out.println(out); // [6, 5, 6, 5]
    }

    // The method takes an array and an instance of FnFactory as arguments
    static <T, S> ArrayList<S> mapForEach(List<T> arr, FnFactory<T, S> fn) {
        var newArray = new ArrayList<S>();
        // We are executing the method from the FnFactory instance
        arr.forEach(t -> newArray.add(fn.execute(t)));
        return newArray;
    }

    @FunctionalInterface // this doesn't do anything it is just informative.
    public interface FnFactory<T, S> {
        // The interface defines the contract for the anonymous class
        S execute(T it);
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

幸运的是，实际上可以使用内置的`Function`接口和 lambda 表达式语法进一步简化上面的例子。

```
public class HocSample {
    public static void main(String[] args) {
        var list = Arrays.asList("Orange", "Apple", "Banana", "Grape");
        // we are passing the array and a lambda expression as arguments to mapForEach method.
        var out = mapForEach(list, it -> it.length());
        // This can be further simplified to "mapForEach(list, String::length);", I'm writing the expanded version for readability
        System.out.println(out); // [6, 5, 6, 5]
    }

    // The method takes an array and an instance of Function as arguments (we have replaced the custom interface with the built-in one)
    static <T, S> ArrayList<S> mapForEach(List<T> arr, Function<T, S> fn) {
        var newArray = new ArrayList<S>();
        // We are executing the method from the Function instance
        arr.forEach(t -> newArray.add(fn.apply(t)));
        return newArray;
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

使用这些概念和 lambda 表达式，我们可以编写如下的闭包和 currying

```
public class ClosureSample {
    // this is a higher-order-function that returns an instance of Function interface
    Function<Integer, Integer> add(final int x) {
        // this is a closure, i.e, a variable holding an anonymous inner class instance of the Function interface
        // which uses variables from the outer scope
        var partial = new Function<Integer, Integer>() {
            @Override
            public Integer apply(Integer y) {
                // variable x is obtained from the outer scope of this method which is declared as final
                return x + y;
            }
        };
        // The closure function instance is returned here
        return partial;
    }

    public static void main(String[] args) {
        ClosureSample sample = new ClosureSample();

        // we are currying the add method to create more variations
        var add10 = sample.add(10);
        var add20 = sample.add(20);
        var add30 = sample.add(30);

        System.out.println(add10.apply(5)); // 15
        System.out.println(add20.apply(5)); // 25
        System.out.println(add30.apply(5)); // 35
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

我们可以用下面的 lambda 表达式进一步简化这个问题

```
public class ClosureSample {
    // this is a higher-order-function that returns an instance of Function interface
    Function<Integer, Integer> add(final int x) {
        // The lambda expression is returned here as closure
        // variable x is obtained from the outer scope of this method which is declared as final
        return y -> x + y;
    }

    public static void main(String[] args) {
        ClosureSample sample = new ClosureSample();

        // we are currying the add method to create more variations
        var add10 = sample.add(10);
        var add20 = sample.add(20);
        var add30 = sample.add(30);

        System.out.println(add10.apply(5));
        System.out.println(add20.apply(5));
        System.out.println(add30.apply(5));
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

Java 中也有许多内置的高阶函数，例如下面是来自`java.util.Collections`
的排序方法

```
var list = Arrays.asList("Apple", "Orange", "Banana", "Grape");

// This can be simplified as "Collections.sort(list, Comparator.naturalOrder());", I'm writing the expanded version for readability
Collections.sort(list, (String a, String b) -> {
    return a.compareTo(b);
});

System.out.println(list); // [Apple, Banana, Grape, Orange] 
```

Enter fullscreen mode Exit fullscreen mode

Java 流 API 还提供了许多有趣的高阶函数，比如 forEach、map 等等。

### 纯函数

正如我们已经看到的，一个纯函数应该只根据传递的参数返回值，而不应该影响或依赖全局状态。在 Java 中可以做到这一点，除了一些涉及检查异常的情况。

这相当简单，就拿下面这个纯函数来说吧。对于给定的输入，它总是返回相同的输出，并且它的行为是高度可预测的。如果需要，我们可以安全地缓存该方法。

```
public static int sum(int a, int b) {
    return a + b;
} 
```

Enter fullscreen mode Exit fullscreen mode

如果我们在这个函数中添加额外的一行，行为将变得不可预测，因为它现在具有影响外部状态的副作用。

```
static Map map = new HashMap<String, Integer>();

public static int sum(int a, int b) {
    var c = a + b;
    map.put(a + "+" + b, c);
    return c;
} 
```

Enter fullscreen mode Exit fullscreen mode

所以尽量保持你的函数简单明了。

### 递归

函数式编程更喜欢递归而不是循环。在 Java 中，这可以通过使用流 API 或编写递归函数来实现。让我们看一个计算一个数的阶乘的例子。

我还使用 [JMH](http://tutorials.jenkov.com/java-performance/jmh.html) 对这些进行了基准测试，并在下面提到了纳秒/操作

在传统的迭代方法中:

```
public class FactorialSample {
    // benchmark 9.645 ns/op
    static long factorial(long num) {
        long result = 1;
        for (; num > 0; num--) {
            result *= num;
        }
        return result;
    }

    public static void main(String[] args) {
        System.out.println(factorial(20)); // 2432902008176640000
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

使用下面的递归也可以做到这一点，递归在函数式编程中很受欢迎。

```
public class FactorialSample {
    // benchmark 19.567 ns/op
    static long factorialRec(long num) {
        return num == 1 ? 1 : num * factorialRec(num - 1);
    }

    public static void main(String[] args) {
        System.out.println(factorialRec(20)); // 2432902008176640000
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

递归方法的缺点是，与迭代方法相比，它在大多数情况下会更慢(我们的目标是代码的简单性和可读性)，并且可能会导致堆栈溢出错误，因为每个函数调用都需要作为一个帧保存到堆栈中。为了避免这种尾部递归是优选的，特别是当递归进行太多次时。在尾递归中，递归调用是函数执行的最后一件事，因此编译器不需要保存函数堆栈帧。大多数编译器可以像优化迭代代码一样优化尾部递归代码，从而避免性能损失。不幸的是，Java 编译器不做这种优化:(

现在使用尾部递归，同样的函数可以写成如下，但是 Java 并没有对此进行优化，尽管有[的变通方法](https://blog.knoldus.com/tail-recursion-in-java-8/)，它在基准测试中仍然表现得更好。

```
public class FactorialSample {
    // benchmark 16.701 ns/op
    static long factorialTailRec(long num) {
        return factorial(1, num);
    }

    static long factorial(long accumulator, long val) {
        return val == 1 ? accumulator : factorial(accumulator * val, val - 1);
    }

    public static void main(String[] args) {
        System.out.println(factorialTailRec(20)); // 2432902008176640000
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

我们也可以使用 Java 流库进行递归，但是目前它比普通的递归要慢。

```
public class FactorialSample {
    // benchmark 59.565 ns/op
    static long factorialStream(long num) {
        return LongStream.rangeClosed(1, num)
                .reduce(1, (n1, n2) -> n1 * n2);
    }

    public static void main(String[] args) {
        System.out.println(factorialStream(20)); // 2432902008176640000
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

为了可读性和不变性，在编写 Java 代码时考虑使用 stream API 或递归，但是如果性能很重要或者迭代次数很大，则使用标准循环。

### 懒评

惰性求值或非严格求值是将表达式的求值延迟到需要时进行的过程。一般来说，Java 会进行严格的求值，但是对于像`&&`、`||`和`?:`这样的操作数，它会进行惰性求值。在编写 java 代码时，我们可以利用这一点进行惰性评估。

以 Java 急切地评估一切为例。

```
public class EagerSample {
    public static void main(String[] args) {
        System.out.println(addOrMultiply(true, add(4), multiply(4))); // 8
        System.out.println(addOrMultiply(false, add(4), multiply(4))); // 16
    }

    static int add(int x) {
        System.out.println("executing add"); // this is printed since the functions are evaluated first
        return x + x;
    }

    static int multiply(int x) {
        System.out.println("executing multiply"); // this is printed since the functions are evaluated first
        return x * x;
    }

    static int addOrMultiply(boolean add, int onAdd, int onMultiply) {
        return (add) ? onAdd : onMultiply;
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

这将产生下面的输出，我们可以看到两个函数总是被执行

```
executing add
executing multiply
8
executing add
executing multiply
16 
```

Enter fullscreen mode Exit fullscreen mode

我们可以使用 lambda 表达式和高阶函数将它重写为一个延迟求值的版本

```
public class LazySample {
    public static void main(String[] args) {
        // This is a lambda expression behaving as a closure
        UnaryOperator<Integer> add = t -> {
            System.out.println("executing add");
            return t + t;
        };
        // This is a lambda expression behaving as a closure
        UnaryOperator<Integer> multiply = t -> {
            System.out.println("executing multiply");
            return t * t;
        };
        // Lambda closures are passed instead of plain functions
        System.out.println(addOrMultiply(true, add, multiply, 4));
        System.out.println(addOrMultiply(false, add, multiply, 4));
    }

    // This is a higher-order-function
    static <T, R> R addOrMultiply(
            boolean add, Function<T, R> onAdd,
            Function<T, R> onMultiply, T t
    ) {
        // Java evaluates expressions on ?: lazily hence only the required method is executed
        return (add ? onAdd.apply(t) : onMultiply.apply(t));
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

输出如下，我们可以看到只执行了所需的功能

```
executing add
8
executing multiply
16 
```

Enter fullscreen mode Exit fullscreen mode

### 类型系统

Java 有一个强大的类型系统，随着关键字`var`的引入，它现在也有了相当不错的类型推断。与其他函数式编程语言相比，唯一缺少的是 case 类。有关于未来 Java 版本的[值类](http://cr.openjdk.java.net/~jrose/values/values-0.html)和 case 类的提议。希望他们能成功。

### 参照透明

来自维基百科:

> 函数式程序没有赋值语句，也就是说，函数式程序中的变量值一旦定义就永远不会改变。这消除了任何副作用的可能性，因为任何变量都可以在任何执行点用它的实际值替换。因此，函数式程序在引用上是透明的。

不幸的是，在 Java 中限制数据突变的方法并不多，但是通过使用纯函数，通过使用我们前面看到的其他概念明确地避免数据突变和重新分配，这是可以实现的。对于变量，我们可以使用 [`final`](https://www.geeksforgeeks.org/final-keyword-java/) 关键字，这是一个非访问修饰符，以避免通过重新分配而发生突变。

例如，下面的代码在编译时会产生一个错误

```
final var list = Arrays.asList("Apple", "Orange", "Banana", "Grape");

list = Arrays.asList("Earth", "Saturn"); 
```

Enter fullscreen mode Exit fullscreen mode

但是，当变量持有对其他对象的引用时，这将无济于事，例如，无论最后一个关键字是什么，下面的变异都将起作用。

```
final var list = new ArrayList<>();

list.add("Test");
list.add("Test 2"); 
```

Enter fullscreen mode Exit fullscreen mode

`final`关键字允许被引用变量的内部状态变异，因此从函数式编程的角度来看`final`关键字只对常量和捕捉重赋值有用。

### [数据结构](https://en.wikipedia.org/wiki/Purely_functional_data_structure)

当使用函数式编程技术时，鼓励使用函数式数据类型，如堆栈、映射和队列。因此，在函数式编程中，作为数据存储，映射比数组或散列集更好。

* * *

## 结论

对于那些试图在 Java 中应用一些函数式编程技术的人来说，这只是一个介绍。在 Java 中可以做更多的事情，Java 8 增加了许多 API，使得用 Java 进行函数式编程变得容易，比如流 API、可选接口、函数式接口等等。正如我前面说过的，函数式编程不是银弹，但它提供了许多有用的技术，使代码更容易理解、维护和测试。它可以与命令式和面向对象的编程风格完美共存。事实上，我们都应该利用一切最好的东西。

这是我在 meetup 上展示的一个视频，涵盖了这个内容。

[https://www.youtube.com/embed/5dBw4madIpo](https://www.youtube.com/embed/5dBw4madIpo)

* * *

我希望你觉得这是有用的。如果你有任何问题，或者如果你认为我错过了什么，请添加评论。

如果你喜欢这篇文章，请留下赞或评论。

你可以在 [Twitter](https://twitter.com/deepu105) 和 [LinkedIn](https://www.linkedin.com/in/deepu05/) 上关注我。