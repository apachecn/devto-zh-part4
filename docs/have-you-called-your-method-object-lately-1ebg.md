# 你最近调用过你的方法对象吗？

> 原文：<https://dev.to/hint/have-you-called-your-method-object-lately-1ebg>

*最初发布于 [Hint 的博客](https://hint.io/blog/procto-method-object)T3】*

请允许我向您介绍另一个已经证明在编写 Ruby 时非常有用的库:the[proct](https://github.com/snusnu/procto)gem。

## 监考概述

Procto 是一块宝石，它可以让您将 Ruby 类转换成方法对象。这有助于清理实例化一个类以执行单个任务、计算等的代码。

为了更好地理解它试图促进什么，也许回顾一下 Ruby 中的方法对象会有所帮助。

### 红宝石法对象

方法对象本质上是一个具有绑定上下文并提供单一方法的对象。

考虑这个例子:

```
 >> meth_obj = [:foo, :bar, :baz].method(:size)
  => Array#size()
  >> meth_obj.class
  => Method < Object
  >> meth_obj.call
  => 3 
```

Enter fullscreen mode Exit fullscreen mode

上面，`meth_obj`是一个绑定到包含`[:foo, :bar, :baz]`的数组的方法对象。在这种情况下，当在方法对象上执行`call`时，`size`消息被传递给方法对象所绑定的上下文(`[:foo, :bar, :baz]`)，结果是`3`。有趣的是(尽管不相关)，在这种情况下，Ruby 允许您解除绑定和重新绑定。

例如:

```
 >> meth_obj = [:foo, :bar, :baz].method(:size)
  >> meth_obj.call
  => 3
  >> meth_obj = meth_obj.unbind.bind([:waldo, :fred])
  >> meth_obj.call
  => 2 
```

Enter fullscreen mode Exit fullscreen mode

## 使用

要使用 Procto，您需要确保您已经安装了 gem，并且它已经由您的程序或应用程序加载。如果 gem 是可用的，但是还没有被加载，你可以在你的 Ruby 类的顶部使用`require 'procto'`。

一旦 gem 被加载并可供您使用，您只需要:`include Procto.call`在您的类定义之后，ala:

```
require 'procto'

class Foo
  include Procto.call

  def initialize(bar)
    @bar = bar
  end

  def call
    # do stuff
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

当调用您的类时，您将向`call`提供您通常会传递给类的初始化器的参数。

例如:

你应该做`Foo.call(bar)`而不是`Foo.new(bar).call`。

## 区块

虽然 Procto 非常*有用*，但它不是所有事情的答案。您可能会发现自己想做的一件事是向您的`call`方法传递一个块。不幸的是，Procto 不支持这一点。

考虑下面的代码:

```
class FooWithBlock
  include Procto.call

  def initialize(bar)
    @bar = bar
  end

  def call
    # do something

    yield if block_given?
  end

  protected

  attr_reader :bar
end 
```

Enter fullscreen mode Exit fullscreen mode

您可能希望能够像这样使用上面的代码:

```
FooWithBlock.call('Hello') do
  puts "I'm in a block!"
end 
```

Enter fullscreen mode Exit fullscreen mode

可悲的是，你的块永远不会被执行。

如果您*需要*做上面的事情，您将不得不在不使用 Procto 的情况下这样做，因此您需要从您的类中移除`include Procto.call`，并如下调用您的类:

```
FooWithBlock.new('Hello').call do
  puts "I'm in a block!"
end 
```

Enter fullscreen mode Exit fullscreen mode

## 奖金

如果你以前没用过，我也强烈推荐[协和宝石](https://github.com/mbj/concord)。当与 Procto 结合使用时，它确实为调用方法对象创建了一个很好的接口。

Concord 抽象出不必为类及其属性定义初始化器和属性访问器。它也恰好与普罗克多配合得很好。

使用这两者，您可以:

```
class Foo

  def initialize(bar, baz)
    @bar = bar
    @baz = baz
  end

  def call
    # do stuff
  end

  protected

  attr_reader :bar, :baz
end

For.new(bar, baz).call 
```

Enter fullscreen mode Exit fullscreen mode

成:

```
class Foo
  include Concord.new(:bar, :baz)
  include Procto.call

  def call
    # do stuff
  end
end

Foo.call(bar, baz) 
```

Enter fullscreen mode Exit fullscreen mode

我应该提到 Concord 将您的初始化器限制为三个参数。您可以通过传递一个更复杂的数据结构作为一个(或多个)参数，并从该对象中提取参数来解决这个问题，或者使用[Anima gem([https://github.com/mbj/anima](https://github.com/mbj/anima))来代替，它不限制您可以传递的参数的数量，而是采用一个初始化散列(关键字参数)来代替参数。

## 总结

Procto 是清理你的类界面的一个极好的工具。当与 Concord gem 结合使用时，您会发现自己编写了明显更少的样板代码来启动和运行您的类。您的代码最终还会生成一个更小的抽象语法树(AST)，这通常是一件非常好的事情。