# 装饰红宝石-第三部分-前置装饰

> 原文：<https://dev.to/baweaver/decorating-ruby-part-three-prepending-decoration-1ehc>

我写文章的一个先兆是，如果我不断忘记某事是如何完成的，导致我写一个参考文献供以后回顾。这是一篇这样的文章。

# 今天会发生什么？

我们将关注下一种类型的修饰，包括在调用链中预先放置一个模块。

[![The "Dark Lord" Crimson with Metaprogramming magic](img/d52ea6a68f619a37a6e95ef0ab5dc374.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Wj0l-UKS--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/5pzy2brl5apjtt4edgrm.png)

**目录**

*   [第一部分——符号法装饰](https://dev.to/baweaver/decorating-ruby-part-1-symbol-method-decoration-4po2)
*   [第二部分-方法添加装饰](https://dev.to/baweaver/decorating-ruby-part-two-method-added-decoration-48mj)
*   [第三部分-前置装饰](https://dev.to/baweaver/decorating-ruby-part-three-prepending-decoration-1ehc)

[< <上一个](https://dev.to/baweaver/decorating-ruby-part-two-method-added-decoration-48mj) | ~~下一个>>T3】~~

# 前置是什么样子的？

前置就是我们取一点代码，放在原始代码前面:

```
Prepended -> Original -> Call Chain 
```

Enter fullscreen mode Exit fullscreen mode

与`include`不同，它在原:
后插入

```
Original -> Included -> Call Chain 
```

Enter fullscreen mode Exit fullscreen mode

## 包含在混合中

为了演示，让我们在一个模块中制作一个`talk`函数，`Talkable`，并在一个`Lemur`类中制作`include`:

```
module Talkable
  def talk; "Hi there!" end
end

class Lemur
  include Talkable
end

Lemur.new.talk
# => "Hi there!" 
```

Enter fullscreen mode Exit fullscreen mode

如果`Lemur`有自己的说话方式会怎么样？:

```
module Talkable
  def talk; "Hi there!" end
end

class Lemur
  include Talkable

  def talk; "I wasn't expecting you today" end
end

Lemur.new.talk
# => "I wasn't expecting you today" 
```

Enter fullscreen mode Exit fullscreen mode

因为`Lemur`有一个用于`talk`的方法，所以它在调用链中首先被调用。

## 改为前置？

如果我们把`include`换成`prepend`呢？

```
module Talkable
  def talk; "Hi there!" end
end

class Lemur
  prepend Talkable

  def talk; "I wasn't expecting you today" end
end

Lemur.new.talk
# => "Hi there!" 
```

Enter fullscreen mode Exit fullscreen mode

> 哦，如果你在`pry`或`irb`，记住之前的那个`include`还会在那里。Ruby 的开放类可以带来一些有趣的调试问题。

`prepend`将`Talkable#talk` *放在* `Lemur#talk`前面，使其先被调用。

这也有一个有趣的副作用:我们可以使用`super`来引用原始类:

```
module Talkable
  def talk; "Hi there! #{super}" end
end

class Lemur
  prepend Talkable

  def talk; "I wasn't expecting you today" end
end

Lemur.new.talk
# => "Hi there! I wasn't expecting you today" 
```

Enter fullscreen mode Exit fullscreen mode

记住这个，因为这里有各种有趣的暗示。

## 延长谈话

那什么是`extend`？它既不做这两者，它是一个具有模块方法的类，使它们成为类方法。

迷茫？哦，那没什么问题，我经常在两者之间切换(`include`和`extend`)，直到我的代码工作几次。以后我可能会写一篇关于呼叫链的完整文章，用图片参考来提醒我。图片让一切变得更有趣。

啊，对了，`prepend`作装饰，对了对了。让我们回到那个话题。

# 用前置装饰

再一次，我们需要知道一些事情，并冒险深入元编程的兔子洞几米。

## 模块在飞

您已经看到了这样创建的模块:

```
module Talkable
  def talk; "Hi there!" end
end 
```

Enter fullscreen mode Exit fullscreen mode

在 Ruby 中，我们可以动态定义一个模块:

```
Module.new do
  def talk; "Hi there!" end
end 
```

Enter fullscreen mode Exit fullscreen mode

像 Ruby 中的大多数表达式一样，这是一个值，这意味着我们可以用它做各种有趣的事情。

## 不断的经历

我们可以把它赋给一个变量，或者如果我们真的想的话，我们可以把它变成一个常量:

```
mod = Module.new do
  def talk; "Hi there!" end
end

Lemur.const_set("Talkable", mod)
# => Lemur::Talkable 
```

Enter fullscreen mode Exit fullscreen mode

完成之后，我们可以检查这个常量是否存在:

```
Lemur.const_defined?("Talkable")
# => true 
```

Enter fullscreen mode Exit fullscreen mode

然后我们就可以得到:

```
Lemur.const_get("Talkable")
# => Lemur::Talkable 
```

Enter fullscreen mode Exit fullscreen mode

注意，你不能在顶级水平上这样做(除非，那不好玩...)，但是您甚至可以在任何已定义的类上这样做，如`Object`或`Kernel`！(尽管那样会显得特别调皮。)

重点是，这些常量给一个潜在的动态定义的值起了一个好名字，并允许我们做一些有趣的事情。

## 卓越的元编程

如果我们想动态定义一些方法呢？我们记得以前的一些文章中提到过`define_method`，但是如果我们想在模块内部定义一个方法呢？

Ruby 让我们用`class_eval` :
也能做到这一点

```
mod = Module.new

phrases = {
  hello: "Why hello there!",
  goodbye: "Fare thee well!"
}

mod.class_eval do
  phrases.each do |name, phrase|
    define_method(name) { phrase }
  end
end

mod.instance_methods
=> [:hello, :goodbye] 
```

Enter fullscreen mode Exit fullscreen mode

但这是一个模块！我们应该用`module_eval`！嗯，我们可以，但它们是一样的:

```
[2] pry(main)> $ mod.class_eval

From: vm_eval.c (C Method):
Owner: Module
Visibility: public
Number of lines: 5

VALUE
rb_mod_module_eval(int argc, const VALUE *argv, VALUE mod)
{
    return specific_eval(argc, argv, mod, mod);
}
[3] pry(main)> $ mod.module_eval

From: vm_eval.c (C Method):
Owner: Module
Visibility: public
Number of lines: 5

VALUE
rb_mod_module_eval(int argc, const VALUE *argv, VALUE mod)
{
    return specific_eval(argc, argv, mod, mod);
} 
```

Enter fullscreen mode Exit fullscreen mode

> `$`是显示`pry`中某事物来源的快捷方式。我经常使用它。`?`做了同样的事情，但它的文档。

# 终于前置了！

现在我们知道了所有这些，是时候用一个神奇的元编程将它们联系起来了！

有趣的是，前两节中的大部分技术在这里仍然非常有用。但是对于这一个，我总是想知道事情花了多长时间。

让我们做一个前置装饰器来告诉我们这一点！我们称之为`Timeable` :

```
module Timeable
end 
```

Enter fullscreen mode Exit fullscreen mode

在它的内部，我们需要一个方法来包装另一个方法，你可能还记得`Symbol`方法:

```
module Timeable
  def self.included(klass)
    klass.extend(ClassMethods)
  end

  module ClassMethods
    def measure(method_name)
    end
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

现在我们需要利用常数的知识，给我们自己一个挂钩点来添加东西。给一个类添加几个模块是很不礼貌的，所以我们把它放在一个地方。如果我们需要调试的话，这也有助于跟踪事情的发展

```
module Timeable
  def self.included(klass)
    klass.extend(ClassMethods)
  end

  module ClassMethods
    def measure(method_name)
      timing_module =
        if const_defined?("Timing")
          const_get("Timing")
        else
          const_set("Timing", Module.new).tap(&method(:prepend))
        end

      p timing_module
    end
  end
end

class Lemur
  include Timeable

  measure "Something"
end
# => Lemur::Timing 
```

Enter fullscreen mode Exit fullscreen mode

我们在这里使用`tap`,因为`prepend`返回的是某个东西被加在前面的常数。`Lemur`，在这种情况下，我们却要用`Timing`常数来代替。结尾的`p`是为了证明它是有效的。

要么常量存在，要么我们创建它并将它添加到包含`Timeable`的类中

## 时间和地点

我们来实现这个东西:

```
module Timeable
  def self.included(klass)
    klass.extend(ClassMethods)
  end

  module ClassMethods
    def measure(method_name)
      timing_module =
        if const_defined?("Timing")
          const_get("Timing")
        else
          const_set("Timing", Module.new).tap(&method(:prepend))
        end

      timing_module.class_eval do
        define_method(method_name) do |*args, &fn|
          start_time = Time.now
          result = super(*args, &fn)
          puts "Time taken: #{Time.now - start_time}"
          result
        end
      end
    end
  end
end

class Lemur
  include Timeable

  measure def super_cached_array_sample
    @super_cached_array_sample ||= (1..100_000_000).to_a.sample(10)
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

跑步让我们得到了这个:

```
indigo = Lemur.new

indigo.super_cached_array_sample
# Time taken: 3.165698
# => [30125753, 40206899, 23039117, 35232027, 60498349, 60359828, 24456489, 58646248, 96152882, 69191217]
indigo.super_cached_array_sample
# Time taken: 2.0e-06
# => [30125753, 40206899, 23039117, 35232027, 60498349, 60359828, 24456489, 58646248, 96152882, 69191217] 
```

Enter fullscreen mode Exit fullscreen mode

如果我们看一看我们亲爱的狐猴类的祖先，我们会发现一些有趣的事情:

```
Lemur.ancestors
# => [Lemur::Timing, Lemur, Timeable, Object 
```

Enter fullscreen mode Exit fullscreen mode

我们在这里做的唯一一件事是使用原始方法运行之前和之后的时间，并使用`puts`为我们将它记录到屏幕上。

## 但是关于方法的补充呢？

我们可以，我们当然可以。这也需要更多的代码，所以我把它作为一个练习留给读者，让他们把这两种技术结合成有趣的东西！

学习 Ruby 是一个学习一些小事情的过程，这些小事情可以帮助我们找到更大问题的答案。一旦你对什么东西该去哪里有了直觉，有了名副其实的工具带，你就能为几乎任何事情做好准备。

请记住，这些工具之一是使用搜索引擎，并不时寻求帮助。我当然现在也是。

# 包装完毕

一个接一个的魔术，这是一个值得写的系列。谁知道呢，我甚至可能在这里的某个地方偷偷放入另一部分，尽管我仅存的想法是涉及`TracePoint`和[的更黑暗的魔法，我已经写了几篇关于那个](https://medium.com/@baweaver/exploring-tracepoint-in-ruby-part-one-example-code-2cf9b1a1b956)的文章。

我可能会把它们翻译给 dev.to，并完成最后几个部分。装饰当然是魔法，但`TracePoint`是纯粹的神秘乐趣和恶作剧。

就像所有的魔法一样，谨慎是明智的，但完全避免谨慎是明智的？有时你需要电锯来修剪一棵树，天哪，我们将为你准备一把闪亮的神奇电锯来修剪你能想象到的最壮观的树。

**目录**

*   [第一部分——符号法装饰](https://dev.to/baweaver/decorating-ruby-part-1-symbol-method-decoration-4po2)
*   [第二部分-方法添加装饰](https://dev.to/baweaver/decorating-ruby-part-two-method-added-decoration-48mj)
*   [第三部分——前置装饰](https://dev.to/baweaver/decorating-ruby-part-three-prepending-decoration-1ehc)

[< <上一个](https://dev.to/baweaver/decorating-ruby-part-two-method-added-decoration-48mj) | ~~下一个>>T3】~~*