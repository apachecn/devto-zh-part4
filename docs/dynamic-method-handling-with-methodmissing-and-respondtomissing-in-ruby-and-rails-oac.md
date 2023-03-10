# 用#method_missing 和#respond_to_missing 进行动态方法处理？在 Ruby 和 Rails 中

> 原文：<https://dev.to/morinoko/dynamic-method-handling-with-methodmissing-and-respondtomissing-in-ruby-and-rails-oac>

我刚刚读完《[扎实的 Rubyist](https://www.manning.com/books/the-well-grounded-rubyist-third-edition) 》，终于了解了神秘的`#method_missing`方法。

每当一个对象收到一个它不知道如何处理的方法调用时，就会被调用。也许它从未被定义，拼写错误，或者由于某种原因，ruby 在对象的方法查找路径上找不到它。

很酷的一点是，您可以使用`#method_missing`来捕捉您想要动态处理的某些方法。这里有一个从[这位有良好基础的 Rubyist](https://www.manning.com/books/the-well-grounded-rubyist-third-edition) 那里借来的例子:

```
class Student
  def method_missing(method, *args)
    if method.to_s.start_with?("grade_for_")
      puts "You got an A in #{method.to_s.split("_").last.capitalize}!"
    else
      super
    end
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

这段代码允许你动态地调用任何方法，比如`grade_for_english`、`grade_for_science`等等。在学生对象上调用，而不需要为每个主题显式定义几个类似的方法。

被调用方法的名称总是通过`method`参数以符号的形式提供，因此您可以在重新定义的版本中相应地使用它。`*args`参数获取传递的任何参数。它也可以采用一个可选的块，但我不会在这里深入讨论。

确保你总是在重定义的`#method_missing`的末尾包含`super`，这样任何最终不能被实际处理的方法都会被传递回`#method_missing`的原始定义

考虑到我的个人项目，我意识到`#method_missing`最终会让我解决困扰我一段时间的一个问题，所以我将用它作为一个真实世界的简单例子来说明我如何使用`#method_missing`来重构我的代码。

# 背景&原码

我有一个基本的网络应用程序，为农民出售盒装有机蔬菜，有点像 CSA，但在一次性的基础上，而不是季节性的。无论如何，该网站有英语和日语两种版本，因此对象的数据需要以两种语言存储。在这种情况下，我有一个名为`VeggieBox`的类来跟踪季节性蔬菜盒。

我设置了应用程序，以便它根据页面的区域设置(`:en`或`:ja`)从数据库中提取数据。这使我能够在两种语言之间共享单个视图文件，而不是为每种语言共享单独的视图。

`VeggieBox`类创建带有标题和描述的对象。为了处理不同的语言，属性被存储为`title_ja`、`title_en`、`description_ja`和`description_en`。

现在让我们创建一个示例框:

```
@veggie_box = VeggieBox.new
@veggie_box.title_en = "Summer box"
@veggie_box.title_ja = "夏のボックス"
@veggie_box.description_en = "Tons of delicious summer veggies!!"
@veggie_box.description_ja = "美味しい夏野菜いっぱい！！" 
```

Enter fullscreen mode Exit fullscreen mode

因为视图文件是共享的，所以我必须弄清楚如何根据用户查看的是英文还是日文站点来显示正确的数据。为此，我最初创建了`#localized_title`和`#localized_description`，它们使用`#send`在 I18n 语言环境中解析并调用适当的方法:

```
# app/models/veggie_box.rb

class VeggieBox < ApplicationRecord
  # ... other methods

  def localized_title
    self.send("title_#{I18n.locale}")
  end

  def localized_description
    self.send("description_#{I18n.locale}")
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

根据用户查看的页面，区域设置为`en`或`ja`。因此，如果用户在英文页面上，`@veggie_box.localized_title`就相当于说`@veggie_box.title_en`，在视图中拉出正确的文本就像:
一样简单

```
<div class="description">
  <h3><%= @veggie_box.localized_title %></h3>
  <p><%= @veggie_box.localized_description %></p>
</div> 
```

Enter fullscreen mode Exit fullscreen mode

这是一个相当简单的解决方案，但是不够灵活。如果我开始向`VeggieBox`类添加越来越多的属性，会发生什么？我必须为每个属性添加一个新的`localize_***`实例方法！

这就是`#method_missing`的用武之地。

# 重构 1

为了避免使用附加了`localized_`的方法，我决定将其简化为基本属性名，这样我就可以只写`@veggie_box.title`或`@veggie_box.description`来代替。

我第一次尝试使用`#method_missing`是这样的:

```
# app/models/veggie_box.rb

class VeggieBox < ApplicationRecord
  # ... other methods

  def method_missing(method, *args)
    if method == "description" || "title"
      localized_method = "#{method}_#{I18n.locale}"
      self.send(localized_method)
    else
      super
    end
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

如果应用遇到类似`@veggie_box.title`的情况，它不会立即知道该做什么，因为 VeggieBox 对象正在调用一个没有明确定义的方法；在这种情况下`title`。它被重定向到在`VeggieBox`类中定义的`#method_missing`方法，该方法捕获 if 子句`if method == "description" || "title"`中的未知方法`title`。如果用户查看的是英文页面，对`title`的调用会被处理成`title_en`，如果是日文页面，则被处理成`title_ja`，而不是抛出一个 NoMethodError。

我们可以在控制台上试试:

```
I18n.locale = :en
@veggie_box.title
 => "Summer box"

I18n.locale = :ja
@veggie_box.title
 => "夏のボックス" 
```

Enter fullscreen mode Exit fullscreen mode

稍微好一点，但是方法名仍然被硬编码到`if`-子句中！

我的下一个重构将去掉`#method-missing`定义中的硬编码。

# 重构 2

为了去掉硬编码的值，我决定通过添加 locale，然后简单地检查 veggie box 对象是否响应该方法，将方法名转换为本地化的属性方法(例如`title_ja`)。如果是这样，则使用 send 在对象上调用本地化方法。如果没有，它将被发送到`super`。

下面是重构后的代码:

```
def method_missing(method, *args)
  localized_method = "#{method}_#{I18n.locale}"

  if self.respond_to?(localized_method)
    self.send(localized_method)
  else
    super
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

现在，如果将另一个基于区域设置的属性添加到`VeggieBox`类中，`#method_missing`将能够处理它而无需编辑代码。

但是在使用`#method_missing`时出现了一个小问题，我将在最后的重构中解决这个问题。

# 重构 3

虽然`#method_missing`会让你的对象正确地动态处理某些方法调用，但它不允许它们像“真正的方法”一样工作。这意味着 ruby 在其他地方无法识别它们，例如，当你使用`object.respond_to?` :

```
@veggie_box.respond_to?(:title)
 => false

@veggie_box.respond_to?(:description)
 => false 
```

Enter fullscreen mode Exit fullscreen mode

`respond_to?`对`#method_missing`处理的方法返回`false`！这是不对的，因为从技术上来说，它确实响应了这些方法，尽管它们没有被定义。

像`object.method`这样让你把方法变成对象的东西也不行:

```
@veggie_box.method(:title)
=> NameError (undefined method `title' for class `#<Class:0x00007fc56289b2a8>') 
```

Enter fullscreen mode Exit fullscreen mode

在查看了 [Ruby 风格指南中关于元编程的章节](https://rubystyle.guide/#no-method-missing)和[这篇伟大的文章](http://blog.marc-andre.ca/2010/11/15/methodmissing-politely/)之后，我意识到了这一点。他们建议在使用`#method_missing`时也定义`#respond_to_missing?`，这允许 ruby 正确地识别出你的对象确实响应了元编程方法。(实际上，Ruby 风格指南建议*而不是*使用`#method_missing`...哎呀)。

在我的例子中，我是这样定义`#respond_to_missing?`的:

```
def respond_to_missing?(method, *)
  localized_method = "#{method}_#{I18n.locale}"
  attribute_names.include?(localized_method) || super
end 
```

Enter fullscreen mode Exit fullscreen mode

这里，我使用了 Rails 的 ActiveRecord 方法`#attribute_names`来确保属性注册到了类中。如果是，它通过了`respond_to`测试。

现在，当被问及是否对`#title`或`#description`做出回应时，每个对象都会返回`true`。

```
@veggie_box.respond_to?(:title)
=> true

@veggie_box.respond_to?(:description)
=> true 
```

Enter fullscreen mode Exit fullscreen mode

这些方法现在也可以被对象化:

```
method = @veggie_box.method(:title)

I18n.locale = :en
method.call
 => "Summer box"

I18n.locale = :ja
method.call
 => "夏のボックス" 
```

Enter fullscreen mode Exit fullscreen mode

# 测试信心

考虑到这是我第一次使用`#method_missing`，我不是 100%确信代码会正确工作，所以我在元编程方法的测试套件(MiniTest)中添加了一个测试，给自己更多的信心。即使你有 100%的信心，无论如何进行测试也是一个好主意:)

