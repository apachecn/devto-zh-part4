# 爱情魔药` number == 9` |灵丹妙药如何彻底改变我与测试的关系

> 原文：<https://dev.to/thepeoplesbourgeois/love-potion-assert-number-9-how-elixir-completely-changed-my-relationship-with-testing-5h8p>

回到 2012 年，我面试了一家强调 TDD 是他们主要特性开发策略的公司。那时，我从事专业软件开发还不到一年，对 JavaScript 和人们在他们打开开发工具时只能用它做事情的能力感到恐惧，总共写了五个测试断言——如果是这样的话——这要感谢我作为计算机科学辅修课的一部分的最后一个实验模块，当然,*完全没有说*知道 TDD 是什么。但是我决定我不会提起我有多紧张，因为我们甚至在知道代码的形状之前就在编写测试用例。我只是想通过面试，尽我最大的努力去了解这些模式。

我坐飞机回家的第二天，电话来了:我得到了那份工作。我非常感激能够“假装直到你成功”给了我机会向我职业生涯中共事过的一些最积极的开发人员、最勤奋的测试人员、最敬业的支持代表和最注重细节的设计师学习。

我在 Pivotal Tracker 上的工作教会了我很多重要的经验，关于代码和人生，但我遗憾的是只让一个人半途而废:如何进行测试驱动的开发。这是一项需要不断练习和维护的技能，对于我们这一行的人来说，很容易忘记牢记目标的重要性，而倾向于立即行动，尽我们所能去建设。

一旦我发现了长生不老药，情况就完全变了。前几天，我用语言实现了[对日常编码挑战](https://dev.to/thepeoplesbourgeois/comment/eai6)的回答。我会把我为那个挑战写的代码复制到这里，这样就能把这篇文章的重点都放在*在*这篇文章:

```
defmodule W do
  @doc ~S"""
    Takes in a string and returns a list of variations of that string, each containing
  one capitalized letter from the string, in sequence, starting from the left.

  ## Examples

      iex>W.ave("hello.")
      ["Hello.", "hEllo.", "heLlo.", "helLo.", "hellO.", "hello."]

      iex>W.ave("Hello World!")
      ["Hello world!", "hEllo world!", "heLlo world!", "helLo world!", "hellO world!",
       "hello World!", "hello wOrld!", "hello woRld!", "hello worLd!", "hello worlD!"]
  """
  def ave(string) do
    string
      |> String.downcase
      |> String.to_charlist
      |> do_wave # private function
  end

  defp do_wave(charlist, waveform \\ [], waved \\ '') # no `do` block because this signature is used to define defaults
  # ...
end 
```

这段代码获取一个输入字符串，对其进行降频处理，然后将其转换为 Erlang 使用的一种旧格式，称为`charlist`(使用单引号构成)，并处理该列表以生成挑战描述中要求的一系列字符串。现在，让我们看看这个模块的测试代码:

```
# ./test/w_test.exs
defmodule WTest do
  use ExUnit.Case, async: true
  doctest W
end 
```

看起来少多了，对吧？让我们来分析一下这里发生了什么:

*   首先，我用`defmodule`声明测试模块名。`defmodule`是面向对象语言中类声明的同义词，除了因为在 Elixir 中没有对象，你在模块中定义的所有函数和宏都存在于模块本身中，并直接从模块中调用，就像`String.length(string)`；在 Elixir 中，永远不要从任何变量或数据结构中直接调用方法！无论如何，习惯上通常将测试模块命名为“test”，以保持一切简单
*   下一行是`use ExUnit.Case, async: true`，从`ExUnit`的一个模块中获取代码，这是 Elixir 的内置测试框架。`use`是一个关键字(具体来说，它是一种特殊的形式)，告诉编译器`import ExUnit.Case`并加载它的函数和宏，以便它们可以在这个模块中使用，以及运行执行一些额外功能的代码块。我们传递了`async: true`,这样这些测试可以与其他测试文件中的测试并发运行，这加快了测试运行，并允许更大程度的随机执行顺序
*   这个文件的倒数第二行， [`doctest W`](https://elixir-lang.org/getting-started/mix-otp/docs-tests-and-with.html) ，告诉测试运行者…🤔

你猜怎么着我完全超越了自己😐让我们运行这个测试文件。

```
2019-07-07 19:07:07 ⌚ ruby 2.6.3p62 Newtons-Mac in ~/workspace/elixir/joywave
±  |master ✓| → mix test test/w_test.exs 
Compiling 1 file (.ex)
.

  1) doctest W.ave/1 (1) (WTest)
     test/ext/w_test.exs:3
     Doctest failed
     doctest:
       iex>W.ave("Hello.")
       ["Hello.", "hEllo.", "heLlo.", "helLo.", "hellO.", "hello."]
     code:  W.ave("Hello.") === ["Hello.", "hEllo.", "heLlo.", "helLo.", "hellO.", "hello."]
     left:  ["Hello.", "hEllo.", "heLlo.", "helLo.", "hellO."]
     right: ["Hello.", "hEllo.", "heLlo.", "helLo.", "hellO.", "hello."]
     stacktrace:
       lib/joywave/w.ex:8: W (module) 
```

你刚刚见证的魔法，在`W.ave/1`的文档中被称为如何调用它的例子只是作为实际测试运行。Elixir 将文档视为该语言的一等公民，当您在函数文档*中提供的完全相同的用法示例是将作为套件的另一部分运行的*单元规范时，这一点再明显不过了。

这个事实改变了我对单元规格、文档以及测试驱动开发实践的全部想法。像 Minitest 和 RSpec 这样的框架有它们自己的特定领域语言，这需要一些时间来学习和理解它们本身。

是的，这些框架的一些更强大的功能是不可替代的，但是能够向你的库的消费者演示如何使用给定的函数，或者给定的语言范式，同时你也在向自己演示**你的代码做了它应该做的事情，**消除了 ***，所以*** 在为你的代码编写测试时会有很多摩擦。这让我有可能在考虑实现之前考虑我的代码的输出应该是什么。这让我担心我的文档与代码的实际行为不一致。它让我*想要*写文档；这让我想记录所有的 API。

###### 它让我想告诉拼写检查器，它不是世界女王，批评我的可怕的新词“documentate”。

当涉及到你谋生的任何事情时，都有值得投入时间的实践。测试套件对于捕捉回归是必不可少的，TDD 和探索性测试帮助你巩固你的用户体验，强化你的代码对抗边缘情况，在它们分别变成不确定的调用过程和未知行为的灌木丛之前。文档可能看起来很麻烦，但是如果你想要工程师理解如何使用你的框架、库或 API，你**需要**能够告诉他们*如何使用*你的框架、库或 API。

如果您发现自己在测试驱动开发、一般测试、编写文档或理解公司首选测试框架的细微差别方面遇到困难，请查看您的语言是否有用于运行 doctests 的包或模块。如果他们没有，考虑学习[仙丹](https://elixir-lang.org/getting-started/basic-types.html)。它有一些非常有趣的特性，是我一生中见过的最好的参考资料，它由整个行业中并发、容错编程的最佳运行时环境之一提供支持。除此之外，您可能会发现自己正在学习一些很好的模式，以便用所有已知的语言构建可靠的、可读性更好的代码。

现在向前去，大大地开发✨🐶✨