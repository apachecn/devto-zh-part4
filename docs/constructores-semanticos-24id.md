# 语义构造函数

> 原文：<https://dev.to/mangelsnc/constructores-semanticos-24id>

[![](img/9750774d3a9eb03bfb1d2a89fba86804.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--fdcGEUWr--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2000/1%2AEX7vNErwk_YeSEz1YUTQkw.png)

今天，我们将讨论一种将显着提高我们守则质量和可读性的做法。

### 说明

语义构造函数或**命名构造函数**是一种小反应物，当构造函数需要知道构造函数的实现细节时，即消除构造函数的复杂性。

例如，假设以下代码:

这里我们看到一个代表价格的小[价值对象](https://martinfowler.com/bliki/ValueObject.html)。value object 由两个属性组成:要使用的数量和货币。

安装后，此值对象要求我们了解其实施的详细信息，如交易方货币和非交易方货币。

### 实施

为了应用**命名建造商**的反应器，我们只需遵循几个步骤:

*   将构造函数转换为专用

*   创建一个或多个静态方法，这些方法适当地使用构造函数返回新对象。

*   为新静态方法命名，以描述其意图。

*   将旧的构造函数调用替换为新的静态方法调用

*   验证测试是否通过。

我们来看看这个反应器的一个例子:

如您所见，该构造函数已成为私有构造函数，并出现了三种新方法:

*   创建价格

*   createPriceInPounds

*   createPriceInEuros

使用起来很简单:

```
$poundsPrice = Price::createPriceInPounds(22.5);
$dollarsPrice = Price::createPriceInDollars(22.5);
$eurosPrice = Price::createPriceInEuros(22.5); 
```

现在，value object 的公共接口列出了创建它所支持的价格的各种方法，因此程序员不必知道其实现的内部详细信息。

### 结论

这仍然是 Factory Method 模式的实现变体，其中构造函数方法是在同一类中实现的，因此必须是静态的。

其应用确实简单，优点显而易见。在使用静态方法构建对象时，可能会认为这些类的可测试性会受到损害，但由于其使用范围，通常不会产生问题。我解释一下:测试这些方法不需要(通常)使用静态方法的 mocks 或 stubs，这是我们可能遇到的最关键的情况。

为了测试这些方法，我们可以简单地依靠:

*   返回相应类中的对象

*   如果需要发布，则抛出异常

*   通过*getter*访问相应属性时，我们传递给构造函数的信息将正确返回

还有另一种用法，即**命名构造函数**是有用的，而且是在我们希望有多个构造函数时，显然得到不同的参数。这在 Java 等支持构造函数开销的语言中很容易做到，但 PHP 不允许这样做。

解决方法是创建新的语义构造函数，使我们能够使用不同的参数实现不同的构造函数。

让我们来看一个案例:

我们在此嵌入了仅包含浮点值的第二个值对象 *Amount* 。

如果我们希望我们的价值对象*价格*可以从一个对象*量*构造，PHP 中我们不能使构造函数过载以添加第二个构造函数，该构造函数代替*浮动*作为参数之一

*   createPriceInPoundsFromAmount

*   createPriceInDollarsFromAmount

*   createPriceInEurosFromAmount

可以看出，方法的名称仍然清楚地反映了其意图。