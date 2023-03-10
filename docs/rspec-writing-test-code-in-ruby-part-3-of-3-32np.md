# r spec——用 Ruby 编写测试代码(3/3)

> 原文：<https://dev.to/exampro/rspec-writing-test-code-in-ruby-part-3-of-3-32np>

这是一系列文章，每篇文章都建立在另一篇之上。我建议从[第一部](https://dev.to/exampro/testunit-writing-test-code-in-ruby-part-1-of-3-44m2)开始。

**迷你系列**

*   [Test unit——用 Ruby 编写测试代码(1/3)](https://dev.to/exampro/testunit-writing-test-code-in-ruby-part-1-of-3-44m2)
*   [MiniTest——用 Ruby 编写测试代码(2/3)](https://dev.to/exampro/minitest-writing-test-code-in-ruby-part-2-of-3-4306)
*   [RSpec -用 Ruby (3/3)编写测试代码](https://dev.to/exampro/rspec-writing-test-code-in-ruby-part-3-of-3-32np)

## RSpec

RSpec 纯粹是一个 BDD 测试框架。在 RSpec 中，测试被称为规范。
规格文件必须以`_spec.rb`结尾

RSpec 与 MiniTest 的不同之处在于，它的 DSL 提供了更多的魔力，使规范更易于阅读。

RSpec 也有更丰富的为 BDD 设计的扩展。

`RSpec`还与`Cucumber`用户接受度
框架无缝集成。

## 通过捆绑器安装 RSpec

我们的 Gemfile 将与之前的讲座相同，除了
我们将把`minitest`换成`rspec`

```
# Gemfile
source 'https://rubygems.org'

git_source(:github) do |repo_name|
  repo_name = "#{repo_name}/#{repo_name}" unless repo_name.include?("/")
  "https://github.com/#{repo_name}.git"
end

gem 'rspec' 
```

Enter fullscreen mode Exit fullscreen mode

然后我们需要安装 rspec

```
bundle install 
```

Enter fullscreen mode Exit fullscreen mode

## 初始化 RSpec

Rspec 使我们可以很容易地建立一个传统的目录结构。将我们的规格文件保存在一个地方是很重要的。

我们可以通过运行以下命令来初始化 RSpec:

```
rspec --init 
```

Enter fullscreen mode Exit fullscreen mode

然后，您应该会看到它创建了以下内容:

```
create   .rspec
create   spec/spec_helper.rb 
```

Enter fullscreen mode Exit fullscreen mode

RSpec 将创建一个`.rspec`文件。
`.rspec`文件允许我们包含或排除要运行的不同目录
或文件。

它还将创建一个`spec`目录来存放我们所有的规范文件。

`spec_helper.rb`是一个文件，用于更改我们希望
RSpec 如何运行的配置。

## 使用 RSpec 创建我们的 HelloSpec

我们需要创建我们的`hello_spec.rb`并将其放在`spec`目录中，我们将创建我们的文件并比较`MiniSpec`和
之间的差异

```
# spec/hello_spec.rb
require_relative '../hello'

RSpec.describe Hello do
  context "#world" do
    it { expect(Hello.world).to eql 'world' }
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

### 没有要求的必要

您会注意到我们不需要要求 RSpec。当 Spec 目录中有 spec 文件
时，RSpec 会自动包含其自身。这种行为被开发商
称为魔术。

### 目录的变更

我们不得不改变路径来要求我们的文件，因为它现在在一个
目录中。`../`表示上一级目录，其中`./`表示在
当前目录
内

```
require_relative '../hello' 
```

Enter fullscreen mode Exit fullscreen mode

### 指定 RSpec

我们需要将`RSpec.`放在 describe 前面。
如果我们使用 Rails，就可以排除`RSpec.`，但情况并非总是如此。

```
RSpec.describe Hello do 
```

Enter fullscreen mode Exit fullscreen mode

### 匹配器和人类可读性

让我们比较一下匹配器的区别。

```
# MiniTest
it "should return world" do
  Hello.world.must_equal 'world'
end

# RSpec
it { expect(Hello.world).to eql 'world' } 
```

Enter fullscreen mode Exit fullscreen mode

您可以看到，Rspec 的 DSL 允许以更简洁的方式编写规范。

## 运行 RSpec

RSpec 知道它期望你的规范文件在哪里，所以你可以简单地运行

```
rspec 
```

Enter fullscreen mode Exit fullscreen mode

## 关于上海石化的思考

RSpec 是纯粹的 BBD
它可以产生更简洁的 DSL 语法
它使用了更多的 ruby 魔法，这可能会导致混乱。
它有很强的组织文件的惯例，这也减少了手动配置的数量。
RSpec 在规模上比 MiniTest 和 TestUnit 慢得多。

## 代码