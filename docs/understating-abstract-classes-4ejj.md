# 理解抽象类

> 原文：<https://dev.to/watzon/understating-abstract-classes-4ejj>

在我们开始之前，强制字典定义:

> /abˈstrakt,ˈabˌstrakt/
> 
> 1.  存在于思想中或者作为一个想法，但是没有实体或者具体的存在。

抽象是非常有用的，尽管很难理解编程中的概念。毕竟，如果抽象很容易理解，它们可能就不是抽象的了...他们会吗？

抽象类只是编程中存在的一种抽象形式，也是我将在这篇文章中讨论的一种。这个灵感来自于[水晶郎论坛](https://forum.crystal-lang.org/t/difference-between-abstract-def-and-def/998)上的一个[@ girging](https://dev.to/girng)的问题。由于原来的问题是一个 Crystal 问题，并且 Crystal 是我目前最喜欢的语言，我将使用 Crystal 示例，但是许多概念转移到其他语言，即使实现不同。

## 什么是抽象类？

抽象类是指应该被继承，而不是被实例化的类。它们通过提供应该存在于所有子类中的方法来充当其他类的基础，而无需实际实现这些方法。

一个简单抽象类的例子如下:

```
abstract class Foo
  abstract def bar(text : String) : Array(String)
end 
```

Enter fullscreen mode Exit fullscreen mode

在这个例子中，我们有一个类`Foo`，它有一个抽象方法`bar`。`bar`方法必须接受单个字符串参数，并且必须返回一个字符串数组。注意，如果我们试图创建一个`Foo`类的实例，编译器将抛出一个错误。如果我们想让`Foo`有用，我们必须扩展它。

```
class Baz < Foo
  def bar(text : String)
    text.split(" ")
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

现在我们已经定义了一个类`Baz`,它完全按照描述实现了`bar`方法。让我们试着让编译器抛出一个错误。

```
# This will throw an error since `bar` is not defined on `Baz`
class Baz < Foo
end

# This will throw an error since `bar` has the incorrect definition
class Baz < Foo
  def bar(opts : Array(String))
    opts.join
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

抽象类也可以定义包含在子类中的实际方法。这些方法可以是初始化器或任何其他类型的方法。

```
class Foo
  def initialize
    puts "#{self.class} initalized"
  end

  abstract def bar(text : String) : Array(String)
end

class Baz < Foo
  def bar(text : String)
    text.split(" ")
  end
end

baz = Baz.new
# => "Baz initalized" 
```

Enter fullscreen mode Exit fullscreen mode

## 应该在哪里使用它们？

抽象类在很多情况下都很有用，但是首先想到的例子是适配器，比如不同的数据库。数据库是一个很好的例子，因为它们都有相似的功能，但是它们都以稍微不同的方式做事。

这里有一个超级基础的例子:

```
class Connection
  # Implementation
end

abstract class Adapter

  def initialize(@name : String, @url : String)
  end

  abstract def insert(table, fields) : Int64

  # Other methods...

end

class Mysql < Adapter
  def insert(table, fields)
    # Insert stuff
    0
  end
end

class Postgres < Adapter
  def insert(table, fields)
    # Insert stuff
    0
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

显然这不是一个函数示例，但它应该编译并说明了基本概念。你可以从 [amberframework/granite](https://github.com/amberframework/granite) 查看 [`Granite::Adapter::Base`](https://github.com/amberframework/granite/blob/master/src/adapter/base.cr) 的工作示例。

感谢你阅读这篇文章。请不要忘记点击自我助推器按钮之一(个人来说，我喜欢独角兽)，如果你这么想分享到社交媒体。如果你分享到 twitter，请务必在@_watzon 标记我。

一些有帮助的链接:
[https://crystal-lang.org/](https://crystal-lang.org/)
[https://github.com/kostya/benchmarks](https://github.com/kostya/benchmarks)
[https://github.com/kostya/crystal-benchmarks-game](https://github.com/kostya/crystal-benchmarks-game)
[https://github.com/crystal-lang/crystal](https://github.com/crystal-lang/crystal)

在线找我:
[https://medium.com/@watzon](https://medium.com/@watzon)
[https://twitter.com/_watzon](https://twitter.com/_watzon)
[https://github.com/watzon](https://github.com/watzon)
[https://watzon . tech](https://watzon.tech)