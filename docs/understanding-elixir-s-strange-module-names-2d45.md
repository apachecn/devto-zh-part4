# 了解 Elixir 奇怪的模块名称

> 原文：<https://dev.to/honeybadger/understanding-elixir-s-strange-module-names-2d45>

上周太棒了。这是我们第一次蜜獾黑客周。我们不得不暂时停止考虑异常和正常运行时间，将所有精力集中在全新的事物上。我们作为一个团队决定在 Elixir 和 Phoenix 中创建一个小应用程序。

当我对长生不老药越来越适应的时候，一件奇怪的事情开始引起我的注意。当你打开控制台(iex)并输入一个模块名时，从来没有错误。即使模块不存在。

```
iex> SomeModule
# => SomeModule 
```

这与 Ruby 不同，在 Ruby 中您总是会得到一个错误:

```
irb> SomeClass
NameError: uninitialized constant SomeClass 
```

事实证明，Elixir 以一种意想不到的方式处理模块名。这有点奇怪，但是一旦理解了底层机制，对模块进行推理就变得非常容易了。

所以找个座位坐下。围在火边，让我给你们讲个故事。

## 其他语言如何处理模块

在像 Rust 这样的静态语言中，模块只在编译时存在。它们被用来执行正确性检查，但是随后它们被丢弃，并且不存在于编译后的目标文件中，除了作为调试元数据。

```
mod sound {
    fn guitar() {
    }
}

sound::guitar(); 
```

在 Ruby 中，模块和类是特殊类型的对象。模块“名称”只是变量。它们有特殊的名称和一些特殊的查找规则，但它们基本上只是变量。您甚至可以重新分配它们。

```
module MyModule
  def self.hello()
    puts "world"
  end
end

YourModule = MyModule
MyModule = nil
YourModule.hello
# => "world" 
```

## 仙丹奇招

乍一看，仙丹看起来像红宝石。我们可以定义模块，将它们赋给变量，然后调用函数，就像引用原始函数一样。

```
defmodule MyModule do
  def hello() do
    IO.puts("world")
  end
end

x = MyModule
x.hello()
# => "world" 
```

但是*和*是不同的。

如果你*真的*注意，你可能已经注意到在 ruby 的例子中，我们有`YourModule = MyModule`，而在 elixir 中，我们有`x = MyModule`。这是因为如果我尝试前者，elixir 会给出一个错误:

```
YourModule = MyModule
# ** (MatchError) no match of right hand side value: MyModule 
```

我可以将一个模块名赋给一个变量，但是模块名本身似乎并不像在 Ruby 中那样是一个变量。相反，模块名是“原子”

## 原子

当你不想使用字符串时，原子被用来命名事物。它们与 Ruby 的“符号”非常相似:

```
:foo
:bar
:"this atom has spaces and.special.chars" 
```

原子的常见用途是作为映射中的键(Ruby 中的散列，JavaScript 中的对象)以及在函数中指定关键字参数:

```
my_map = %{foo: 1}
my_func(foo: 1) 
```

它们也被用来命名模块。例如，如果我想从 elixir 调用 Erlang 的`format`函数，我会编写如下代码:

```
:io.format(arg1, arg2) 
```

像所有的 Erlang 模块一样，我们通过原子`:io`来引用`io`模块。

## 别名

在这一点上，我想你一定在想，“等一下！药剂模块名称看起来像`MyModule`而不是`:my_module`！那些不一样！”

那是因为长生不老药在捉弄我们。在后台，它将引用`MyModule`的任何代码转换为引用原子`:"Elixir.MyModule"`。如果您打开 iex，就可以看到这一点:

```
MyModule == :"Elixir.MyModule"
# true 
```

用原子替换模块名(如`MyModule`)称为“别名”对于任何看起来像模块名的关键字:`AnythingThatLooksLikeThis`，它都会自动发生。

我们用 atom ( `:io`等)直接引用 Erlang 模块的原因是因为不涉及别名。但是，如果您愿意，您可以手动创建别名:

```
alias :io, as: IO

IO
# => :io 
```

## 实际含义

既然我们知道 Elixir 中的“模块名”只是原子+别名，那么就很容易对模块进行推理了。

首先，我们看看为什么当我们试图赋值`YourModule = MyModule`时会出错。展开后，该代码变成:

```
:"Elixir.YourModule" = :"Elixir.MyModule"
# error 
```

将一个原子分配给另一个原子毫无意义。你不妨试着说`1 = 2`或者`"foo" = "bar"`。

此外，我们看到，可以用原子完成的任何事情也可以用模块名来完成:

```
# Call functions using the atom directly

:"Elixir.MyModule".hello()

#   从字符串中动态构造模块名

String.to_atom("仙丹。MyModule”)。你好()

#   将模块传递给匿名函数

(fn x -> x.hello()结束)。(MyModule)

#   使一个 genserver 名称等于当前模块名称

GenServer.start_link(堆栈，[:hello]，名称:**模块**

#   在模块列表中执行相同的功能

对于 mod 

#   在定义模块之前引用模块。如果不是就不要运行这个函数:)

def do_something()，do:someothermodule . something() 
```

## 
 [T3】
结论](#conclusion) 

当我第一次发现将原子用于长生不老药模块时，我并不激动。它看起来丑陋而怪异。但我已经改变主意了。

原子非常适合命名事物，包括模块。他们很快。它们从来没有被收过垃圾。它们很独特。最棒的是，它们很容易推理。像很多 Erlang 一样，一旦你克服了最初的陌生感，它们就很有意义。