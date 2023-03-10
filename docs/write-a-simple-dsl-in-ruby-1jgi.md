# 用 Ruby 写一个简单的 DSL

> 原文：<https://dev.to/vinistock/write-a-simple-dsl-in-ruby-1jgi>

# 什么是 DSL，它是用来做什么的？

DSL(领域特定语言)可以被认为是一种为独特目的而构建的方言。在底部，它是一组可以在给定域内使用的函数。

在`Gemfile`、`Rakefile`或用`rspec`编写的测试中使用的语法是 Ruby 世界中 DSL 的一些例子。他们的目标是为他们的任务提供定义良好的接口——分别声明依赖关系、定义 make like 任务和编写单元测试。

让我们来看看如何创建一个类似于 [bundler](https://github.com/bundler/bundler) 的工具来读取 Gemfile。请注意，我们的目的是掌握概念，而不是最终拥有一个包管理器的工作版本。

# 如何在 Ruby 中创建 DSL

令人惊讶的是(至少对我来说)，用 Ruby 定义自己的 DSL 并不复杂。事实上恰恰相反。在我们的包管理器示例中，它包括以下步骤:

1.  创建一个定义 DSL 中所有可用方法的类
2.  读取 gem 文件
3.  在给定实例的执行上下文中评估 Gemfile
4.  使用评估的信息来安装 gem

这些步骤一开始可能听起来晦涩难懂，但是坚持下去，它们很快就会变得有意义。让我们从创建 DSL 类实现开始。这个类将包含所有可用于 Gemfile 的方法。

为了简单起见，我们的 DSL 将只包含`gem`指令——用于添加依赖项。为简单起见，其他方法(如`group`或可选参数(如`require: false`)的实现将被省略。但是，这里介绍的概念应该是实现更丰富功能的良好起点。

```
# dsl.rb

class Dsl
  attr_reader :gems

  def initialize
    # Initialize the list of gems
    # as an empty array.
    @gems = []
  end

  def read_gemfile
    # Read the contents of the gemfile as a string.
    contents = File.read("Gemfile")

    # Evaluate the Ruby string within the context
    # of the Dsl instance. That is, execute whatever
    # code found in the Gemfile using the state of
    # the current Dsl object.
    instance_eval(contents)
  end

  private

  # Gem directive definition
  # Adds the given gem_name to the list of
  # gems to be installed.
  #
  # This is the method executed whenever the Gemfile
  # contains the directive "gem 'something'"
  def gem(gem_name)
    @gems << gem_name
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

我们现在有了 Dsl 类的最小实现，它可以读取 gem 文件的内容，并通过将 gem 名称添加到`@gems`数组来执行`gem`指令。

向 DSL 添加新指令所需要的就是在 DSL 类内部创建新方法。类中的每个方法都可以在我们的 Gemfile 中使用。此外，我们可以定义一个自定义的`method_missing`来选择如何处理 DSL 中不存在的调用。

下面这个非常简洁的 Gemfile 使用了我们的`gem`指令，也使用了一些常规的 Ruby 代码。注意，因为我们在 Dsl 实例中执行 Gemfile 的内容，所以任何 Ruby 代码都是有效的。

```
# Gemfile

# Add 'rails' to the list of gems in the Dsl class.
gem "rails"

# Add 'rubocop' to the list of gems only if the environment
# variable RAILS_ENV matches 'development'.
if ENV["RAILS_ENV"] == "development"
  gem "rubocop"
end 
```

Enter fullscreen mode Exit fullscreen mode

现在我们已经有了 Dsl 实现和示例 Gemfile，我们可以编写一些伪代码来表示由[捆绑器](https://github.com/rubygems/rubygems)执行的复杂得多的安装过程。

```
# Create Dsl instance.
dsl = Dsl.new

# Read and evaluate the Gemfile populating
# the @gems variable.
dsl.read_gemfile

# For each defined gem, install it
# which usually involves download the code from
# the package registry (rubygems.org) and placing it
# inside the Ruby folder structure.
dsl.gems.each do |gem|
  Bundler.install_gem(gem)
end 
```

Enter fullscreen mode Exit fullscreen mode

# 结论

仅此而已！我希望这几行代码能够揭示使用 Ruby 创建 DSL 是多么容易。在现实世界中，DSL 可能要复杂得多，有许多复杂的语法和大量的类来实现所有期望的功能。

此外，还有许多我们在本文中没有涉及的安全问题。因为这种方法从字符串中执行 Ruby 代码，所以我们需要格外小心任何最终可能被 DSL 评估的用户输入。否则，它可能导致远程代码执行，并导致各种各样的漏洞。

我希望这个简短的介绍能提供足够的背景知识，并激发人们对这个话题的兴趣。请在评论中告诉我你的想法。你以前写过 DSL 吗？

## 跑题

感谢每一个看我文章的人。我刚刚有了 2000 名粉丝，我很兴奋！

如果你想要一点点编程评论或随机图片，请务必在 Twitter 上关注我。