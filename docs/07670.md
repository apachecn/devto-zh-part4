# 装饰红宝石-第一部分-符号法装饰

> 原文：<https://dev.to/baweaver/decorating-ruby-part-1-symbol-method-decoration-4po2>

我写文章的一个先兆是，如果我不断忘记某事是如何完成的，导致我写一个参考文献供以后回顾。这是一篇这样的文章。

# 今天会发生什么？

在这篇特别的文章中，我们将会看到各种各样的方法修饰和一些可爱的元编程，来给 Ruby 一点额外的弯曲。

[![The "Dark Lord" Crimson with Metaprogramming magic](img/d52ea6a68f619a37a6e95ef0ab5dc374.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Wj0l-UKS--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/5pzy2brl5apjtt4edgrm.png)

**目录**

*   [第一部分——符号法装饰](https://dev.to/baweaver/decorating-ruby-part-1-symbol-method-decoration-4po2)
*   [第二部分-方法添加装饰](https://dev.to/baweaver/decorating-ruby-part-two-method-added-decoration-48mj)
*   [第三部分-前置装饰](https://dev.to/baweaver/decorating-ruby-part-three-prepending-decoration-1ehc)

[下一个> >](https://dev.to/baweaver/decorating-ruby-part-two-method-added-decoration-48mj)

# 什么是装饰？

装饰是用一点额外的东西“装饰”一个方法的想法。一点香料，味道，等等。

你写过这样的代码吗？

```
def something
  @something ||= expensive_call_here
end 
```

Enter fullscreen mode Exit fullscreen mode

很快就变得很普遍了，不是吗？在整个代码库中，它几乎已经成为一种记忆模式。如果我们可以说一个词，而不是重复地输入那些实例变量，这不是很好吗？

装饰可以让我们做这样的事情:

```
memoized def something
  expensive_call_here
end 
```

Enter fullscreen mode Exit fullscreen mode

它将有效地做同样的事情，但是它如何做是完全不同的事情，我们将在本文中探索。

# 符号法装饰

指的是使用方法名的符号来重新定义它以添加额外的行为的修饰。这是我们在第一部分要看的，因为这里有很多内容要讲。

## 方法定义返回符号

我们使用的第一个例子依赖于 Ruby 2.1+中引入的一个事实:当一个方法被定义时，它是一个返回包含方法名称的符号的表达式。

在 REPL 尝试一下真正的快速:

```
def testing; end
# => :testing 
```

Enter fullscreen mode Exit fullscreen mode

这允许我们在方法定义的前面加上另一个方法，用这个事实做一些事情。您可能经常在 Ruby 的私有方法中看到这种模式:

```
private

def private_a; end
def private_b; end 
```

Enter fullscreen mode Exit fullscreen mode

这两种方法都被认为是私有的，但是在 Ruby 中还有另外一种使用`private`的方式，为了清晰起见，我个人更喜欢这种方式:

```
private def private_a; end
private def private_b; end 
```

Enter fullscreen mode Exit fullscreen mode

它允许我们显式地将一个方法前缀为 private，这样更容易移动，一眼就能看出什么是真正的 private。

(好吧，尽管在 Ruby 中是私有的，但我们*毕竟是在使用元编程...)*

考虑到这一点，采用符号的方法如何重定义该方法呢？我们需要更多的工具来做这件事。

## `method`法

我们有了一个方法名的符号，很好，但是我们怎么做呢？一种方法是使用`method`方法:

```
def testing; 1 end

original_method = method(:testing)
# => #<Method: main.testing>

original_method.call
# => 1 
```

Enter fullscreen mode Exit fullscreen mode

这创建了一个类似 Proc 的对象，一个可以像 Proc 一样调用的[方法](https://ruby-doc.org/core-2.6.3/Method.html)的实例，带有参数和其他你通常传递的东西。

利用这个小事实，您还可以做其他非常有用的事情，但这超出了本文的范围，也许改天吧。

重要的是，我们通过方法名得到了一个方法的可调用表示。

## 一种噬人法和`define_method`

让我们从简单的东西开始，一个无论你传递给它什么方法它都会吃掉的方法。我们可以称之为`quiet` :
这样可爱的东西

```
def quiet(method_name)
  define_method(method_name) { nil }
end 
```

Enter fullscreen mode Exit fullscreen mode

这引入了另一个概念，`define_method`，它可以就地定义一个方法。这包括已经定义好的方法，让我们覆盖已经存在的东西。

如果我们在之前的测试方法中调用这个方法:

```
quiet def testing; 1 end

testing
# => nil 
```

Enter fullscreen mode Exit fullscreen mode

那个`1`已经过去了，我们只是直接跳过了老方法。现在这并不是特别有用，我们可以使用`remove_method`来达到同样的效果。

除了有效性之外，我们知道可以通过方法的名称重新定义方法，并将其用作前缀。

## 商店里有什么？

回到我们最初的缓存想法，我们如何实现这样的东西呢？回过头来看，我们已经拥有了所有需要的工具，我们只需要把它们放在一起就能做出有用的东西。

将`define_method`视为用一些额外行为“包装”方法的一种方式。我们所要做的就是捕捉原始参数，并愉快地传递它们，我们可以对原始方法返回的内容做任何我们想做的事情:

```
define_method(name) { |*args, &fn| original_method.call(*args, &fn) } 
```

Enter fullscreen mode Exit fullscreen mode

现在只需要在包装上添加一些额外的香料。请记住，缓存，至少是基本的缓存，看起来像这样:

```
def something
  @something ||= expensive_call
end 
```

Enter fullscreen mode Exit fullscreen mode

这个想法是使用`||=`来存储值，而不是进行另一个昂贵的调用。这正是我们想要模拟的缓存前缀:

```
def cache(method_name)
  original_method = method(method_name)

  define_method(method_name) do |*args, &fn|
    ivar = :"@#{method_name}"
    cached_value = instance_variable_get(ivar)

    return cached_value if cached_value

    value = original_method.call(*args, &fn)
    instance_variable_set(ivar, value)
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

## 分解！

打破这一点，我们在这里有一些其他的新概念，主要是关于实例变量和动态处理它们。

我们从上面记住的第一行，我们想要原来的方法:

```
original_method = method(method_name) 
```

Enter fullscreen mode Exit fullscreen mode

接下来，我们通过名称重新定义原始方法，并将参数代理到:

```
define_method(method_name) do |*args, &fn| 
```

Enter fullscreen mode Exit fullscreen mode

我们感到有点有趣的是，当我们到下一行时，我们通过在方法名前面加上`@` :
来命名一个实例变量

```
ivar = :"@#{method_name}" 
```

Enter fullscreen mode Exit fullscreen mode

这允许我们查看一个值是否已经存在:

```
cached_value = instance_variable_get(ivar) 
```

Enter fullscreen mode Exit fullscreen mode

现在要小心，因为下一行做了一些你可能不喜欢的微妙的事情:

```
return cached_value if cached_value 
```

Enter fullscreen mode Exit fullscreen mode

如果这个值是 falsy，它将跳过它，重新定义这个值。这是与`||=`相同的问题，可以通过将该行改为以下内容来解决:

```
return cached_value if instance_variable_defined?(ivar) 
```

Enter fullscreen mode Exit fullscreen mode

这将使用变量是否被定义的事实，而不是它的值的真实性。

接下来的几行是我们确保记住值的地方:

```
value = original_method.call(*args, &fn)
instance_variable_set(ivar, value) 
```

Enter fullscreen mode Exit fullscreen mode

我们将该值从方法中取出，并设置一个实例变量来记住它。

## 四处奔走

让我们试一试:

```
cache def excruciatingly_long_method_call
  (1..1_000_000).to_a.sample(10)
end

excruciatingly_long_method_call
# => [116266, 583871, 537296, 296408, 563441, 92172, 511762, 597596, 681279, 951614]

excruciatingly_long_method_call
# => [116266, 583871, 537296, 296408, 563441, 92172, 511762, 597596, 681279, 951614]

excruciatingly_long_method_call
# => [116266, 583871, 537296, 296408, 563441, 92172, 511762, 597596, 681279, 951614]

excruciatingly_long_method_call
# => [116266, 583871, 537296, 296408, 563441, 92172, 511762, 597596, 681279, 951614] 
```

Enter fullscreen mode Exit fullscreen mode

现在，除非你真的很幸运，你会得到一些不同的数字。添加 3 到 6 个额外的 0，使它实际上需要一段时间，但我没有耐心，所以一百万左右应该没问题。

(用`srand`种子，你*可以*复制它，但这让我们有点跑题)

有了它，我们就有了一个缓存的方法，我们只需要把`cache`放在它的前面就可以完成所有这些。很漂亮，不是吗？

# 包装完毕

下一次，我们将会看到一种更高级的装饰方法，包括`method_added`、`alias_method`和`instance_exec`以及其他一些巧妙的技巧。

**目录**

*   [第一部分——符号法装饰](https://dev.to/baweaver/decorating-ruby-part-1-symbol-method-decoration-4po2)
*   [第二部分-方法添加装饰](https://dev.to/baweaver/decorating-ruby-part-two-method-added-decoration-48mj)
*   [第三部分——前置装饰](https://dev.to/baweaver/decorating-ruby-part-three-prepending-decoration-1ehc)

[下一个> >](https://dev.to/baweaver/decorating-ruby-part-two-method-added-decoration-48mj)