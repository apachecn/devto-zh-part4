# 一勺冰糕——第一印象

> 原文：<https://dev.to/baweaver/a-scoop-of-sorbet-first-impressions-f30>

[![](img/625c90c27de33696544f01bb0d9d5265.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Gw29taoK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/0m7g9g9qli3ktoqgg08t.jpeg)

Stripe 刚刚发布了一款新的 Ruby 静态类型宝石，名为 Sorbet，此前它在类似于 Ruby Kaigi T1 这样的会议上被公开嘲笑了很长时间。

让我们看一看，看看我们有什么发现。这些将是我阅读图书馆的第一印象，它可能会随着使用和实验而改变。你的经历可能不一样，你也应该写出来！

# [首页](https://sorbet.org/)

条纹就是条纹，主页漂亮，干净，切中要害。我现在有点想做一个紫色的网站。不管怎样，撇开设计不谈...

我们从一个基本的例子开始:

```
# typed: true
extend T::Sig

sig {params(name: String).returns(Integer)}
def main(name)
  puts "Hello, #{name}!"
  name.length
end

main("Sorbet") # ok!
main()   # error: Not enough arguments provided
man("")  # error: Method `man` does not exist 
```

## 品尝冰糕

这里首先注意到的是`typed: true`的头部评论。我想知道这是不是一个编译器标志，并且是每个运行它的文件所必需的。我想是的，有点像 Ruby 中冻结的字符串。

我们从`T::Sig`扩展而来，我假设它是类型签名方法。

`sig`本身就带块。不喜欢无间隔的风格，因为这有点难读。添加几个空格可以更容易地进行可视化解析:

```
sig { params(name: String).returns(Integer) }
def main(name)
  puts "Hello, #{name}!"
  name.length
end 
```

但这更多的是个人喜好。

### 问题

我希望能够复制并粘贴第一个示例，并看到它按预期运行。考虑到我们还没有包含来自`sorbet`的任何内容，它将按原样失败。如果你愿意的话，五秒钟的行动会产生相当大的影响。我认为这是可能的，但可能会妨碍第一个例子，所以可以理解它不那么简单。

## 快速上手

我将在一个没有其他代码的项目中尝试这样做，看看会发生什么。如果失败了，我会切换到我的一个玩具宝石，看看它如何在一个项目中工作。

我们将从一个名为`strawberry_sorbet`的新准系统项目开始，它有一个`Gemfile`:

```
# Gemfile

source 'https://rubygems.org'

gem 'sorbet', group: :development
gem 'sorbet-runtime' 
```

然后安装:

```
➜  strawberry_sorbet git:(master) ✗ bundle
Using bundler 2.0.1
Using sorbet-static 0.4.4271 (universal-darwin-17)
Using sorbet 0.4.4271
Using sorbet-runtime 0.4.4271
Bundle complete! 2 Gemfile dependencies, 4 gems now installed.
Use `bundle info [gemname]` to see where a bundled gem is installed. 
```

现在让我们看看冰糕搭配`srb init`:

```
➜  strawberry_sorbet git:(master) ✗ srb init

👋 Hey there!

This script will get this project ready to use with Sorbet by creating a
sorbet/ folder for your project. It will contain:

- a config file
- a bunch of 'RBI files'

RBI stands for 'Ruby Interface'; these files define classes, methods, and
constants that exist, but which Sorbet doesn't always know about.

⚠️  Heads up:

To set up your project, this script will take two potentially destructive
actions:

1\.  It will require every file in your project. Specifically, every script in
    your project will be run, unless that script checks if __FILE__ == $PROGRAM_NAME
    before running any code, or has the magic comment # typed: ignore in it.

2\.  It will add a comment to the top of every file (like # typed: false or
    # typed: true, depending on how many errors were found in that file.)

❔ Would you like to continue? [Y/n] 
```

我有点想知道是否有一个`-y`标志来绕过那些知道他们想要它并且知道所述风险的项目。

是一种倒退，有一阵子没见过了。

让我们看看它跑吧！

```
❔ Would you like to continue? [Y/n] Y

Generating: sorbet/config
Generating: sorbet/rbi/sorbet-typed/
Generating: sorbet/rbi/gems/
Generating: sorbet/rbi/hidden-definitions/
// snipped
Generating split RBIs into sorbet/rbi/hidden-definitions/
Generating: sorbet/rbi/todo.rbi

✅ Done!

This project is now set up for use with Sorbet. The sorbet/ folder should exist
and look something like this: sorbet/
    ├──  config                      # Default options to passed to sorbet on every run
    └──  rbi/
        ├──  sorbet-typed/           # Community writen type definition files for your gems
        ├──  gems/                   # Autogenerated type definitions for your gems (from reflection)
        ├──  hidden-definitions/     # All definitions that exist at runtime, but Sorbet couldn't see statically
        └──  todo.rbi                # Constants which were still missing, even after the three steps above. Please check this whole folder into version control.

➡️   What's next?

Up to you! First things first, you'll probably want to typecheck your project: srb tc Other than that, it's up to you!
We recommend skimming these docs to get a feel for how to use Sorbet:
 - Gradual Type Checking (https://sorbet.org/docs/gradual)
- Enabling Static Checks (https://sorbet.org/docs/static)
- RBI Files (https://sorbet.org/docs/rbi)

If instead you want to explore your files locally, here are some things to try:
 - Upgrade a file marked # typed: false to # typed: true.
  Then, run srb tc and try to fix any errors.
- Add signatures to your methods with `sig`.
  For how, read: https://sorbet.org/docs/sigs
- Check whether things that show up in the TODO RBI file actually exist in your project.
  It's possible some of these constants are typos.
- Upgrade a file marked # typed: ignore to # typed: false.
  Then, run srb tc and try to fix any errors.

🙌  Please don't hesitate to give us your feedback! 
```

这是大量的文本，让我们来研究一下。我们将暂时忽略许多生成的文件，这可能是一篇完整文章的主题。

### 文件夹布局

```
This project is now set up for use with Sorbet. The sorbet/ folder should exist
and look something like this: sorbet/
    ├──  config                      # Default options to passed to sorbet on every run
    └──  rbi/
        ├──  sorbet-typed/           # Community writen type definition files for your gems
        ├──  gems/                   # Autogenerated type definitions for your gems (from reflection)
        ├──  hidden-definitions/     # All definitions that exist at runtime, but Sorbet couldn't see statically
        └──  todo.rbi                # Constants which were still missing, even after the three steps above. 
```

看着这棵树，很高兴能直观地表现出来。这里有一个[小错别字(`writen -> written` )](https://github.com/sorbet/sorbet/pull/967) ，但不是一个硬修复。

社区编写的定义和自动生成的 gem 类型定义很有趣，稍后我还将对此进行研究。

### 版本控制

```
Please check this whole folder into version control. 
```

注意，这应该被检查到版本控制中。考虑到这是一个类似模式的系统，确保不丢失输入信息是个好主意。不知道你有多想手动编辑这个文件夹中的东西，但我会假设至少有一些。

### 下一步

接下来还有一些后续步骤:

```
➡️   What's next?

Up to you! First things first, you'll probably want to typecheck your project: srb tc 
```

#### 首页示例

因此，我存储了示例问题，并注释掉了失败的行，以备后用，让我们看看它做了什么:

```
# 01_homepage.rb

# typed: strong

require 'sorbet'

extend T::Sig

sig {params(name: String).returns(Integer)}
def main(name)
  puts "Hello, #{name}!"
  name.length
end

main("Sorbet") # ok!
# main()   # error: Not enough arguments provided
# man("")  # error: Method `man` does not exist 
```

在工作文件上运行`srb tc`会产生:

```
➜  strawberry_sorbet git:(master) ✗ srb tc
No errors! Great job. 
```

#### 诱发错误

让我们取消第一个错误行注释:

```
main()   # error: Not enough arguments provided 
```

...再次运行它会给我们:

```
➜  strawberry_sorbet git:(master) ✗ srb tc
01_homepage.rb:14: Not enough arguments provided for method Object#main. Expected: 1, got: 0 https://srb.help/7004
    14 |main()   # error: Not enough arguments provided
        ^^^^^^
    01_homepage.rb:8: Object#main defined here
     8 |def main(name)
        ^^^^^^^^^^^^^^
Errors: 1 
```

哦，那太好了。它不仅跟踪出了主页上提到的错误，而且还给出了被调用方法的周围环境，因此我们可以潜在地看到哪里出错了。

我想知道如果我们传递一个无效类型给它会发生什么:

```
main(42) 
```

我们会得到:

```
01_homepage.rb:14: Integer(42) does not match String for argument name https://srb.help/7002
    14 |main(42)
        ^^^^^^^^
    01_homepage.rb:7: Method Object#main has specified name as String
     7 |sig {params(name: String).returns(Integer)}
                    ^^^^
  Got Integer(42) originating from: 01_homepage.rb:14:
    14 |main(42)
             ^^
Errors: 1 
```

因此，我们得到了值、类型、方法类型签名，以及其他一些关于跟踪到底出了什么问题的细节。他们在揭示上下文方面做得非常好，使得这个特殊的错误很容易被追踪和隔离。

我们将不得不在以后对此进行更深入的研究。

### 设计成交互式的

前一段时间，我有幸与 Matz 聊起了他对 Ruby 3 的一些梦想，在他的列表中排在前列的是一个语言服务器的想法，它可以与编辑器交互，并允许更干净的重构和编码。

看到它变成现实完全是另一回事。

如果这里有一个如何设置上述编辑器的链接就好了，因为我会很快点击它，看看它是如何工作的。

## 第一印象意见？

这让我想起了很多 Rust 和其他一些语言，感觉就像静态输入正确地给了你信息，而不是明显的迂腐。

我的观点可能会改变，因为我会继续玩下去。我打算着手的第一个项目是:

1.  如何使用它
2.  工会或产品类型如何工作
3.  如果系统能够处理类似`is_a?(Enumerable)`的接口
4.  语言服务器如何工作
5.  如何与 Whitequark 的解析器结合使用
6.  如果这可以与 Rubocop 一起用于重构/ codemods
7.  如果这可以潜在地用于范畴理论规范，比如 Ruby 中的幻想世界

我希望会有更多的，因为我有更多的机会玩这个，但这是第一次通读。

你好奇在冰糕中学到了什么？