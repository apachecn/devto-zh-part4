# 将“while”循环转换为 ReScript:使用递归

> 原文：<https://dev.to/johnridesabike/converting-while-loops-to-reason-use-recursion-3749>

*本文原以 ReasonML 和 ReasonReact 撰写。我在 2021 年 5 月更新了它，使用 ReScript。*

我最近重写了我的国际象棋锦标赛应用程序，从 React 改为~~React~~ReScript-React。一开始让我感到困惑的一个领域是命令式的`while`循环。有时，您只需要无限重复一段代码，直到达到某个条件。尽管命令式循环是我们大多数人的首选工具，但事实证明它确实很棒(甚至更好？)功能替代。

如果你熟悉 ReScript，你可能会从他们的文档中认出这个页面: *[Loops](https://rescript-lang.org/docs/manual/latest/control-flow)* 。总之，ReScript 支持这些循环，但它们并不美观。为了节省你的点击，这里是他们的代码样本:

```
Random.self_init()

let break = ref(false)

while !break.contents {
  if Random.int(10) === 3 {
    break := true
  } else {
    print_endline("hello")
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

如你所见，ReScript 不支持`break`语句，谢天谢地！能够在任何点任意地跳出任何循环，会大大增加代码的复杂性。然而，我们可以用一个可变的`ref`值笨拙地模拟它们。(值是不可变的，但是`ref`和它的`:=`语法只是用来访问可变的`content`字段的糖。)

## 真实世界的例子。

我需要为我的应用程序解决一个问题:在一场国际象棋比赛中，你需要按第一、第二、第三等对选手进行排名(咄)，但这出奇地困难。一场典型的锦标赛最终会有很多平手的分数，所以仅有分数是不够的。USCF 和 FIDE 想出了几种打破僵局的方法来解决这个问题。例如，您可以使用某人的累积(跑步)分数来打破平局，或者您可以使用谁作为黑人出场次数更多。方法有很多，有些很复杂。一种方法通常是不够的，因此锦标赛将使用几种方法，每种方法都有相对于其他方法的优先级。对于使用哪一种，或者使用的顺序，并没有规定的标准，所以赛事总监可以自由选择他们想要的。

无论你的锦标赛有丰厚的现金奖励，还是你只是在当地图书馆玩玩，你都希望确保你的抢七方法对参与者来说是清楚的，并且是经过精确计算的。

## 我的解决方案，`while`版本

每个玩家的分数信息在这种类型的记录中定义:

```
type scores = {
  id: string,
  score: float,
  tieBreaks: list<(tieBreak, float)>,
} 
```

Enter fullscreen mode Exit fullscreen mode

`tieBreaks`字段是一个关联列表，它可以像`Map`对象一样使用，除了像这样的情况更简单。它将每个平局决胜方法映射到结果。(在这段代码中，结果已经在前面的函数中计算过了。)

列表中使用的`tieBreak`类型定义如下:

```
type tieBreak =
  | Median
  | Solkoff
  | Cumulative
  | CumulativeOfOpposition
  | MostBlack 
```

Enter fullscreen mode Exit fullscreen mode

(国际象棋爱好者可能会注意到，我还没有实现所有的方法。)

如果我有一个玩家分数列表，我需要对它们进行排序，以找出谁获得了第一名。为了对它们进行排序，我需要一个比较函数。该函数必须检查`tieBreaks`字段中的每一项，它可以是任何大小，直到它找到一个比较或用完所有的项。下面是我一开始用的:

```
let standingsSorter = (tieBreaks: array<tieBreak>, a: scores, b: scores) => {
  let result = ref(0)
  let tieBreakIndex = ref(0)
  let break = ref(false)
  while result.contents === 0 && !break.contents {
    switch tieBreaks->Belt.Array.get(tieBreakIndex.contents) {
    | None => break := true
    | Some(tieBreak) =>
      let getTieBreak = Belt.List.getAssoc(_, tieBreak, \"==")
      /* a and b are switched for ascending order */
      switch compare(b.score, a.score) {
      | 0 =>
        switch (getTieBreak(b.tieBreaks), getTieBreak(a.tieBreaks)) {
        | (Some(tb_b), Some(tb_a)) =>
          /* a and b are switched for ascending order */
          switch compare(tb_b, tb_a) {
          | 0 => tieBreakIndex := tieBreakIndex.contents + 1
          | x => result := x
          }
        | (None, _)
        | (_, None) => ()
        }
      | x => result := x
      }
    }
  }
  result.contents
} 
```

Enter fullscreen mode Exit fullscreen mode

作为第一个参数传递的`tieBreaks`数组告诉比较器函数要检查哪个方法以及检查的顺序。比如:

```
standingsSorter([Median, Cumulative, MostBlack]); 
```

Enter fullscreen mode Exit fullscreen mode

将返回一个函数，该函数首先比较中间分数，然后是累积分数，如果其他分数相等，最后是最黑分数。

我会用以下代码对它们进行排序:

```
listOfScores->Belt.List.sort(standingsSorter(methods)) 
```

Enter fullscreen mode Exit fullscreen mode

这是可行的，但还能更好吗？它容易阅读吗？重构容易吗？我以为我可以改进它。

## 我的解决方案，递归版！

完全不需要任何强制循环就可以获得完全相同的结果。要理解这一点，可以将每个代码块想象成一个返回值的函数。因此，我们不需要让我们的`while`块把它的返回值放在一个可变的`ref`变量中，我们需要让它像普通函数一样返回那个值，而没有副作用。但是，您可能会注意到，`while`循环将无限期地继续下去，直到达到某个条件，并且函数通常会执行一定的次数。这里的关键是使用**递归**。

默认情况下，重写函数不是递归的。首先，我们必须将它们标记为`rec`，这使我们能够执行其内部的函数。与`while`循环相比，逻辑是相反的:`while`循环将重复，直到`break`或条件告诉它们停止；递归函数将返回它们的值，除非有条件告诉它们再次执行该函数。

这是我的代码的第二个版本。注意在主函数
中定义的`tieBreaksCompare`函数

```
let standingsSorter = (tieBreaks: list<tieBreak>, a: scores, b: scores) => {
  let rec tieBreaksCompare = tieBreaks =>
    switch tieBreaks {
    | list{} => 0
    | list{tieBreak, ...rest} =>
      let getTieBreak = Belt.List.getAssoc(_, tieBreak, \"==")
      switch (getTieBreak(a.tieBreaks), getTieBreak(b.tieBreaks)) {
      | (None, _)
      | (_, None) =>
        tieBreaksCompare(rest)
      | (Some(tb_a), Some(tb_b)) =>
        /* a and b are switched for ascending order */
        switch compare(tb_b, tb_a) {
        | 0 => tieBreaksCompare(rest)
        | x => x
        }
      }
    }
  /* a and b are switched for ascending order */
  switch compare(b.score, a.score) {
  | 0 => tieBreaksCompare(tieBreaks)
  | x => x
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

正如你所看到的，很多逻辑并没有改变。我们还在比较同样的东西，最后的结果是一样的。但是没有更多的`ref` s！逻辑，至少一行一行地，已经被简化了，因为需要处理的活动部分更少了。

您还会注意到，输入 tiebreak 数组已经更改为一个列表，这对于模式匹配来说更好。我们不必跟踪当前使用的索引，查找索引的值，并为下一个循环递增索引，而是在每一步简单地析构列表。

这就是人们谈论 ReScript 的“表现力”时所指的。您不需要像`while(result^ === 0 && ! break^)`那样将条件链接在一起，或者进行大量的数组索引查找，因为这种逻辑已经内置在语言中了。虫子的表面积大大减少了！

如果您对递归函数风格取代命令式循环风格的效果持怀疑态度，下面是我们编译了脚本函数之后的 JavaScript 输出:

```
function standingsSorter(tieBreaks, a, b) {
  var x = Caml.caml_float_compare(b.score, a.score);
  if (x !== 0) {
    return x;
  } else {
    var _tieBreaks = tieBreaks;
    while(true) {
      var tieBreaks$1 = _tieBreaks;
      if (!tieBreaks$1) {
        return 0;
      }
      var rest = tieBreaks$1.tl;
      var tieBreak = tieBreaks$1.hd;
      var getTieBreak = (function(tieBreak){
      return function getTieBreak(__x) {
        return Belt_List.getAssoc(__x, tieBreak, (function (prim0, prim1) {
                      return prim0 === prim1;
                    }));
      }
      }(tieBreak));
      var match = getTieBreak(a.tieBreaks);
      var match$1 = getTieBreak(b.tieBreaks);
      if (match !== undefined) {
        if (match$1 !== undefined) {
          var x$1 = Caml.caml_float_compare(match$1, match);
          if (x$1 !== 0) {
            return x$1;
          }
          _tieBreaks = rest;
          continue ;
        }
        _tieBreaks = rest;
        continue ;
      }
      _tieBreaks = rest;
      continue ;
    };
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

注意到第 7 行的内容了吗？一个`while`循环！就计算机而言，递归函数和`while`完全一样。事实上，我们的`tieBreaksCompare`函数在输出中根本不存在。它被转化成了循环体。

## 最后的想法

我可能偏向于函数式、声明式编程，但是我并不是说我们不应该使用命令式循环。正如 ReScript docs 所说，一些算法在一种风格下工作得更好，一些在另一种风格下工作得更好。

也就是说，我知道有很多人以前只使用过命令式代码，他们甚至不知道如何有效地编写声明式循环。(这是几个月前刚刚的我！)充分理解每一种选择可以让你写出好得多的代码。

如果你有兴趣查看我的应用程序的其余代码，看看我到底是如何实现这个逻辑的，[你可以在 GitHub 这里查看](https://github.com/johnridesabike/coronate)。[这里有一个本文引用的函数的链接](https://github.com/johnridesabike/coronate/blob/00d245c12039177d4f406ca78c707d4c40c50f78/src/Data/Data_Scoring.res#L256)。