# 测试(和计时)字符串反转功能

> 原文：<https://dev.to/bugmagnet/testing-string-reversal-functions-8m6>

所以莎拉·奇马写了一篇文章，讲述了用四种不同的方法来反转一根绳子。一些人提出了其他解决方案。我也写了一些。

然后有人建议我们试着找出哪个是最快的。接下来是我在努力。

所以我需要首先提一下我的工作环境。它叫做 [Lychen](https://github.com/axtens/Lychen) ，是一个 C#命令行应用程序中 V8 JavaScript 引擎的包装，可以访问一些 C#对象、方法和属性。Lychen 不应该是尖端的。更确切地说，它在刀刃的脊背上，在不完全离开刀刃的情况下，尽可能远离刀刃。(参见[维基](https://github.com/axtens/Lychen/wiki))。

你可能会对我说:“哎，那[节点](https://nodejs.org/en/)呢？”我的回答通常是“我只是不能理解承诺和异步。也许有一天。”

这是代码。

```
const Console = CS.System.Console; 
```

Enter fullscreen mode Exit fullscreen mode

`CS.`在 Lychen (V8)环境中公开大量 C#核心和第三方对象。我们创建了一个缩写，而不是一直输入`CS.System.Console`。

```
if (CSSettings.ContainsKey("/D")) {
  debugger;
} 
```

Enter fullscreen mode Exit fullscreen mode

启动时，`CSSettings` (a `Dictionary<string,object>`)接收所有命令行参数。在这种情况下，如果是`/D`我们就调试。

```
const times = CSSettings.ContainsKey("/TIMES") ? parseInt(CSSettings("/TIMES"), 10) : 1000; 
```

Enter fullscreen mode Exit fullscreen mode

类似地，这里我们检查`/TIMES`的存在，如果是`/TIMES:123`，那么`times`被设置为`123`。否则`times`默认为`1000`。因为我们希望能够多次运行每个测试。

由于操作系统缓存的原因，任何例程的第一次测试通常比后续运行花费更长的时间。我们将进行多次测量，然后取平均值，希望能更好地了解事情真正需要多长时间。

```
var original;
if (CSSettings.ContainsKey("/RANDOM")) {
  original = Array(12500)
    .fill(0)
    .map(function () {
      return String.fromCharCode(Math.floor(Math.random() * 256));
    }).join("");
} else {
  original = Array(500).join("lewd did i live - evil i did dwel").substr(0, 12500);
} 
```

Enter fullscreen mode Exit fullscreen mode

如果命令行包含`/RANDOM`，我们将生成一个 12500 个随机 ASCII 字符的测试字符串。否则，我们用一些文本填充数组，然后将其截断为 12500 个字符。之所以选择 12500，是因为较大的数字会导致递归函数失败。

```
var reversed = Sarah_ForOf(original); 
```

Enter fullscreen mode Exit fullscreen mode

我们使用下面的反转函数之一来反转`original`测试字符串，这样我们就可以仔细检查反转实际上是 WAA(像宣传的那样工作)。

```
function TimeTest(name, fn, original) {
  var Stopwatch = new CS.System.Diagnostics.Stopwatch();
  Stopwatch.Start();
  var answer = fn(original);
  Stopwatch.Stop();
  var ts = Stopwatch.Elapsed;
  return {
    name: name,
    ticks: ts.Ticks,
    reversed: answer
  };
} 
```

Enter fullscreen mode Exit fullscreen mode

我们使用 C#的`System.Diagnostics.Stopwatch`来跟踪被测试函数的运行时间。这些参数是:函数名、函数引用和要测试的字符串。运行的`Elapsed`结果的`Ticks`与反转的名称和结果一起返回。更多关于`Ticks`在最后。

```
function EmptyFunction(string) {
  return string;
} 
```

Enter fullscreen mode Exit fullscreen mode

我们要考虑实际进行调用的成本，所以我们要计算加载运行一个返回字符串的空函数需要多长时间。

接下来是贡献的例程。

```
const Sarah_SplitReverseJoin = (string) => string.split("").reverse().join('');

const Nathanael_SplitReverseJoin = (string) => [...string].reverse().join('');

function Sarah_ForOf(string) {
  let reverseString = "";
  for (let character of string) {
    reverseString = character + reverseString;
  }
  return reverseString;
}

const Sarah_Reduce = (string) => string.split('').reduce((rev, char) => char + rev, '')

function Sarah_Recursive(string) {
  return string ? Sarah_Recursive(string.substring(1)) + string[0] : string;
}

function Theophanis_SplitFor(string) {
  let result = string.split('');
  for (let i = 0, j = string.length - 1; i < j; i++, j--) {
    result[i] = string[j];
    result[j] = string[i];
  }
  return result.join('');
}

function Theophanis_SplitFor_Bruced(string) {
  let result = string.split('');
  for (let i = 0, j = string.length - 1; i < j; i++, j--) {
    const string_i = string[i];
    const string_j = string[j];
    if (result[i] !== string_j) {
      result[i] = string_j;
    }
    if (result[j] !== string_i) {
      result[j] = string_i;
    }
  }

  return result.join('');
} 
```

Enter fullscreen mode Exit fullscreen mode

我认为在实际交换之前检查交换的需要是一个很好的优化。我错了，尤其是在随机数据方面

```
function Bruce_ArrayApplyMap(string) {
  return Array.apply(null, new Array(string.length).fill(0).map(function (_, i) {
      return string.charAt(string.length - 1 - i);
    })).join("");
}

function Bruce_MapSortMap(string) {
  return Array(string.length)
  .fill({})
  .map(function (item, index) {
    return {
      index: index,
      character: string.charAt(index)
    };
  }).sort(function (a, b) {
    return a.index > b.index ? -1 : (a.index === b.index ? 0 : 1);
  }).map(function (item) {
    return item.character;
  }).join("");
}

function Bruce_Recursive1(string) {
  return (string.length === 1)
   ? string
   : Bruce_Recursive1(string.substr(1)) + string.substr(0, 1);
}

function Bruce_Recursive2(string) {
  if (1 >= string.length)
    return string;
  return (
    string.substr(-1) +
    Bruce_Recursive2(string.substr(1, string.length - 2)) +
    string.substr(0, 1));
}

function Bruce_CharAt(string) {
  const result = Array(string.length);
  for (let i = string.length - 1, j = 0; i >= 0; i--, j++) {
    result[j] = string.charAt(i);
  }
  return result.join("");
}

function Bruce_CharAt2(string) {
    const result = Array(string.length).fill(1);
    result.map(function (item,index) {
        let rhs = string.length - 1 - index;
        result[index] = string.charAt(index);
    });
    return result.join("");
} 
```

Enter fullscreen mode Exit fullscreen mode

Sarah 帖子中的所有功能以及其他贡献者的功能。

```
const namesAndCodes = [{
    name: "Sarah_SplitReverseJoin",
    code: Sarah_SplitReverseJoin
  }, {
    name: "Sarah_ForOf",
    code: Sarah_ForOf
  }, {
    name: "Sarah_Reduce",
    code: Sarah_Reduce
  }, {
    name: "Sarah_Recursive",
    code: Sarah_Recursive
  }, {
    name: "Theophanis_SplitFor",
    code: Theophanis_SplitFor
  }, {
    name: "Theophanis_SplitFor_Bruced",
    code: Theophanis_SplitFor_Bruced
  }, {
    name: "Nathanael_SplitReverseJoin",
    code: Nathanael_SplitReverseJoin
  }, {
    name: "Bruce_ArrayApplyMap",
    code: Bruce_ArrayApplyMap
  }, {
    name: "Bruce_MapSortMap",
    code: Bruce_MapSortMap
  }, {
    name: "Bruce_Recursive1",
    code: Bruce_Recursive1
  }, {
    name: "Bruce_Recursive2",
    code: Bruce_Recursive2
  }, {
    name: "Bruce_CharAt",
    code: Bruce_CharAt
  }, {
    name: "Bruce_CharAt2",
    code: Bruce_CharAt2
  }
]; 
```

Enter fullscreen mode Exit fullscreen mode

要测试的名称和函数。

```
var gathering = {};

for (let i = 0; i < times; i++) {
  namesAndCodes.forEach(function (item) {
    const eps = TimeTest("EmptyFunction", EmptyFunction, original).ticks;
    const result = TimeTest(item.name, item.code, original);
    if (!gathering[result.name]) {
      gathering[result.name] = [];
    }
    gathering[result.name].push(result.ticks - eps);
  });
} 
```

Enter fullscreen mode Exit fullscreen mode

这里我们进行测试，从零循环到任何值`times`保持。我们通过`namesAndCodes`结构`forEach`。我们计算运行一个空函数所需的时间，然后从测试结果的节拍中减去这个时间。`gathering`将每次测试的结果保存在一个以函数名为关键字的数组中。

```
const average = arr => arr.reduce((p, c) => p + c, 0) / arr.length;

Object.keys(gathering).map(function (item) {
  return [item, average(gathering[item])];
}).sort(function (a, b) {
  return a[1] > b[1] ? 1 : a[1] === b[1] ? 0 : -1;
}).forEach(function (item) {
  Console.WriteLine("{0,-28}{1} ticks", item[0], item[1]);
});
"" 
```

Enter fullscreen mode Exit fullscreen mode

报告结果:将收集的对象转换为名称和平均值的数组[，]，对第二个项目进行排序，使最快的项目排在第一位，将结果写入控制台，名称在 28 个字符的字段中左对齐，后跟刻度。

结果呢？

```
>timer.ly  /TIMES:1000
Sarah_ForOf                 2141.86 ticks
Sarah_SplitReverseJoin      2444.758 ticks
Sarah_Reduce                2805.243 ticks
Bruce_CharAt                2842.139 ticks
Nathanael_SplitReverseJoin  3035.17 ticks
Theophanis_SplitFor         3142.142 ticks
Bruce_Recursive1            3319.84 ticks
Bruce_Recursive2            3451.674 ticks
Theophanis_SplitFor_Bruced  3616.858 ticks
Sarah_Recursive             4645.366 ticks
Bruce_ArrayApplyMap         5637.1 ticks
Bruce_MapSortMap            9754.566 ticks
Bruce_CharAt2               13721.967 ticks

>timer.ly  /TIMES:1000 /RANDOM
Sarah_ForOf                 1850.582 ticks
Sarah_SplitReverseJoin      2655.574 ticks
Theophanis_SplitFor         2815.478 ticks
Nathanael_SplitReverseJoin  2832.566 ticks
Bruce_CharAt                2842.439 ticks
Sarah_Reduce                2845.746 ticks
Bruce_Recursive2            3224.578 ticks
Bruce_Recursive1            3306.136 ticks
Theophanis_SplitFor_Bruced  3428.827 ticks
Sarah_Recursive             4258.6 ticks
Bruce_ArrayApplyMap         5421.202 ticks
Bruce_MapSortMap            9748.012 ticks
Bruce_CharAt2               13477.231 ticks 
```

Enter fullscreen mode Exit fullscreen mode

在我的电脑上每秒有 10，000，000 次滴答(使用 CS。系统.诊断.秒表.频率)。根据[文档](https://docs.microsoft.com/en-us/dotnet/api/system.diagnostics.stopwatch.elapsedticks?view=netframework-4.8)“elapsed ticks 值中的每个 tick 代表时间间隔，等于 1 秒除以频率。”

底线呢？Sarah 的 ForOf 和 SplitReverseJoin 是目前最快的。塞奥法尼斯的分裂也很好。也就是说，差异在微秒或更短的时间内。

*注*:非常感谢所有关于如何改进该测试机制的建议。提前感谢。