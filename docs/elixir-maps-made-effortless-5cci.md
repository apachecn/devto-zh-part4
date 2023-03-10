# 长生不老药地图变得毫不费力

> 原文：<https://dev.to/logicmason/elixir-maps-made-effortless-5cci>

习惯使用 Elixir maps 可能是真正熟悉这门语言最痛苦的方面之一。如果您来自 Java 或 Ruby 之类的语言，那么一切都是不可变的这一事实可能会令人沮丧。如果您来自 JavaScript，您将会遇到这个问题*和*被原生映射(JS 中的对象)与 JSON 完美匹配所破坏。

好消息是，只有几个地方会让人犯错！

## 变量在仙丹中是不可变的

```
// JavaScript
a = {foo: 42}
b = a
b.foo   // equals 42
b.foo = 5
a   // equals {foo: 5} 
```

```
# Elixir
a = %{foo: 42}
b = a
b.foo   # equals 42
b.foo = 5   # b and b.foo are immutable so we get an error
# ** (CompileError) iex:5: cannot invoke remote function b.foo/0 inside a match

b = %{b| foo: 5}
b   # now b is reassigned and bound to %{foo: 5}
a.foo   # still equals 42 
```

### 所有地图的“更新”都包括将一个变量重新分配给一个新的地图

以下是几种常见的方式:

*   **put** 给地图增加一个新值:`a = Map.put(a, bar: 5)`现在 a 是`%{foo: 42, bar: 5}`
*   **删除**从地图上删除一个值:`a = Map.delete(a, :foo)`现在 a 是`%{bar: 5}`
*   **put_new** 的工作方式类似于 put，但是如果键已经存在，它什么也不做:`a = Map.put_new(a, :bar, 10)`不会替换现有的键，所以 a 仍然是`%{bar: 5}`
*   **合并**添加/更新多个值到一个映射:`a = Map.merge(a, %{foo: "stuff", baz: -5})`现在 a 是`%{foo: "stuff", bar: 5, baz: -5}`

注意，由于 Elixir 变量是不可变的，上面的映射函数创建了新的映射，而不是改变`a`本身。如果不将 a 重新分配给上面每个例子前面都有`a =`的新地图，a 将保持不变。

### 映射键可以是字符串或者原子(或者任何东西！)

在 Elixir 中，您将遇到两种形式的地图键。像上面的例子一样，有些是原子，有些是字符串。很少，你可能会发现整数，浮点数，甚至其他类型，包括作为映射键的嵌套映射！

以下是所有有效的地图:

*   `a = %{:some_atom => :foo}`
*   `i = %{1 => 52}`
*   `f = %{1.5 => i}`-f 的值现在是:`%{1.5 => %{1 => 52}}`
*   `m = %{f => "wat???"}`-m 的值现在是:`%{%{1.5 => %{1 => 52}} => "wat???"}`

**字符串**和 Erlang 二进制是一回事。

*   它们用引号表示。`"foo"`
*   更长的字符串或带引号的字符串可以用符号`~s{I'm a string made from a sigil and can have "quoted" parts}`生成
*   在**键值形式的**中，字符串键使用箭头语法。`%{"foo" => "stuff", "bar" => 5}`
*   当用于**访问**值时，它们使用括号语法。`a["foo"]`是“东西”`b["foo"]`是 5

**原子**是唯一的符号。它们不会被垃圾收集，所以不要动态生成它们。

*   当单独代表**时，它们以冒号开头。`:foo`**
***   在**键-值形式**中，他们可以使用标准的箭头语法*或者*仅仅有一个尾随冒号。`%{foo: "stuff, bar: 5}`与`%{:foo => "stuff", :bar => 5}`相同*   当用于**访问**值时，它们可以使用点语法。`a.foo`与`a[:foo]`相同**

 **### 使用深度嵌套的地图

上述技术足以处理 Elixir 映射，但是不变性使得处理深度嵌套的映射有点麻烦。需要中间步骤来构建您想要给定变量重新赋值的确切结构。

当然，您总是可以自由地编写自己的助手，但是对于 90%的情况，内置的`get_in`、`put_in`和相关函数将完成这项工作。它们是内核的一部分，而不是映射，因为它们也操作其他类型的数据，所以它们没有`Map.`前缀。

```
users = %{
  "sam" => %{age: 22},
  "pat" => %{age: 58}
}

get_in(users, ["sam", :age])
# returns 22

put_in(users["pat"][:age], 28)
# returns %{"sam" => "{age: 22}, "pat" => %{age: 28}} 
```

*通过电子邮件向[炼金术士索取免费的灵药课程。](https://alchemist967856.typeform.com/to/FA0SUF)营地***