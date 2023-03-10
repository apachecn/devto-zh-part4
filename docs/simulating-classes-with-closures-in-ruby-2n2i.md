# 用 Ruby 中的闭包模拟类

> 原文：<https://dev.to/victorhazbun/simulating-classes-with-closures-in-ruby-2n2i>

假设您想要构建一个非常简单的计数器程序。计数器程序可以执行以下操作:

*   获取计数器的当前值。
*   递增计数器。
*   递减计数器。

这是大多数类的本质:检索和修改数据。下面是一个计数器类的可能实现:

```
Class Counter

    def initialize
        @x = 0
    end

    def get_x
        @x  
    end

    def increment
        @x += 1
    end

    def decrement
        @x -= 1 
    end
end

# Here is a sample run in irb:

    >> c = Counter.new
    => #<Counter:0x007f9335939848 @x=0>
    >> c.increment
    => 1
    >> c.increment
    => 2
    >> c.get_x
    => 2
    >> c.decrement
    => 1
    >> c.decrement
    => 0
    >> c.decrement
    => -1 
```

Enter fullscreen mode Exit fullscreen mode

这个例子不应该有任何令人惊讶的地方。所以让我们添加一些约束，想象一下如果你没有创建类的能力。你还能写一个计数器程序吗？有了兰姆达斯，你绝对可以。创建一个名为 lambda_counter.rb 的新文件，并用下面的代码填充它:

```
Counter = lambda do # 1 

    x = 0 # 2

    get_x = lambda { x } # 3

    increment = lambda { x += 1 } # 4

    decrement = lambda { x -= 1} # 5

    {get_x: get_x, increment: increment, decrement: decrement} # 6
end # 8 
```

Enter fullscreen mode Exit fullscreen mode

这里，计数器是λ。第 2 行声明了 x，即计数器的状态，并将其初始化为零。第 3 行创建了一个 lambda，它返回计数器的当前状态。第 4 行和第 5 行分别通过增加或减少 x 的值来修改计数器的状态。

现在你应该很清楚，x 是自由变量。最后，在第 6 行，最外面的 lambda 的返回结果是一个散列，它的键是各个 lambda 的名称。通过保存返回值，您可以获得对相应 lambdas 的引用并操作计数器。你可以随意操纵计数器！在 irb 中加载 lambda_counter.rb 文件:

`% irb -r ./lambda_counter.rb`

```
>> c1 = Counter.call
=> {:get_x=>#<Proc:0x007fa92904ea28@/counter.rb:4 (lambda)>,
:increment=>#<Proc:0x007fa92904e910@/counter.rb:6 (lambda)>,
:increment=>#<Proc:0x007fa92904e898@/counter.rb:8 (lambda)>}` 
```

Enter fullscreen mode Exit fullscreen mode

计数器 c1 是一个散列，其中每个键指向一个进程。让我们在计数器上执行一些
操作:

```
>> c1[:increment].call
=> 1
>> c1[:increment].call
=> 2
>> c1[:increment].call
=> 3
>> c1[:increment].call
=> 2
>> c1[:get_x].call
=> 2 
```

Enter fullscreen mode Exit fullscreen mode

让我们创建另一个计数器 c2。c2 和 c1 有区别吗？换句话说，它们的行为像不同的物体吗？

```
>> c2 = Counter.call
=> {:get_x=>#<Proc:0x007fa92a1fcc98@/counter.rb:4 (lambda)>,
:increment=>#<Proc:0x007fa92a1fcc70@/counter.rb:6 (lambda)>,
:decrement=>#<Proc:0x007fa92a1fcc48@/counter.rb:8 (lambda)>}
>> c2[:get_x].call
=> 0
>> c1[:get_x].call
=> 2 
```

Enter fullscreen mode Exit fullscreen mode

c1 和 c2 都有它们自己的 x。所以你知道了:拥有没有类的对象是完全可能的。事实上，这种技术经常在 JavaScript
中使用，以确保变量不会泄漏出去，也不会无意中被其他函数或操作覆盖
。

虽然你可能不想在日常的 Ruby 编程中使用这种技术，但是这里有一个重要的教训。Ruby 的
作用域规则是这样的，当定义一个 lambda 时，那个 lambda
也可以访问作用域内的所有变量。正如反例
所示，闭包限制了对它们包装的变量的访问。这个技术
将在后面的章节中派上用场。

如果你做过一些 JavaScript 编程，你肯定会遇到回调的使用。如果使用得当，
回调是一种非常强大的技术。