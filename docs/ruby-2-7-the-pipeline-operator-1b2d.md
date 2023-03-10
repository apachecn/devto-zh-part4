# Ruby 2.7:管道操作者

> 原文：<https://dev.to/baweaver/ruby-2-7-the-pipeline-operator-1b2d>

Ruby 2.7 增加了管道操作符(`|>`)，但不是以许多 Ruby 爱好者预期的方式。

[合并可以在这里找到](https://github.com/ruby/ruby/commit/f169043d81524b5b529f2c1e9c35437ba5bc3a7a)。

**更新**:

[Matz 在 Twitter 上表达了他正在寻求对此功能的意见](https://twitter.com/yukihiro_matz/status/1139419951444271104)

请保持反馈的建设性和文明性(MINASWAN)，并关注他提到的 [LISP-1/LISP-2 命名空间](http://ergoemacs.org/emacs/lisp1_vs_lisp2.html)问题，因为它们是实现真正的灵丹妙药般的管道的真正问题。

关于 Ruby Bugtracker 的讨论正在进行

**更新 2**

这篇文章是历史性的。Ruby 中没有管道操作符，讨论后拒绝[这里](https://bugs.ruby-lang.org/issues/15799#note-46):

> 经过实验，| >所引起的困惑和争议远远超过了我的预料。我仍然重视链接操作符，但是缺点大于好处。所以我现在干脆放弃这个想法。也许我们将来会重新考虑这个想法(使用不同的操作符外观)。
> 
> 在讨论中，我们在允许的方法链中引入了注释。它不会被删除。
> 
> 马茨。

## 它有什么作用

管道操作符实际上是点链接的别名，来自源:

```
# This code equals to the next code.

foo()
  |> bar 1, 2
  |> display

foo()
  .bar(1, 2)
  .display 
```

Enter fullscreen mode Exit fullscreen mode

...并且从测试代码:

```
def test_pipeline_operator
  assert_valid_syntax('x |> y')
  x = nil
  assert_equal("121", eval('x = 12 |> pow(2) |> to_s(11)'))
  assert_equal(12, x)
end 
```

Enter fullscreen mode Exit fullscreen mode

正如所写的，这是管道操作符的当前实现，正如在 [Ruby bug tracker](https://bugs.ruby-lang.org/issues/15799) 中所讨论的。

Chris Salzberg [在问题中提到](https://bugs.ruby-lang.org/issues/15799#note-21)，原因可能是它的优先级低于点运算符:

> 运算符的优先级比`.`低，因此您可以这样做:
> 
> ```
> a .. b |> each do
> end 
> ```
> 
> 对于`.`，由于它的优先级更高，您必须使用大括号:
> 
> ```
> (a..b).each do
> end 
> ```

...他在结尾写道:

> 不过，我认为更重要的一点是，重大的争议性决策是基于这种非常简短的、几乎是不公开的讨论做出的。
> [我不认为这对我们的社区是件好事。](https://twitter.com/shioyama/status/1139168800794791936)

我倾向于同意他和其他几个人对这一变化的讨论，并将在下面列出我的理由，包括赞成的观点和反对的观点。

## 分赞成

在赞成的观点中，我看到了一些:

### Ruby 已经有了多种块语法

[提到的一点](https://github.com/ruby/ruby/commit/f169043d81524b5b529f2c1e9c35437ba5bc3a7a#commitcomment-33926904)是 Ruby 已经有了多种创建块的语法，比如`do ... end`对`{ ... }`。

流水线被看作是多行方法的一种方式，它使正在做的事情更加清楚:

```
# Original
foo.bar.baz

# Current
foo
  .bar
  .baz

# Alternate Current:
foo.
  bar.
  baz

# Pipelined:
foo
|> bar
|> baz 
```

Enter fullscreen mode Exit fullscreen mode

#### 对位

我对此的反对意见是，Ruby 块已经让新开发人员感到困惑，并且在学习 Ruby 时经常成为争论的话题。

添加一个没有明确区分因素的新语言结构只会加剧这种情况，并使语言更难学习。

### 优先

[提出的另一点](https://bugs.ruby-lang.org/issues/15799#note-21)是流水线操作符的优先级比`.`低，允许如上所述的无父代编程:

```
# Current
(a..b).each do
end

# Pipelined
a .. b |> each do
end 
```

Enter fullscreen mode Exit fullscreen mode

#### 对位

我们还有比英语操作符`and`和`or`更优先的参数，这两个操作符通常被认为在语言中不常用，因为它们可能会引起混淆。

正如上面提到的不同优先级的相同任务使用多种语法的问题一样，这也会让新程序员感到困惑。

## 主要反对意见

对于最近有争议的特性，如[模式匹配](https://dev.to/baweaver/ruby-2-7-pattern-matching-first-impressions-13j9)和[编号参数](https://dev.to/baweaver/ruby-2-7-numbered-parameters-1a79)，关于确切的语法一直存在争议，但讨论中有几个支持该特性，因为它为语言添加了一些明显的新内容。

在这种情况下，我认为情况并非如此。新的管道操作符感觉像是`.`的别名，是一种语法糖，而实际上它更适合于这门语言。

主要的优势在于优先级评估的不同，这个问题在过去给新的 Ruby 程序员带来了很大的困惑，并且一直持续到今天。

在一种语言中引入一个新的符号应该会增加一种新的、更有表现力的方式来用那种语言做事。我不认为这达到了那个目的。

## 会是什么呢？

相反的主要观点是，其他语言中的管道对于代码来说是一个强大而富于表现力的特性。我将向您展示其中的一些实现，并让您自己判断它们的优点。

### Javascript 中的

TC39 已经[讨论了一个管道运营商](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Pipeline_operator)，目前正在仔细评估中。由于 Javascript 是一种与 Ruby 非常相似的语言，所以这两种语言之间有许多共同的想法，而且还可能有更多的想法。

在他们的例子中:

```
const double = (n) => n * 2;
const increment = (n) => n + 1;

// without pipeline operator
double(increment(double(double(5)))); // 42

// with pipeline operator
5 |> double |> double |> increment |> double; // 42 
```

Enter fullscreen mode Exit fullscreen mode

管道运算符用于将左侧的输出提供给右侧的输入。它明确要求这是函数的唯一参数，这可以通过两种语言中的 currying 技术来部分应用函数。

#### 应用于 Ruby 的 Javascript 管道

这非常类似于 Ruby 约定的`then` :

```
double = -> n { n * 2 }
increment = -> n { n + 1 }

double[increment[double[double[5]]]]
# => 42

5.then(&double).then(&double).then(&increment).then(&double) 
```

Enter fullscreen mode Exit fullscreen mode

如果管道操作符是能够合理推断出`to_proc` / `&`和方法调用的`then`的别名，它看起来将与 Javascript:
非常相似

```
5 |> double |> double |> increment |> double
# => 42 
```

Enter fullscreen mode Exit fullscreen mode

这实现了几个主要项目:

1.  它消除了使用`&`进行显式块标记的需要
2.  它简化了代码的`then`变体
3.  它(理想情况下)可以智能地处理方法和过程

通过处理方法和过程，我的意思是这不会改变上面代码的输出:

```
def double(n)
  n * 2
end

increment = -> n { n + 1 }

5 |> double |> double |> increment |> double
# => 42 
```

Enter fullscreen mode Exit fullscreen mode

我认为，这是一个更符合 ruby 的实现，更富表现力和优雅，允许更简单的语法无缝地表达更复杂的思想。

它使用操作符的鸭式输入的思想来说，这两个应该行为相同，以实现一个非常强大的语法。

### [OCaml 和 F 中的](#in-ocaml-and-f)

OCaml 和 F#中的[示例](https://fsharpforfunandprofit.com/posts/function-composition/#composition-vs-pipeline)看起来非常相似，所以我们将把重点放在 F#变体上，它也强调了这和 composition 之间的区别(我不会在这里介绍，但值得一读):

```
let (|>) x f = f x 
```

Enter fullscreen mode Exit fullscreen mode

它的工作原理是将最后一个参数输入函数:

```
let doSomething x y z = x+y+z
doSomething 1 2 3       // all parameters after function
3 |> doSomething 1 2    // last parameter piped in 
```

Enter fullscreen mode Exit fullscreen mode

我不是很熟悉 F#，但这似乎是一个 currying 实现，它实现了 Javascript 示例中提到的一些功能

#### F#管道应用于 Ruby

这引入了一个有趣的 currying 思想，即将参数应用于函数，并等待最后一个参数调用以获取值。我们在 Ruby 中已经有了这个`curry` :

```
adds = -> a, b { a + b }.curry

adds[2, 3]
# => 5

adds[2]
# => #<Proc:0x00007fe8ab6996a0 (lambda)>
adds[2][3]
# => 5 
```

Enter fullscreen mode Exit fullscreen mode

虽然我不认为 auto-currying 能很好地匹配 Ruby，但它会给管道操作符增加一个有趣的功能:

```
adds = -> a, b { a + b }.curry

def double(n)
  n * 2
end

increment = -> n { n + 1 }

5 |> adds[2] |> double |> increment
# => 15 
```

Enter fullscreen mode Exit fullscreen mode

### 换仙丹

[Elixir 与 Javascript 实现的工作方式](https://elixirschool.com/en/lessons/basics/pipe-operator/)非常相似，除了它还可以“软处理”那些在管道中等待额外输入的函数。

再说一次，我对仙丹还不太熟悉，欢迎大家指正:

```
"Elixir rocks" |> String.upcase() |> String.split()
["ELIXIR", "ROCKS"]

"elixir" |> String.ends_with?("ixir")
true 
```

Enter fullscreen mode Exit fullscreen mode

有趣的是，它与 Ruby 在这方面的一个问题相冲突，即圆括号中的模糊语法:

```
iex> "elixir" |> String.ends_with? "ixir"
warning: parentheses are required when piping into a function call.
For example:

  foo 1 |> bar 2 |> baz 3

is ambiguous and should be written as

  foo(1) |> bar(2) |> baz(3)

true 
```

Enter fullscreen mode Exit fullscreen mode

## 兼收并蓄

现在有一个令人震惊的发现:我喜欢用它作为`.`、*和*的别名....

...它应该是这两者和来自其他语言的上述思想的结合:

```
5
|> double
|> increment
|> to_s(2)
|> reverse
|> to_i 
```

Enter fullscreen mode Exit fullscreen mode

这使我们在表达能力上有了实质性的提高，同时将当前的实现与其他语言中已有的思想统一起来。如果这样的实现能够投入使用，我会非常兴奋，因为它将把函数世界的精华与 Ruby 的自然面向对象结合起来，实现一些全新的东西。

对我来说，这就是 Ruby，用来自世界各地和不同语言的想法实现新的东西。我们把新奇和激动人心的东西结合在一起，使之成为我们自己的，我相信通过管道运营商，我们有一个惊人的机会做到这一点！

我只是不相信当前的实现完全实现了这种潜力，这让我很难过。