测试确保蔬菜盒的实例确实响应元编程方法。随后的`assert_equal`测试首先将区域设置为`:en`或`:ja`，并确保元编程方法返回与原始属性方法相同的值。

```
require 'test_helper'

class VeggieBoxTest < ActiveSupport::TestCase

  def setup
    # sets up a test object from a fixture file
    @veggie_box = veggie_boxes(:box_one)
  end

  # ...other tests...

  test "should respond correctly to localized methods" do
    # Make sure it actually responds to the meta-programmed methods
    assert_respond_to @veggie_box, :title
    assert_respond_to @veggie_box, :description

    # Make sure the method gets the correct values for each locale by 
    # comparing to the original attributes
    I18n.locale = :ja
    assert_equal @veggie_box.title_ja, @box.title
    assert_equal @veggie_box.description_ja, @box.description

    I18n.locale = :en
    assert_equal @veggie_box.title_en, @box.title
    assert_equal @veggie_box.description_en, @box.description
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

希望你喜欢这篇文章，如果你以前没有用过`#method_missing`，希望这会有所帮助！

# 参考文献

*   大卫·a·布莱克和约瑟夫·利奥三世的《扎实的 Rubyist》
*   [红宝石风格指南](https://rubystyle.guide/#no-method-missing)
*   [Method_missing，客气地说](http://blog.marc-andre.ca/2010/11/15/methodmissing-politely/)博客文章
*   关于 method_missing 的 Ruby 文档(英语)
*   关于 method_missing 的 Ruby 文档(日文版——有很好的代码示例，即使你看不懂解释)