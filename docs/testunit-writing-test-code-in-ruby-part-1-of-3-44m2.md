# Test unit——用 Ruby 编写测试代码(1/3)

> 原文：<https://dev.to/exampro/testunit-writing-test-code-in-ruby-part-1-of-3-44m2>

**迷你系列**

*   [Test unit——用 Ruby 编写测试代码(1/3)](https://dev.to/exampro/testunit-writing-test-code-in-ruby-part-1-of-3-44m2)
*   [MiniTest——用 Ruby 编写测试代码(2/3)](https://dev.to/exampro/minitest-writing-test-code-in-ruby-part-2-of-3-4306)
*   [RSpec -用 Ruby (3/3)编写测试代码](https://dev.to/exampro/rspec-writing-test-code-in-ruby-part-3-of-3-32np)

学习的前奏
我正在培训我的三年级学生如何编写测试代码，因为我们需要提高我们在 ExamPro 中的代码覆盖率。上周日，我迅速整理了三份关于 TestUnit、MiniTest 和 RSpec 的指南。为什么是三个？我相信通过变化学习会产生完整的知识。

我想我应该“按原样”在 DEV.to 文章上发表这三篇文章，否则我会忘记它们，它们会被放在我的“转储”文件夹中，再也不会被看到。

我已经写好了对这些指南的挑战。不知道大家要不要。给我看足够多的反应，想看就在下面评论，有兴趣我会找时间发表的。

## TestUnit

TestUnit 是一个标准的 ruby 库。标准的 ruby 库意味着你不必安装 gem，因为它已经包含在语言中了。

使用 TestUnit 的最简单的例子如下。

```
require "test/unit/assertions"
include Test::Unit::Assertions

hello = 'world'

assert_equal 'world', hello, "hello function should return 'world'" 
```

Enter fullscreen mode Exit fullscreen mode

## 断言功能

`assert_equal`是许多断言函数中的一个。
断言函数是所有测试库的一部分，用来断言
某事是否会通过或失败。断言是你的“测试”。

下面是 TestUnit 中所有断言函数的列表。在实践中，你会用到一些这样的断言函数。

```
assert
assert_block
assert_boolean
assert_compare
assert_const_defined
assert_empty
assert_equal
assert_fail_assertion
assert_false
assert_in_delta
assert_in_epsilon
assert_include
assert_instance_of
assert_kind_of
assert_match
assert_nil
assert_no_match
assert_not_const_defined
assert_not_empty
assert_not_equal
assert_not_in_delta
assert_not_in_epsilon
assert_not_include
assert_not_match
assert_not_nil
assert_not_predicate
assert_not_respond_to
assert_not_same
assert_not_send
assert_nothing_raised
assert_nothing_thrown
assert_operator
assert_path_exist
assert_path_not_exist
assert_predicate
assert_raise
assert_raise_kind_of
assert_raise_message
assert_raises
assert_respond_to
assert_same
assert_send
assert_throw
assert_throws
assert_true
build_message
flunk 
```

Enter fullscreen mode Exit fullscreen mode

## 断言并失败

最基本的断言函数是`assert`和`flunk`。

函数签名定义了函数的输入和输出。
你可以在
你的语言和库的技术文档中找到函数签名。让我们看看在 [RubyDocs](https://www.rubydoc.info/gems/test-unit/2.3.0/Test/Unit/Assertions#assert-instance_method) 上定义的
assert 和`flunk`的函数签名。

### 断言

这是`assert`
的功能签名

```
#assert(boolean, message = nil) ⇒ Object 
```

Enter fullscreen mode Exit fullscreen mode

这里我们可以看到`assert`接受两个参数作为输入:

1.  布尔-这是必需的
2.  消息-这是可选的

并且它输出一个`Object`。

这就是我们如何使用这个函数

```
# simple.rb
require "test/unit/assertions"
include Test::Unit::Assertions

x = true

assert x, "x should pass" 
```

Enter fullscreen mode Exit fullscreen mode

### 不及格

这是`flunk`
的功能签名

```
#flunk(message = "Flunked") ⇒ Object 
```

Enter fullscreen mode Exit fullscreen mode

这里我们可以看到`flunk`接受一个参数作为输入:

1.  消息-如果没有提供值，则默认为“不及格”

并且它输出一个`Object`。

这就是我们如何使用这个函数

```
# flunk.rb
require "test/unit/assertions"
include Test::Unit::Assertions

flunk "throw a failure message" 
```

Enter fullscreen mode Exit fullscreen mode

`flunk`的目的是当你做一个总是失败的测试时。
你可能在`flunk`找不到实用的东西

## 如何正确地用 TestUnit 编写测试

上面的例子是使用 TestUnit 最少的行数，但是这不是正确的方法。
正确的做法是创建一个单独的文件。
这个新的测试文件将需要我们希望测试的代码，我们需要创建测试用例类，然后编写测试函数。

### Hello World TestCase 示例

我们将在一个名为`hello.rb`的文件中创建一个简单的类。
这个类将有一个名为`self.hello`的类方法，它将返回一个字符串。

```
# hello.rb
class Hello
  def self.world
    'world2'
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

我们将创建一个名为`hello_test.rb`
的新文件。注意，我们给了这个文件名一个准确的名字，然后在它的末尾附加了`_test`。
你会发现，当你有更多的测试文件时，你会因为自动化工具而想要坚持这个惯例。

```
# hello_test.rb
require "test/unit"
require_relative './hello'

class HelloTest < Test::Unit::TestCase
  def test_world
    assert_equal 'world', Hello.world, "Hello.world should return a string called 'world'"
  end

  def test_flunk
    flunk "You shall not pass"
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

我们定义的类不是普通的类，而是一种**领域特定语言(DSL)** 。
DSL 就是你改变你的代码的行为，使之成为一种语言中的一种语言。
ruby 语言是一种可塑性很强的语言，因此很容易编写 DSL。
当提到 DSL 时，ruby 社区经常说代码是“神奇的”。
DSL 的规则并不总是很清楚，这可能会导致常见的混淆。

`HelloTest`类扩展了`Test::Unit::TestCase`,这是将这个类变成 DSL 的“魔法”。
当该文件被执行时，例如

```
ruby hello_test.rb 
```

Enter fullscreen mode Exit fullscreen mode

它将输出测试的结果。
它将执行以`test_`开头的`HelloTest`类中的每个实例函数

1.  测试 _ 世界
2.  测试 _ 不及格

如果实例函数没有用`test_`命名，那么它将不会运行。
这是本 DSL 规则的一部分。

### 关于 TestUnit 的想法

`TestUnit`不是 ruby 中唯一的测试框架，因为我们还有`MiniTest`和`Rspec`。
这些其他测试框架有不同的权衡，但功能相似。

`TestUnit`是古老而简单的测试框架。

在接下来的讲座中，我们将探索`MiniTest`和`Rspec`以及
之间的取舍。

所有的框架都被使用，很难说哪一个是最常用的
，这取决于团队的偏好。

## 代码