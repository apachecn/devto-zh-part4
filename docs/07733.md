# Ruby 快速提示:ABC，永远保持稳定

> 原文：<https://dev.to/thepeoplesbourgeois/ruby-quick-tip-abc-always-be-constantizing-43bo>

Ruby 被誉为现代编程中最快速和最松散的语言之一，但实际上，它有许多微妙的特性，可以帮助确保代码的意图相当清晰。

例如，如果您尝试像这样定义一个类...

```
# ./puppy.rb
class puppy
end 
```

翻译会告诉你

```
SyntaxError (./puppy.rb:2: class/module name must be CONSTANT) 
```

你将无话可说

常量有很多很好的用途，但它们最大的优势是帮助你组织你的代码，并提供关于文字用途的上下文。

```
class Action < ApplicationRecord
  scope :accepted, -> { where(type: "accept") }
  scope :rejected, -> { where(type: "reject") }
  scope :requested_changes, -> { where(type: "changes_requested") }

  # ...
end 
```

这些范围包含可能不会改变的字符串文字，但是假设这个应用程序的业务逻辑发生了重大重写，它们突然*发生了。*如果开发人员错过了该文字的一次调用——比方说，在另一个类似`user.rb`的文件中，您可能不会发现它，直到异常在您的运行时出现。

您可以通过将这些字符串文字移动到常量，然后引用它们来代替文字本身来防止这种情况发生:

```
class Action < ApplicationRecord
  module Type
    ACCEPT = "accept"
    REJECT = "reject"
    REQUEST_CHANGES = "changes_requested"
    MAKE_BETTER = "improvements_required"
    RUBY_PRETTIER = "improve_readability"

    ALL = [ACCEPT, REJECT, REQUEST_CHANGES, MAKE_BETTER, RUBY_PRETTIER]
  end

  scope :accepted, -> { where(type: Type::ACCEPT) }
  scope :rejected, -> { where(type: Type::REJECT) }
  scope :requested_changes, -> { where(type: Type::REQUEST_CHANGES) }
end 
```

您甚至可以在数组文本`ALL`中声明这些常量，并访问单独的字符串和集合，所有这些都可以通过一个简洁的声明来实现。

```
class Action < ApplicationRecord
  module Type
    ALL = [
      ACCEPT = "accept",
      REJECT = "reject",
      REQUEST_CHANGES = "changes_requested",
      MAKE_BETTER = "improvements_required",
      RUBY_PRETTIER = "improve_readability"  
    ]
  end

  # ...
end 
```

在您的终端:

```
 2019-03-03 15:33:33 ⌚ ruby 2.6.3p62 Newtons-Mac in ~/workspace/my_great_app
±  |master ✓| → rails console
Running via Spring preloader in process 90221
Loading development environment (Rails 5.2.3)
irb(main):001:0> Action::Type::ACCEPT
=> "accept"
irb(main):002:0> Action::Type::ALL
=> ["accept", "reject", "changes_requested", "improvements_required", "improve_readability"] 
```

在重构之后，您还将在您选择的 IDE 中获得制表符补全。奖金。👍

现在向前去，大大地开发✨🐶✨