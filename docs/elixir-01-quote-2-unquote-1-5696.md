# Elixir 元编程 01 :内部表示 quote/2 和 unquote/1

> 原文：<https://dev.to/gumi/elixir-01-quote-2-unquote-1-5696>

本文在 Elixir 官网的许可下，根据“[Quote and unquote](https://elixir-lang.org/getting-started/meta/quote-and-unquote.html) ”的解说，加以修改，对 Elixir 中可以使用的元编程技术中内部表现的处理进行说明。 用自己的数据结构表示 Elixir 程序的功能是元编程的基础。 其数据结构称为内部表示。

# quote/2 对公式的表达

在 Elixir 程序中，表达式由三个元素的元组组成。 宏[`quote/2`](https://hexdocs.pm/elixir/Kernel.SpecialForms.html#quote/2)用元组返回表达式。 例如，对函数 sum ( 1，2，3 )的调用由以下元组表示:

```
iex> quote do: sum(1, 2, 3)
{:sum, [], [1, 2, 3]} 
```

Enter fullscreen mode Exit fullscreen mode

元组的第一个元素是函数名称，第二个元素是包含元数据的列表，第三个元素是参数的列表。

不仅是函数，运算符也可以用三要素的元组表示。

```
iex> quote do: 1 + 2
{:+, [context: Elixir, import: Kernel], [1, 2]} 
```

Enter fullscreen mode Exit fullscreen mode

贴图也是三个元素的元组。

```
iex> quote do: %{1 => 2}
{:%{}, [], [{1, 2}]} 
```

Enter fullscreen mode Exit fullscreen mode

此外，变量的声明也用元组表示。 但是，第 3 要素是阿童木。

```
iex> quote do: x
{:x, [], Elixir} 
```

Enter fullscreen mode Exit fullscreen mode

更复杂的表达式也可以用三要素元组的组合来表示。 那时，元组往往会成为嵌套的树结构。 例如，将值代入变量时。

```
iex> quote do: x = 1
{:=, [], [{:x, [], Elixir}, 1]} 
```

Enter fullscreen mode Exit fullscreen mode

在很多语言中，这样的表达被称为[抽象语法树( AST )](https://ja.wikipedia.org/wiki/%E6%8A%BD%E8%B1%A1%E6%A7%8B%E6%96%87%E6%9C%A8)。 在 Elixir 术语中是内部表示。

```
iex> quoted = quote do: sum(1, 2 + 3, 4)
{:sum, [], [1, {:+, [context: Elixir, import: Kernel], [2, 3]}, 4]} 
```

Enter fullscreen mode Exit fullscreen mode

有时您可能想将内部表达转换为原始代码的字符串表达。 此时请使用[`Macro.to_string/2`](https://hexdocs.pm/elixir/Macro.html#to_string/2)(第 2 参数为函数，默认值为`fn _ast, string -> string end`)。

```
iex> Macro.to_string(quoted)
"sum(1, 2 + 3, 4)" 
```

Enter fullscreen mode Exit fullscreen mode

从`quote/2`返回的元组的三个元素的格式如下:

```
{atom | tuple, list, list | atom} 
```

Enter fullscreen mode Exit fullscreen mode

*   第 1 要素:阿童木或 3 要素元组
*   第二元素:包含元数据的关键字列表
    *   例如，数值或上下文
*   第三元素:被调用函数的参数列表或原子
    *   阿童木的情况下元组意味着变量

以下五个 Elixir 文字在传递给`quote/2`的参数时，将返回其值而不是元组。

| 文字 | example |
| --- | --- |
| 阿童木 | :总和 |
| Counting | One |
| 列表 | [1, 2] |
| Text column | "字符串" |
| 双元素元组 | {键，值} |

大多数 Elixir 代码都可以表示为内部表示。 例如，表达式的树结构组合。

```
iex> quote do: String.upcase("foo") 
{{:., [], [{:__aliases__, [alias: false], [:String]}, :upcase]}, [], ["foo"]} 
```

Enter fullscreen mode Exit fullscreen mode

`if/2`宏的语法可以不用`do`/ `end`块，用一个公式表示。 在这种情况下，内部表示如下。

```
iex> quote do: if(true, do: :this, else: :that)
{:if, [context: Elixir, import: Kernel], [true, [do: :this, else: :that]]} 
```

Enter fullscreen mode Exit fullscreen mode

使用`do`/ `end`模块时，内部表现也不会改变。

```
iex> quote do
...>   if true do
...>     :this
...>   else
...>     :that
...>   end
...> end
{:if, [context: Elixir, import: Kernel], [true, [do: :this, else: :that]]} 
```

Enter fullscreen mode Exit fullscreen mode

# 基于 unquote/1 的公式的值的取出

`quote/2`返回的内部表达表示一组代码。 例如，使用包含值的变量时，内部表示中显示的是标识符。

```
iex> number = 13
13
iex> Macro.to_string(quote do: 11 + number)
"11 + number" 
```

Enter fullscreen mode Exit fullscreen mode

但是，根据情况也有想确认变量值的情况吧。 取出数值并插入公式中时使用的是[`unquote/1`](https://elixir-lang.org/getting-started/meta/quote-and-unquote.html)。 另外，该宏必须与`quote/2`一起使用。

```
iex> Macro.to_string(quote do: 11 + unquote(number))
"11 + 13" 
```

Enter fullscreen mode Exit fullscreen mode

将变量传递给函数并调用时，内部表示中会用嵌套元组表示变量。

```
iex> quote do
...>   sum(1, number, 3)
...> end
{:sum, [], [1, {:number, [], Elixir}, 3]} 
```

Enter fullscreen mode Exit fullscreen mode

这种情况下，如果将变量传递到`unquote/1`，也可以取出变量值并插入到内部表现中。

```
iex> quote do
...>   sum(1, unquote(number), 3)
...> end
{:sum, [], [1, 13, 3]} 
```

Enter fullscreen mode Exit fullscreen mode

另外，可以表示将原子传递给`unquote/1`并命名的函数。

```
iex> func = quote do: unquote(:hello)(:world)
{:hello, [], [:world]}
iex> Macro.to_string(func)
"hello(:world)" 
```

Enter fullscreen mode Exit fullscreen mode

您可能想要在列表中添加其他列表的值以作为内部表示。 在这种情况下，用`unquote/1`插入的列表表示为嵌套。

```
iex> inner = [3, 4, 5]
[3, 4, 5]
iex> Macro.to_string(quote do: [1, 2, unquote(inner), 6])
"[1, 2, [3, 4, 5], 6]" 
```

Enter fullscreen mode Exit fullscreen mode

想从列表中取出要素添加到内部表达中时使用的是[`unquote_splicing/1`](https://hexdocs.pm/elixir/Kernel.SpecialForms.html#unquote_splicing/1)。

```
iex> Macro.to_string(quote do: [1, 2, unquote_splicing(inner), 6])
"[1, 2, 3, 4, 5, 6]" 
```

Enter fullscreen mode Exit fullscreen mode

此宏不仅可用于添加列表元素作为列表，还可用于添加列表元素作为函数的参数。

```
iex> Macro.to_string(quote do: sum(1, 2, unquote_splicing(inner), 6))
"sum(1, 2, 3, 4, 5, 6)" 
```

Enter fullscreen mode Exit fullscreen mode

`unquote/1`和`unquote_splicing/1`在处理宏时非常有用。 因为写宏的开发者接收到一组代码，然后又可以插入其他代码。 用于转换和编写代码，并在编译时生成代码。

# 通过 Macro.escape/2 进行转义

内部表示法以三要素元组为基础。 例如，贴图不能直接用作内部表示。 4 要素以上的元组也是一样的。 这些值必须转换为内部表达。

```
iex> quote do: %{1 => 2}
{:%{}, [], [{1, 2}]} 
```

Enter fullscreen mode Exit fullscreen mode

使用[`Macro.escape/2`](https://hexdocs.pm/elixir/Macro.html#escape/2)也可以得到内部表现。

```
iex(23)> Macro.escape(%{1 => 2})
{:%{}, [], [{1, 2}]} 
```

Enter fullscreen mode Exit fullscreen mode

另外，如果使用`Macro.escape/2`，从变量中取出的值可以成为内部表现。

```
iex> map = %{hello: :world}
%{hello: :world}
iex> quote do: map
{:map, [], Elixir}
iex> quote do: unquote(map)
%{hello: :world}
iex> Macro.escape(map)
{:%{}, [], [hello: :world]} 
```

Enter fullscreen mode Exit fullscreen mode

宏必须接收内部表示，并返回内部表示。 但是，有时在运行宏时必须处理值。 此时，请仔细区分值和内部表达。 将 Elixir 的标准值(列表、地图、流程、引用等)与内部表示分开处理非常重要。

整数、原子和字符串都有表示该值的内部表示。 除此之外，例如，地图必须转换为内部表示。 或者，函数或引用不能将值作为内部表示。 关于`quote/2`和`unquote/1`等，详情请参阅“[Kernel.SpecialForms](https://hexdocs.pm/elixir/Kernel.SpecialForms.html) ”，另外，与`Macro.escape/1`相关的函数请参阅“[Macro](https://hexdocs.pm/elixir/Macro.html) ”模块。