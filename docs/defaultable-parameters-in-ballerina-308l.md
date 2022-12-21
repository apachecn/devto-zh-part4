# 芭蕾舞演员的默认参数

> 原文：<https://dev.to/lankavitharana/defaultable-parameters-in-ballerina-308l>

这是我在 dev.to 上的第二篇文章，实际上，我们最近发布了 1.0 版本，我有了一些空闲时间来写博客。

因此，让我们深入了解这个很酷的芭蕾舞演员功能。有多少次，你想知道如何调用一个方法，以及向该函数传递什么？当然，您不应该传递什么，但是有些情况下，您只关心几个参数，而不知道如何处理其余的参数。就我个人而言，我多次面临这种情况。芭蕾舞女演员提供了一个很酷的方式来处理这种情况。基本上，函数开发人员可以为参数提供默认值，以便使用和调用函数的人可以选择不为该参数提供值。

让我们举一个例子来进一步澄清这一点。下面是一个芭蕾舞函数，它有两个`string`参数，但第二个参数是可选参数。

```
public function encode(string content, string charset = "UTF-8") {

} 
```

因此，当调用方法时，您可以简单地选择完全忽略该参数，只在必要时重写，调用示例如下

```
public function main() {
    // Invoke with first parameter, second will get defaulted to "UTF-8"
    encode("example");

    // Invoke with both parameters 
    encode("example", "UTF-16");

    // Invoke as named parameters
    encode("example", charset = "UTF-16");

    // Invoke as named parameters
    encode(content = "example", charset = "UTF-16");

    // Invoke as named parameters
    encode(charset = "UTF-16", content = "example");

} 
```

如果您已经注意到，我们可以使用这两种类型的调用，其中参数到参数的映射是从调用中参数的索引推导出来的(第一个参数到第一个参数，依此类推)，并且您可以将参数作为命名值传递。在将它们作为命名值传递的情况下，顺序并不重要。酷吧？:)

所以接下来的问题是我们在多大程度上支持这个？只有简单的文字可以用作默认值吗？不，您可以使用任何类型的表达式作为默认值。下面是一个调用函数作为参数默认值的例子。

```
import ballerina/io;

public function main() {
    // invoking without value
    invocationAsDefault();

    // invoking with value
    invocationAsDefault(abc(10));

}

public function invocationAsDefault(int asyncVal = abc(8)) {
    io:println(asyncVal);
}

public function abc(int a) returns int {
    return a + 5;
} 
```

另一件很酷的事情是，当你组合这些表达式时，你可以在这些表达式中使用以前的参数值，下面是一个双参数函数，其中第一个参数的值用于计算第二个参数的默认值。

```
import ballerina/io;

public function main() {
    // Invoke without values
    paramExample(5);

    // Invoke with values
    paramExample(5, 20);
}

public function paramExample(int a, int b = a + abc(a)) {
    io:println(a + b);
}

public function abc(int a) returns int {
    return a + 5;
} 
```

这种行为在所有情况下都是相似的(甚至 extern 和 interop 函数也可以有默认参数)

你可能知道，芭蕾舞演员以非阻塞的方式工作，这是内部的事情，但实际上，这意味着芭蕾舞演员不会不必要地阻塞线程。我在这里提到这一点，因为我想强调的是，您甚至可以将非阻塞调用作为参数默认值。

注意-这篇文章是为芭蕾舞者版本 1.0.0 写的。
芭蕾舞演员官方网站-[https://v1-0 . Ballerina . io](https://v1-0.ballerina.io)

芭蕾舞演员还有很多其他很酷的特点。我会写更多关于这些功能的博客文章，但目前，请给芭蕾舞演员一个回旋，看看你是否已经这样做了..:)并且不要忘记提供反馈:)因为我们仍在发展中，并且希望得到反馈...