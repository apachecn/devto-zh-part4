# PHPUnit:从相同的模拟方法返回值并抛出异常

> 原文：<https://dev.to/ethanbray/phpunit-returning-values-and-throwing-exceptions-from-the-same-mocked-method-2fod>

最近我写了一个测试用例，在这个用例中，对一个方法的初始调用会返回值，而后续调用会抛出一个异常。我假设可以通过使用`will`、`willReturn`和`willThrowException`的组合来实现。

```
$mockFoo     = $this->createMock(Foo::class);
$mockFactory = $this->createMock(FooFactory::class);

$mockFactory
    ->expects($this->exactly(2))
    ->method('make')
    ->with('Foo', 'Bar')
    ->will($this->willReturn($mockFoo), $this->throwException(new BarException())); 
```

PHPUnit 只支持一个参数给`will`，因此这个异常从来不会被抛出。我惊讶地发现 PHPUnit 没有针对这个问题的文档解决方案，我转而求助于搜索 StackOverflow。我找到了[这个](https://stackoverflow.com/a/6286827)的答案，它让我步入了正轨。

与其创建`InvokedCount`匹配器并立即将其传递给`expects`，我们可以更早地创建它并将其赋给一个变量。然后我们可以用`willReturnCallback`替换我们的`will`方法，在回调中使用`$matcher`。`InvokedCount`提供了方法`getInvocationCount`，它允许我们跟踪方法`make`被调用了多少次。使用回调，我们可以分别处理每个调用。

```
$mockFoo     = $this->createMock(Foo::class);
$mockFactory = $this->createMock(FooFactory::class);
$matcher     = $this->exactly(2);

$mockFactory
    ->expects($matcher)
    ->method('make')
    ->with('Foo', 'Bar')
    ->willReturnCallback(function () use ($matcher, $mockFoo) {
        if ($matcher->getInvocationCount() === 1) {
            return $mockFoo;
        }

        throw new BarException();
    }); 
```

值得注意的是，这个解决方案不是对`willReturnOnConsecutiveCalls`的替代，只在你的模拟方法需要做的不仅仅是返回值的时候使用。