# 编写模块函数的 4 种方法

> 原文：<https://dev.to/tkimia/4-ways-to-write-module-functions-1lmm>

这不是一篇最佳实践的文章。

假设您想添加一些对字符串进行操作的函数，但不想对字符串类进行修改。我们还可以说，您从 Apache Commons 的 Lang 库中获得了灵感，想要创建自己版本的 [StringUtils](https://commons.apache.org/proper/commons-lang/apidocs/org/apache/commons/lang3/StringUtils.html) 。

我们想以可以这样调用的函数结束:

```
StringUtils.alphanumeric? "Hello123" # true
StringUtils.alphanumeric? "Hello World" # false 
```

由于`StringUtils`不是我们想要实例化的对象，我们将把它写成一个模块。

现在，您必须做出选择:我们希望如何编写代码，以便模块本身可以接收这些方法。

## 选项 1:在方法名前面加上`self`

```
module StringUtils
  def self.alphanumeric?(str)
    /\A[0-9a-zA-Z]+\z/.match? str
  end
end 
```

这相当于`def StringUtils.alphanumeric?`，是托管在[官方 Ruby docs 网站](https://ruby-doc.org/docs/ruby-doc-bundle/ProgrammingRuby/book/tut_modules.html)上的一个例子。但是，Rubocop 会抱怨，建议你改用`self.alphanumeric?`。

## 选项 2:将它们放在“单例类”(Eigenclass)中

```
module StringUtils
  class << self
    def alphanumeric?(str)
      /\A[0-9a-zA-Z]+\z/.match? str
    end
  end
end 
```

当我第一次读到这种类型的代码时，我彻底糊涂了。另外，如果你搜索 *Ruby Singleton class* ，你会找到几十个与实现 Singleton 模式无关的链接。好在我们想出了一个更清晰的名字:“本征类”。

无论如何，这个选项很好，因为您可以将希望模块接收的所有方法分组到一个块中。由于这个原因，我经常看到这个选项在类内部和实例方法一起使用。

## 选项三:`extend self`

```
module StringUtils
  extend self

  def alphanumeric?(str)
    /\A[0-9a-zA-Z]+\z/.match? str
  end
end 
```

我在 [Pact 源代码](https://github.com/pact-foundation/pact-ruby/blob/master/spec/support/spec_support.rb)中看到这个，吃了一惊。

这段代码允许你调用`StringUtils.alphanumeric?`，但也允许你在其他类中调用`include StringUtils`。

我很难想出一个你想要的用例。

## 选项四:`module_function`

```
module StringUtils
  module_function

  def alphanumeric?(str)
    /\A[0-9a-zA-Z]+\z/.match? str
  end
end 
```

在`module_function`行下定义的所有方法都可以被模块本身接收，并且会被私有，所以它们不能被包含在类中。你也可以把`module_function`放在你的模块的底部，给它符号参数，这样你就可以挑选受影响的方法:

```
module StringUtils
  def alphanumeric?(str)
    /\A[0-9a-zA-Z]+\z/.match? str
  end

  module_function :alphanumeric?
end 
```

## 结论

我不会说我会选择哪个选项来编写这段代码。虽然每个选项都有细微的争论，但它们都完成了工作。我在这篇文章的开头说这不是关于最佳实践的。

通常很难理解不是自己编写的代码中使用的模式。当有人编写的代码与您编写的不同时，这一点尤其明显。我希望看到这些并排的片段能够帮助您在将来阅读其他人的代码时认识到发生了什么。

**奖金**
这里有一个有趣的事实:如果你在使用 Rubocop 时输入`extend self`，它会指示你使用`module_method`来代替。但是，这些显然是不一样的！[更多在此。](https://github.com/rubocop-hq/ruby-style-guide/issues/556)