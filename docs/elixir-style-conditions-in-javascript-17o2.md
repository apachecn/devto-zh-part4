# Javascript 中的 Elixir 样式条件

> 原文：<https://dev.to/petecorey/elixir-style-conditions-in-javascript-17o2>

Elixir 有一个称为 [`cond`](https://elixir-lang.org/getting-started/case-cond-and-if.html#cond) 的有用的控制流结构，可以让您在任意条件下进行分支。与更常见的 [`switch`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/switch) 控制结构不同(仙丹中的 [`case`)，`cond`与预定值不匹配。相反，它依次评估每个条件，寻找第一个评估为真值的条件(不是`nil`或`false`)。](https://elixir-lang.org/getting-started/case-cond-and-if.html#case) 

```
 numbers = [1, 2, 3]
result = cond do
  4 in numbers ->
    :four
  6 == Enum.sum(numbers) ->
    :sum
  true ->
    :default
end 
```

这可能对你来说都是老生常谈了。

正如我提到的，`cond`可能是一个非常有用的控制结构，当我在像 Javascript 这样只有`switch`表达式的语言中工作时，有时会错过它。

上述内容的传统 Javascript 实现(在 [Lodash](https://lodash.com/) 的一点帮助下)应该是这样的:

```
 let numbers = [1, 2, 3];
if (_.includes(numbers, 4)) {
  var result = "four";
} else if (6 === _.sum(numbers)) {
  var result = "sum";
} else {
  var result = "default";
} 
```

然而，[我最近偶然发现了一个技巧](https://twitter.com/swyx/status/1163225169676132353)，它让你在 Javascript 中实现一个`switch`语句，其行为与 Elixir 中的`cond`表达式非常相似。关键是`switch`对`true`的值。计算结果为`true`的 case 表达式将匹配，并且它们对应的语句将按顺序计算。

```
 let numbers = [1, 2, 3];
switch (true) {
  case _.includes(numbers, 4):
    var result = "four";
    break;
  case 6 === _.sum(numbers):
    var result = "sum";
    break;
  default:
    var result = "default";
    break;
} 
```

这是否比一系列的`if` / `else`块更有用或更易读是有争议的。也就是说，这绝对是一个有趣的视角转换的例子，用新的眼光看待旧的代码。希望你能和我一样感兴趣。