# 装饰红宝石-第二部分-方法添加装饰

> 原文：<https://dev.to/baweaver/decorating-ruby-part-two-method-added-decoration-48mj>

我写文章的一个先兆是，如果我不断忘记某事是如何完成的，导致我写一个参考文献供以后回顾。这是一篇这样的文章。

# 今天会发生什么？

我们将会看到下一种类型的装饰，它包括截取`method_added`来制作一个更流畅的界面。

[![The "Dark Lord" Crimson with Metaprogramming magic](img/d52ea6a68f619a37a6e95ef0ab5dc374.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Wj0l-UKS--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/5pzy2brl5apjtt4edgrm.png)

**目录**

*   [第一部分——符号法装饰](https://dev.to/baweaver/decorating-ruby-part-1-symbol-method-decoration-4po2)
*   [第二部分-方法添加装饰](https://dev.to/baweaver/decorating-ruby-part-two-method-added-decoration-48mj)
*   [第三部分——前置装饰](https://dev.to/baweaver/decorating-ruby-part-three-prepending-decoration-1ehc)

[< <上一个](https://dev.to/baweaver/decorating-ruby-part-1-symbol-method-decoration-4po2) | [下一个>>T3】](https://dev.to/baweaver/decorating-ruby-part-three-prepending-decoration-1ehc)

# 方法加装饰是什么样子的？

你已经看到了符号方法的变体:

```
private def something; end 
```

Enter fullscreen mode Exit fullscreen mode

非常关注上一篇文章的读者可能已经注意到，我说过我更喜欢在 Ruby 中声明`private`方法的风格，但是这是在之后的*方式，这种方式可能被认为在社区中更受欢迎和广泛使用:* 

```
private

def something; end
def something_else; end 
```

Enter fullscreen mode Exit fullscreen mode

像这样使用`private`意味着*之后定义的每个*方法都将被认为是私有的。我们知道第一个是如何工作的，但是第二个呢？它不可能使用方法名，因为它捕获了这两种方法，并且没有改变定义语法。

这就是我们今天要研究和学习的内容，让我告诉你这是一次元编程之旅。

# 制作自己的方法补充装饰

和上一篇文章一样，在我们准备实现这篇文章之前，我们需要学习一些工具。

## 模块内含物

Ruby 使用模块包含作为一种方式来扩展具有额外行为的类，有时需要满足一个接口才能这样做。`Enumerable`是最常见的一种，需要一个`each`实现来工作:

```
class Collection
  include Enumerable

  def initialize(*items)
    @items = items
  end

  def each(&fn)
    return @items.to_enum unless block_given?
    @items.each { |item| fn.call(item) }
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

(`yield`在这里可以改为使用，但是不太明确，教起来会很混乱。)

通过定义这一个方法，我们给了我们的类做各种令人惊奇的事情的能力，像`map`、`select`等等。

通过这几行代码，我们给一个类添加了很多功能。Ruby 有趣的地方在于:它还提供了钩子让`Enumerable`知道它被包含了，包括它包含了什么。

## 感情包含在内

假设我们有自己的模块， [`Affable`](https://www.merriam-webster.com/dictionary/affable) ，这给了我们一个说“嗨”的方法:

```
module Affable
  def greeting
    "It's so very lovely to see you today!"
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

我的，它*是*相当于 [`Affable`](https://www.merriam-webster.com/dictionary/affable) 的模块，现在不是吗？

我们甚至可以做一只特别的`Affable`狐猴:

```
class Lemur
  include Affable
  def initialize(name) @name = name; end
end

Lemur.new("Indigo").greeting
=> "It's so very lovely to see you today!" 
```

Enter fullscreen mode Exit fullscreen mode

多漂亮的狐猴啊，是的。

## 钩、线、坠

比方说，我们想要告诉什么特定的动物是`Affable`。我们可以使用`included`来查看:

```
module Affable
  def self.included(klass)
    puts "#{klass.name} has become extra Affable!"
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

如果我们重新`include`那个模块:

```
class Lemur
  include Affable
  def initialize(name) @name = name; end
end

# STDOUT: Lemur has become extra Affable!
# => :initialize 
```

Enter fullscreen mode Exit fullscreen mode

很经典。哦，对了，说到经典...

## 确实格外上等

所以我们可以挂钩包含一个模块，太好了！我们为什么关心？

如果我们既想在类*中包含方法，又想*扩展它的行为，会怎么样？

只需`include`它就会将所有行为应用到一个类的*实例*中。仅仅用`extend`它就会将所有的行为应用到*类本身*。我们不能两者兼得。

...好吧好吧，这是鲁比，你抓到我了，我们完全可以做到这两点。

事实证明，`include`和`extend`只是一个类上的方法。如果我们想的话，我们可以只`Lemur.extend(ExtraBehavior)`，或者我们可以使用我们之前的有趣的小钩子。

使用这种技术的一个常见惯例是一个叫做`ClassMethods`的子模块，比如:

```
module Affable
  def self.included(klass)
    klass.extend(ClassMethods)
  end

  module ClassMethods
    def affable?
      true
    end
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

这允许我们将行为直接注入到类中，以及其他我们希望包含在实例中的行为。

我的一部分认为这是因为我不必记住`include`和`extend`之间的区别，但我总是记得这一点，不必花 20 分钟在两者和`prepend`之间翻转，看看哪个实际上有效，绝对不是。

现在还记得关于方法是今天的技术的标题吗？哦，是的，这也有一个钩子，但是首先我们需要指出一些东西需要首先被钩住。

## 升起你的旗帜

我们可以拦截正在添加的方法，但是我们如何知道应该拦截哪个方法？我们需要添加一个标志，让那个钩子知道是时候开始全力拦截了。

```
module Affable
  def self.included(klass)
    klass.extend(ClassMethods)
  end

  module ClassMethods
    def extra_affable
      @extra_affable = true
    end
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

如果您还记得`private`，这可能是一个标志，表示后面的每个方法都应该是私有的:

```
private

def something; end
def something_else; end 
```

Enter fullscreen mode Exit fullscreen mode

这里的想法是一样的，一旦一个标志被升起，它也可以被取下，以确保后面的方法不会受到影响。我们一直在暗示增加挂钩方法，所以让我们继续这样做。

## 方法补充

现在我们有了我们的旗帜，我们有足够的资源来钩住`method_added` :

```
module Affable
  def self.included(klass)
    klass.extend(ClassMethods)
  end

  module ClassMethods
    def extra_affable
      @extra_affable = true
    end

    def method_added(method_name)
      return unless @extra_affable

      @extra_affable = false
      # ...
    end
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

我们可以使用我们的标志来忽略`method_added`,除非设置了所述标志。在我们检查之后，我们可以取下标志，以确保之后定义的其他方法也不会受到影响。对于`private`来说，这种情况不会发生，但我们想要有礼貌。它*毕竟是*和`Affable`模块。

## 客气地别名

说到礼貌，仅仅重写一个方法而不给出一个调用它的方法是不太好的。我们可以在覆盖它之前使用`alias_method`为方法取一个新名字:

```
def method_added(method_name)
  return unless @extra_affable

  @extra_affable = false

  original_method_name = "#{method_name}_without_affability".to_sym
  alias_method original_method_name, method_name
end 
```

Enter fullscreen mode Exit fullscreen mode

这意味着我们可以通过这个名字访问原始方法。

## 裹战

这样我们就有了原始方法的别名，我们的钩子也就位了，让我们开始重写那个方法吧！和上一个教程一样，我们可以使用`define_method`来完成这个任务:

```
module Affable
  def self.included(klass)
    klass.extend(ClassMethods)
  end

  module ClassMethods
    def extra_affable
      @extra_affable = true
    end

    def method_added(method_name)
      return unless @extra_affable

      @extra_affable = false

      original_method_name = "#{method_name}_without_affability".to_sym
      alias_method original_method_name, method_name

      define_method(method_name) do |*args, &fn|
        original_result = send(original_method_name, *args, &fn)

        "#{original_result} Very lovely indeed!"
      end
    end
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

再次覆盖我们原来的类:

```
class Lemur
  include Affable

  def initialize(name) @name = name; end

  extra_affable

  def farewell
    "Farewell! It was lovely to chat."
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

我们可以试一试:

```
Lemur.new("Indigo").farewell
=> "Farewell! It was lovely to chat. Very lovely indeed!" 
```

Enter fullscreen mode Exit fullscreen mode

## `send`救命！

等等等等等等等等，`send`？上次不是用了`method`吗？

我们做到了，但是记住`method_added`是一个没有类实例上下文的类方法，或者换句话说，它不知道`farewell`方法在哪里。

让我们通过直接发送方法名，再次将它视为一个实例。现在我们*也可以*在这里使用`method`，但是那可能会贵一点。

只有`define_method`块中的内容在实例的上下文中执行。

## `exec`效用函数

如果我们想的话，我们可以让我们的特殊方法获取在实例上下文中执行的块，这是这篇文章的一个额外的特殊技巧。

假设我们让`extra_affable`也使用一个块，它允许我们操作原始值，并且仍然在实例的上下文中执行:

```
class Lemur
  include Affable

  def initialize(name) @name = name; end

  extra_affable { |original|
    "#{@name}: #{original} Very lovely indeed!"
  }

  def farewell
    "Farewell! It was lovely to chat."
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

对于普通块，这将在类的上下文中计算，但是我们希望它在实例的上下文中计算。这就是我们有`instance_exec`的原因:

```
module Affable
  def self.included(klass)
    klass.extend(ClassMethods)
  end

  module ClassMethods
    def extra_affable(&fn)
      @extra_affable    = true
      @extra_affable_fn = fn
    end

    def method_added(method_name)
      return unless @extra_affable

      @extra_affable   = false
      extra_affable_fn = @extra_affable_fn

      original_method_name = "#{method_name}_without_affability".to_sym
      alias_method original_method_name, method_name

      define_method(method_name) do |*args, &fn|
        original_result = send(original_method_name, *args, &fn)
        instance_exec(original_result, &extra_affable_fn)
      end
    end
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

跑步给了我们这个:

```
Lemur.new("Indigo").farewell
# => "Indigo: Farewell! It was lovely to chat. Very lovely indeed!" 
```

Enter fullscreen mode Exit fullscreen mode

现在请仔细注意这一行:

```
extra_affable_fn = @extra_affable_fn 
```

Enter fullscreen mode Exit fullscreen mode

我们需要使用它，因为 inside `define_method`的块在实例内部，它不知道`@extra_affable_fn`是什么。也就是说，它仍然可以看到调用该块的上下文之外的内容，这意味着它可以看到本地版本的`extra_affable_fn`就在那里，允许我们调用它:

```
instance_exec(original_result, &extra_affable_fn) 
```

Enter fullscreen mode Exit fullscreen mode

## `instance_eval` vs `instance_exec`？

为什么不用`instance_eval`？`instance_exec`也允许我们传递参数，否则`instance_eval`在一个实例中评估一些东西会很有意义。相反，我们需要在实例的上下文中执行一些东西，所以我们在这里使用`instance_exec`。

# 包装完毕

所以那是相当多的魔法，我花了相当多的时间才真正理解其中一些在做什么以及为什么。这完全没问题，如果我第一次就理解了所有的东西，我会担心，因为这意味着我没有真正学到任何东西！

我认为稍后会出现的一个问题是，我想知道多挂钩到`method_added`的效果会有多差。如果事实证明它让事情以一种非常漂亮和令人困惑的方式发展，那就有第三部了。如果没有，这一段就会消失，如果你问我，我会假装不知道你在说什么。

这里有很多潜在的真正有趣的东西，但也有很多潜在的滥用。一定不要滥用这种魔力，因为对于每一层的重定义，代码会变得越来越难以推理和测试。

在大多数情况下，我会提倡用`SimpleDelegate`、`Forwardable`，或者用`super`的简单继承来扩展类的行为。不要在树篱修剪机可以使用的地方使用电锯，但有时知道电锯可以解决那些特别棘手的问题会很好。

谨慎是游戏的名字。

**目录**

*   [第一部分——符号法装饰](https://dev.to/baweaver/decorating-ruby-part-1-symbol-method-decoration-4po2)
*   [第二部分-方法添加装饰](https://dev.to/baweaver/decorating-ruby-part-two-method-added-decoration-48mj)
*   [第三部分——前置装饰](https://dev.to/baweaver/decorating-ruby-part-three-prepending-decoration-1ehc)

[< <上一个](https://dev.to/baweaver/decorating-ruby-part-1-symbol-method-decoration-4po2) | [下一个>>T3】](https://dev.to/baweaver/decorating-ruby-part-three-prepending-decoration-1ehc)