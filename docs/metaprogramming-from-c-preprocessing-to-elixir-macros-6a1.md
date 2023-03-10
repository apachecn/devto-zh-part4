# 元编程:从 C 预处理到 Elixir 宏

> 原文：<https://dev.to/appsignal/metaprogramming-from-c-preprocessing-to-elixir-macros-6a1>

开发人员对元编程又爱又恨。一方面，它是构建可重用代码的强大工具。另一方面，它可能很快变得难以理解和维护。

我喜欢把它想象成盐。它在很多场合都很方便，但是用多了一点，你就剩下一道不好吃的菜了。

此外，大剂量服用其中任何一种都会导致血压升高。😅

然而，元编程自早期以来已经走过了漫长的道路。虽然我仍然尽量不过度使用它，但它已经变得更加有用和易于使用。我们来看看它是怎么进化的。

# C/C++

如果我们回到几十年前，当编程语言更接近金属的时候，C/C++预处理器是我们必须做一些接近元编程的事情的唯一选择之一。

顾名思义，这个预处理器是一个解析器，它将运行 C 代码，处理特定的定义(关键字，如`#define`和`#if`)，并将 C 代码的最终版本输出给编译器。这个最终版本可能会根据某些标准发生变化。它看起来会像这样:

```
#define FOO 1 
#if FOO == 1
#define MSG "Hello, World"
#else
#define MSG "Goodbye, World"
#endif 
#include <stdio.h> 
int main() {
  printf(MSG);
end 
```

这个程序会一直打印`"Hello, World"`。正如您可能猜到的，将 FOO 定义更改为 0，并重新编译程序，会导致它打印出`"Goodbye, World"`。

这些预处理器指令通常用于创建针对特定平台或架构的代码。例如，您可以为您的程序设置不同的行为，当编译目标是 Windows 系统和 Linux 系统时。两个结果二进制文件将只有与特定平台相关的代码，因此不需要对这些条件执行运行时检查。这些在存储和运行时性能方面的节省通常是显著的。

然而，如果你有任何 C 语言的经验，你就会知道它只是以香草的形式存在是多么的危险。现在，在此基础上添加许多预处理行为，很快就会变得很难管理。因此，在大多数情况下，除了小配置之外，不建议使用它。

# 红宝石

随着更好的技术和更高级别的脚本语言的出现，创造更精细的编程风格也成为可能。特别是在 Ruby 中，元编程被证明是一个强大而可怕的特性。

这在 Ruby 中的工作方式是基于这样的想法，即代码只不过是一串文本，由 Ruby 环境解释和执行。

因为 Ruby 是在运行时解释的，所以不需要预先编译整个代码库。Ruby 允许动态定义类的实例方法。

此外，由于 Ruby 类和实例的内部构造方式，您甚至可以为单个实例而不是整个类定义方法！

