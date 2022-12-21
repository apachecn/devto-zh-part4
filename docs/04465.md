# 挥舞着它所有的权力

> 原文：<https://dev.to/savagepixie/wielding-replace-in-all-its-power-1m1m>

[当我们需要分析或转换用户输入或其他字符串时，正则表达式](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Regular_Expressions)会有很大的帮助，我们直到运行时才知道这些字符串的精确内容，尤其是当我们需要处理复杂的表达式时。`String`的方法`.replace()`很万能。我们可以用它来寻找字符串形式的模式或字符串中的正则表达式，并返回一个替换了模式的新模式。

直到这里，这里，这听起来很好，但很平淡。当人们意识到我们可以传递一个函数作为参数来作用于每一个匹配时，激动人心的部分就来了。

## 这是怎么回事？

```
const newStr = str.replace(pattern, replacement) 
```

这是该方法的基本语法。`pattern`将是一个字符串或一个正则表达式。`replacement`将是一个字符串或一个函数。如果它是一个函数，它可以接受几个参数:

*   第一个参数是字符串中的整个匹配
*   `group`接下来，每个[捕获组](https://www.regular-expressions.info/brackets.html)将有一个参数
*   `offset`字符串中匹配的偏移量(即匹配开始的字符串中的索引)
*   `string`整串

## 有什么用？

不久前，我写了一篇文章，解释了 lookaheads 如何帮助我把骆驼案变成烤肉串案。让我们想象一下，这次我想做的正好相反，把烤肉串盒变成骆驼盒。一种方法是这样。

```
const toCamelCase = str => str.split("-")
          .map((x, i) => i == 0 ? x : x.slice(0,1).toUpperCase() + x.slice(1))
          .join('')

toCamelCase("some-stuff-written-here") //returns someStuffWrittenHere 
```

如果我愿意，我可以只使用两个方法和一个回调函数，而不是六个方法和一个回调函数。

```
const toCamelCase = str => {
  const regEx = /-(\w)/g
  return str.replace(regEx, (_, letter) => letter.toUpperCase())
}

toCamelCase("some-stuff-written-here") //returns someStuffWrittenHere 
```

结果是一样的，但是语句要短得多。然而，我认为重要的是要注意，一些不太确定的测试似乎表明，第一个函数对于少量的文本来说稍微快一些。

## 做一些更复杂的事情

现在让我们想象一下，我们有一个包含掷骰子表达式的文本。在我们的网站获得数据之前，我们不知道掷出了多少骰子，或者这些骰子有多少面。但是我们知道这个表达式将会以`xdy`的形式出现，其中`x`表示掷出的骰子的数量，而`y`表示这些骰子有多少面。我们需要一个函数来返回一个新的字符串，其中包含骰子滚动的结果。

我们首先需要的是一个模拟掷骰子的函数。让我们做一些简单的事情。

```
const rollDice = (dice, sides) => {
  let result = 0
  for (let i = 0; i < dice; i++) result += Math.ceil(Math.random() * sides)
  return result
} 
```

解决了这个问题，让我们开始研究将结果添加到每个掷骰子旁边的函数:

```
const addResult = (string, roller) => {
  const regEx = /(\d+)d(\d+)/g
  return string.replace(regEx, (roll, dice, sides) => `${roll} [${roller(dice, sides)}]`)
}

const string = "The first dice roll is 2d6 and the second one is 3d8."

addResult(string, rollDice)
//Returns something like this: "The first dice roll is 2d6 [7] and the second one is 3d8 [17]." 
```

这里我们创建了一个函数，它接受一个字符串和另一个函数来掷骰子。在函数内部，我们调用`.replace()`来查找字符串中的掷骰子表达式，并为每个匹配执行我们的掷骰子，这样得到的新字符串就包含了表达式之后的掷骰子结果。

在这种情况下，我们的正则表达式有两个被捕获的组，每个组由一个或多个数字(`(\d+)`)组成。当我们调用回调函数时，我们将第一个组命名为`dice`，将第二个组命名为`sides`。整个匹配表达式将在参数`roll`中被捕获。我们不需要它能接受的任何其他参数，所以我们简单地忽略它们。

## 当它变得真正有趣的时候

但是我们甚至可以使用`.replace()`而不替换字符串中的任何内容。考虑下面的例子。

```
const addResult = (string, roller) => {
  const regEx = /(\d+)d(\d+)/g
  const newArr = []
  string.replace(regEx, (roll, dice, sides) => newArr.push({
      roll: roll, 
      result: roller(dice, sides) 
    }))
  return newArr
} 
```

这一次，我们的`addResult()`返回一个对象数组，其中存储了所有掷骰子表达式及其计算结果，而没有对原始字符串做任何实际的操作。当我们想从一个字符串中收集数据，但又不想改变它时，这种技术是很有用的。

## 结论

因为它可以接受一个函数作为参数，所以当我们需要以复杂的方式操作一个文本或者我们只是想从中收集信息时，`.replace()`是一个非常有用的方法。

如果想了解更多，可以随时查看 [MDN web docs](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String/replace) 。就我个人而言，我也发现 JavaScript 忍者 的 [*秘密中关于正则表达式的章节非常有启发性。*](https://www.manning.com/books/secrets-of-the-javascript-ninja)