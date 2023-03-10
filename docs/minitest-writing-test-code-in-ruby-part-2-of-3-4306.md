# 用 ruby _2/3 编写测试代码)

> 原文：<https://dev.to/exampro/minitest-writing-test-code-in-ruby-part-2-of-3-4306>

这是一系列文章，每篇文章都建立在另一篇之上。我建议从[第一部](https://dev.to/exampro/testunit-writing-test-code-in-ruby-part-1-of-3-44m2)开始。

**迷你系列**

*   [Test unit——用 Ruby 编写测试代码(1/3)](https://dev.to/exampro/testunit-writing-test-code-in-ruby-part-1-of-3-44m2)
*   [MiniTest——用 Ruby 编写测试代码(2/3)](https://dev.to/exampro/minitest-writing-test-code-in-ruby-part-2-of-3-4306)
*   [RSpec -用 Ruby (3/3)编写测试代码](https://dev.to/exampro/rspec-writing-test-code-in-ruby-part-3-of-3-32np)

## 迷你测试

MiniTest 与 UnitTest 的相似之处在于它具有 xUnit/TDD 风格的断言函数
。我们所指的这种风格是这样一个事实:我们的断言函数都是从`assert_`开始的

MiniTest 内置了更多的工具，使得管理和运行
测试更加容易。

MiniTest 也能够进行 spec/BDD 风格的测试，这将在后面讨论。
以下是 MiniTest 提供的各种现成承诺:

```
minitest/autorun - the easy and explicit way to run all your tests.
minitest/test - a very fast, simple, and clean test system.
minitest/spec - a very fast, simple, and clean spec system.
minitest/mock - a simple and clean mock/stub system.
minitest/benchmark - an awesome way to assert your algorithm's performance.
minitest/pride - show your pride in testing! 
```

Enter fullscreen mode Exit fullscreen mode

MiniTest 不是标准库。要安装 MiniTest，我们需要安装 gem。
Gem 就是 ruby 所说的库。
gem 通常使用 bundler 来管理，bundler 是一个包管理器。
我们将在未来的所有讲座中使用 bundler。

## 设置捆扎机并安装 MiniTest

### 安装捆扎机

要开始使用 bundler，我们必须通过 rubygems 安装 bundler gem。

```
gem install bundler 
```

Enter fullscreen mode Exit fullscreen mode

### 创建 Gemfile

一旦 bundler 安装完毕，我们现在就可以创建一个`Gemfile`。
A `Gemfile`定义了我们的代码需要依赖的所有宝石。
我们可以指定确切的 gem 版本，捆绑商可以自动执行这些 gem 的
安装和更新。

我们将继续创建我们的`Gemfile`

```
# Gemfile
source 'https://rubygems.org'

git_source(:github) do |repo_name|
  repo_name = "#{repo_name}/#{repo_name}" unless repo_name.include?("/")
  "https://github.com/#{repo_name}.git"
end

gem 'minitest' 
```

Enter fullscreen mode Exit fullscreen mode

在我们的`Gemfile`中，我们必须指定我们想在哪里得到我们的宝石。
所以我们在这里说我们想从`rubygems.org`
下载我们的宝石

```
source 'https://rubygems.org' 
```

Enter fullscreen mode Exit fullscreen mode

为了从 github 获取宝石，我们需要添加一个黑客。在过去，这种攻击是不需要的，但现在是所有 ruby 项目的标准。

```
git_source(:github) do |repo_name|
  repo_name = "#{repo_name}/#{repo_name}" unless repo_name.include?("/")
  "https://github.com/#{repo_name}.git"
end 
```

Enter fullscreen mode Exit fullscreen mode

然后我们可以列出我们想要安装的宝石，所以这里是我们
包括`minitest`
的地方

```
gem 'minitest' 
```

Enter fullscreen mode Exit fullscreen mode

### 捆绑安装

要通过 bundler 安装`minitest` gem，我们需要运行终端:

```
bundle install 
```

Enter fullscreen mode Exit fullscreen mode

Bundler 将继续安装或更新 gems，并生成一个称为锁文件的
`Gemfile.lock`。

### 锁定文件

锁定文件是为了锁定特定的 gem 版本，以确保其他想要运行我们代码的人不会因为使用错误的 gem 版本而遇到错误。

您不能手动修改锁定文件。

## 把我们的 UnitTest 变成 MiniTest

我们将测试上节课的`Hello`课。

```
# hello.rb
class Hello
  def self.world
    'world2'
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

我们之前使用`UnitTest`编写了一个`HelloTest`类。
让我们将其转换为使用`MiniTest`并比较其差异。

```
require 'minitest/autorun'
require_relative './hello'

class HelloTest < Minitest::Test
  def test_world
    assert_equal 'world', Hello.world, "Hello.world should return a string called 'world'"
  end

  def test_flunk
    flunk "You shall not pass"
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

我们只改变了两个要求:

```
require 'minitest/autorun' 
```

Enter fullscreen mode Exit fullscreen mode

以及我们正在扩展的类。

```
class HelloTest < Minitest::Test 
```

Enter fullscreen mode Exit fullscreen mode

`MiniTest`在 DSL 语法和断言函数命名上与`UnitTest`非常接近。
这不是巧合，因为它旨在帮助开发者将他们的`UnitTest`代码移植到`MiniTest`

## 运行 MiniTest

因为我们在运行测试文件
时使用 bundler 来管理我们的 gems，所以我们需要它在 bundler 的上下文中执行。我们可以通过使用`bundle exec`的
来做到这一点。这是我们运行测试的方式:

```
bundle exec ruby hello_test.rb 
```

Enter fullscreen mode Exit fullscreen mode

## 断言功能

我们之前看了一下`UnitTest`断言函数。让我们看看他们与`MiniTest` :
相比如何

```
assert
assert_empty
assert_equal
assert_in_delta
assert_in_epsilon
assert_includes
assert_instance_of
assert_kind_of
assert_match
assert_mock
assert_nil
assert_operator
assert_output
assert_predicate
assert_raises
assert_respond_to
assert_same
assert_send
assert_silent
assert_throws
capture_io
capture_subprocess_io
diff
exception_details
flunk
message
mu_pp
mu_pp_for_diff
pass
refute
refute_empty
refute_equal
refute_in_delta
refute_in_epsilon
refute_includes
refute_instance_of
refute_kind_of
refute_match
refute_nil
refute_operator
refute_predicate
refute_respond_to
refute_same
skip
skipped? 
```

Enter fullscreen mode Exit fullscreen mode

许多相同的断言函数已经存在，而且还有以前不存在的新函数。
`MiniTest`和`UnitTest`不是一对一的。

## 这么多分机

MiniTest 有数百个 gems 形式的扩展。
这是优于`UnitTest`的一个优势，因为您可以根据自己的需求定制测试框架。
这里有几个例子:

```
minitest-line    - Run test at line number.
minitest-logger  - Define assert_log and enable minitest to test log messages. Supports Logger and Log4r::Logger.
minitest-osx     - Reporter for the Mac OS X notification center.
minitest-profile - List the 10 slowest tests in your suite.
minispec-rails   - Minimal support to use Spec style in Rails 5+. 
```

Enter fullscreen mode Exit fullscreen mode

## MiniTest 的替代规范/BDD 语法

到目前为止，我们一直在使用测试驱动开发(TDD ),这是一种用于编写测试代码的
方法。使用行为驱动开发(BDD)有另一种更新的方式来编写
测试(被称为)。

BDD 的目标是定义更易于人类阅读的测试，并且以更有可能覆盖所有边缘情况的方式编写。

让我们将我们的 TDD MiniTest 转换成 BDD。

```
# hello_spec.rb
require 'minitest/autorun'
require_relative './hello'

describe Hello do
  describe "#world" do
    it "should return world" do
      Hello.world.must_equal 'world'
    end
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

BBD 使用使用 ruby 块的大型 DSL`function value do`。
断言函数的使用和命名都不同。

`assert_equal`现在变成了`must_equal`
断言函数现在使用一种称为链接的语法。
这些变化旨在使测试更具可读性。

让我们来比较:

```
# TDD "test"
assert_equal 'world', Hello.world, "Hello.world should return a string called 'world'"

# BDD "spec"
it "should return world" do
  Hello.world.must_equal 'world'
end 
```

Enter fullscreen mode Exit fullscreen mode

## 我们可以像测试一样运行这个规范文件:

```
bundle exec ruby hello_spec.rb 
```

Enter fullscreen mode Exit fullscreen mode

## 关于 MiniTest 的思考

`MiniTest`可以处理 TDD 和 BDD 两种语法。
它附带了一系列测试工具，使自动化变得容易
有几个扩展可以根据您的需求定制`MiniTest`。将`TestUnit`移植到`MiniTest`相当容易。
`MiniTest`必须通过捆扎机安装。

在下一节课中，我们将讨论 RSpec，它仅仅是一个 BDD 工具。

## 代码