PS:进一步阅读 Ruby 类[这里](https://www.devalot.com/articles/2008/09/ruby-singleton)。

```
class Foo
  def hello1
    puts "Hello from a regular method"
  end

  [:hello2, :hello3].each do |f|
    define_method f do
      puts "Hello from a dynamically-defined #{f} method"
    end
  end
end

foo = Foo.new

foo.define_singleton_method(:hello4) { puts "Hello only from this instance of Foo" }

foo.hello1
foo.hello2
foo.hello3
foo.hello4 
```

Ruby 在编辑现有代码时也相当宽松，即使是来自标准库的代码。这是有效的拼音:

```
array = [1, 2, 3]

# will print out 3
puts array.size

class Array
  def size
    "Hello"
  end
end

# will now print out "Hello"
puts array.size 
```

不过，不要这样！这很可能会破坏你的程序，总体来说是一个不好的做法。

最后但同样重要的是，Ruby 有一些处理意外函数调用的强大方法，比如`method_missing`回调:

```
array = [1, 2, 3]

class Array
  def method_missing(method, *args)
    puts "#{method} method not found"

    if method == :sise then
      puts "Did you intend to type size instead?"
    end
  end
end

puts array.sise 
```

总的来说，当我第一次了解这些能力时，它们对我来说是一个巨大的游戏改变者。它使我能够以一种全新的不同方式思考我的代码库，并在这个过程中对其进行改进。

不过，还是有些问题。你知道他们怎么说的:权力越大，责任越大。

一些 Ruby 库使用并滥用了这些元编程机制来创建他们自己的领域特定语言。从长远来看，这种过度使用会导致我们在 C++时代遇到的类似问题:维护和理解代码库的困难。

在我看来，长生不老药在这方面又朝正确的方向前进了一步…

# 仙丹❤️

在这里，元编程以一种更强大的方式构建在语言的核心中。Ruby 允许您动态定义方法，或者事件生成一个字符串并将其作为代码进行评估(我们都讨厌的旧的`eval`方法)，而 Elixir 允许您处理抽象语法树(AST)本身。

这是通过`quote`关键字:
完成的

```
iex> expr = quote do
  "Hello, " <> "World"
end 
```

尝试上面的代码，您会发现 string concat 操作不会直接执行。最终得到的不是一个最终的字符串，而是一个描述代码的 AST 表达式:

```
{:<>, [context: Elixir, import: Kernel], ["Hello, ", "World"]} 
```

熟悉[波兰语符号](https://en.wikipedia.org/wiki/Polish_notation)的人可能会很快发现这相当于上面的字符串连接代码。因此，通过引用一些代码，您可以获得该代码的 AST 描述，然后您可以在代码库的其余部分使用它。

然后，您可以开始推理您的代码，就好像它是一个数据结构(它是…一个 AST)，并执行操作来转换它:

让我们稍微修改一下:

```
iex> expr = quote do
  "Hello, " <> name
end 
```

现在我们的表达式使用一个动态名称来代替。然而，这个名字从何而来？
我们没有在任何地方定义这个变量，但是它在语法上仍然是正确的:

```
{:<>, [context: Elixir, import: Kernel], ["Hello, ", {:name, [], Elixir}]} 
```

但是，它将无法执行，我们可以使用`Code.eval_quoted/3` :
进行测试

```
iex> Code.eval_quoted(expr)
** (CompileError) nofile:1: undefined function name/0
    (elixir) lib/code.ex:590: Code.eval_quoted/3
    test.ex:5: (file) 
```

现在让我们创建第二个 AST 定义:

```
definition = quote do
  name = "Miguel"
end 
```

第二个表达式定义定义了一个名为`name`的变量。但是，请记住，我们没有定义任何值，只是为该操作创建了 AST。

我们可以将这两个表达式合并成一个:

```
final_code = quote do
  unquote(definition)
  unquote(expr)
end 
```

结果就像我们输入了:
一样

```
name = "Miguel"
"Hello, " <> name 
```

但是，请注意，在这样做的时候，我们从来没有放弃过 Elixir 语法和规则。我们在写长生不老药，写长生不老药！

这在长生不老药的核心中被大量使用。每当您定义一个函数或一个简单的 if 语句时，您都在执行宏，这些宏根据您的代码来改变代码的 AST。说到这个…

## 仙丹氏宏

Elixir 的许多特性都是用宏编写的。您使用的许多常见运算符都可以用宏重写。让我们以`unless`操作符(它已经存在于语言的核心中)为例，自己定义它:

```
defmodule Foo do
  defmacro custom_unless(condition, do: do_clause, else: else_clause) do quote do
      if !unquote(condition) do
        unquote(do_clause)
      else
        unquote(else_clause)
      end
    end
  end

  defmacro custom_unless(condition, do: do_clause) do
    quote do
      Foo.custom_unless(unquote(condition), do: unquote(do_clause), else: nil)
    end
  end
end

defmodule Bar do
  require Foo

  Foo.custom_unless true, do: IO.puts("not true"), else: IO.puts("true")
end 
```

我们的`custom_unless`宏接受一个布尔值。在内部，我们检查条件的相反情况(我们运行 AST 在该条件下给出的任何代码，并反转得到的布尔值)。然后，我们根据结果执行为`do`或`else`子句给出的 AST。

然而，关于 Elixir 有趣的部分是，因为即使是基本的构造，如`if`子句，通常也是使用宏本身构建的，所以我们可以更好地在语言中嵌入宏。换句话说，在定义了我们的宏之后，这也是工作的灵丹妙药代码:

```
defmodule Bar
  # importing instead of requiring allows us to call the macro directly,
  # without the Foo. prefix
  import Foo

  custom_unless true do
    IO.puts("not true")
  else
    IO.puts("true")
  end
end 
```

这是可行的，因为对 Elixir 中多行 if/else 块的解释只不过是对:
的语法修饰

```
if condition do: something, else: something_else 
```

# 结论

希望这是一个有用的演练，展示了宏在过去是如何发展的，特别是对于那些可能不了解他们语言的全部功能以及历史的 Elixir 开发人员。

如果你想获得常规剂量的⚗️Elixir 炼金术，请[订阅](https://appsignal.com/elixir-alchemy)让下一集炼金术直接发送到你的收件箱。

*本文由客座作者[米盖尔·帕哈斯](http://naps62.github.io/)撰写。米盖尔是专业的总工程师[@子视觉](https://twitter.com/subvisual)，组织 [@rubyconfpt](https://twitter.com/rubyconfpt) 和 [@MirrorConf](https://twitter.com/mirrorconf) 。*