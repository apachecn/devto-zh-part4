# 常数不是真正的常数

> 原文：<https://dev.to/aminnairi/constants-are-not-really-constants-416o>

用过`const`关键字吗？如果没有，它允许您定义不能重新赋值的变量。

```
"use strict";

const fruit = "orange";

fruit = "apple";
// TypeError: invalid assignment to const `fruit' 
```

Enter fullscreen mode Exit fullscreen mode

[网上试试](https://repl.it/repls/PracticalDarksalmonActivemovie)。

它们还具有块范围的优势。但这不是这篇文章的重点。你可以在这里找到更多关于`const`关键词[的信息。](https://developer.mozilla.org/fr/docs/Web/JavaScript/Reference/Instructions/const)

我们可以在代码中为任何变量定义常量。

```
"use strict";

const answer = 42;
const awesome = true;

answer = 41;
// TypeError: Assignment to constant variable.

awesome = false; 
```

Enter fullscreen mode Exit fullscreen mode

[网上试试](https://repl.it/repls/OddSnappyCommas)。

## 一天一个苹果，远离异常...

但是现在您对常量更有经验了，并且您想将这种模式应用到更复杂的变量，如数组和对象，因为您现在确信不可变变量是防止代码中出现错误和副作用的好方法。

```
"use strict";

const fruits = [
  "apple",
  "banana",
  "lemon",
  "orange"
];

fruits[0] = "salad";
// ... 
```

Enter fullscreen mode Exit fullscreen mode

[网上试试](https://repl.it/repls/GrowlingTenseCareware)。

如果运行这段代码，您会注意到没有抛出任何错误。这段代码没有任何问题，它的行为也不奇怪。事实上，它完全按照设计的方式运行:const 关键字只防止重新分配。还记得上面的错误信息吗？所以这意味着前面的代码会工作，而这段代码不会:

```
"use strict";

const fruits = [
  "apple",
  "banana",
  "lemon",
  "orange"
];

fruits = [
  "salad",
  "carrot",
  "cucumber",
  "potato"
];
// TypeError: Assignment to constant variable. 
```

Enter fullscreen mode Exit fullscreen mode

[网上试试](https://repl.it/repls/BaggyCircularAccounting)。

现在我们抛出了异常！但是我们想要的是一个真正的常量变量，能够在我们试图重新赋值和改变它的成员时发出警告。JavaScript 中有一个解决方案是 [`Object.freeze`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/freeze) 方法。

```
"use strict";

const fruits = Object.freeze([
  "apple",
  "banana",
  "lemon",
  "orange"
]);

fruits[0] = "salad";
// TypeError: Cannot assign to read only property '0' of object '[object Array]' 
```

Enter fullscreen mode Exit fullscreen mode

[网上试试](https://repl.it/repls/SomeImaginativeLoopfusion)。

酷！现在我们的对象处于只读模式，不能被覆盖。如果我们试图向数组中添加值呢？

```
"use strict";

const fruits = Object.freeze([
  "apple",
  "banana",
  "lemon",
  "orange"
]);

fruits.push("salad");
// TypeError: Cannot add property 4, object is not extensible 
```

Enter fullscreen mode Exit fullscreen mode

[网上试试](https://repl.it/repls/OutlyingUpsetEngineer)。

耶！像我们期望的那样工作。

## 亲爱的，客人来了！

现在，一个稍微复杂一点的例子。比方说，我们想要一个对象，该对象具有配方的某种配置。属性将是成分，值将是剂量。

```
"use strict";

const classicPasta = Object.freeze({
  pasta: "500g",
  salt: "1tbs",
  pepper: "1tbs"
});

classicPasta.pepper = "11tbs";
// TypeError: Cannot assign to read only property 'pepper' of object '#<Object>' 
```

Enter fullscreen mode Exit fullscreen mode

[网上试试](https://repl.it/repls/PuzzledBothHypothesis)。

哇哦。我们差点没通过这道食谱。客人们正在品尝我们意大利面。他们爱他们。祝贺这次晚宴成功！但是现在你在你的国家获得了知名度。不知名的客人想尝尝你的美味食谱。快！让我们增加剂量...但是等等，我们的物体被冻结了。你不能。他们从很远的地方来...他们现在恨你。这是不行的！我们必须找到解决办法。这个方案就是 [`Object.seal`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/seal) 。这将锁定我们的对象，防止添加任何新的属性，但允许我们在需要时更新我们的对象。

```
"use strict";

const classicPasta = Object.seal({
  pasta: "500g",
  salt: "1tbs",
  pepper: "1tbs"
});

classicPasta.pasta = "2.5kg";
classicPasta.salt = "10tbs";
classicPasta.pepper = "10tbs";

classicPasta.sugar = "1tbs";
// TypeError: Cannot add property sugar, object is not extensible 
```

Enter fullscreen mode Exit fullscreen mode

[网上试试](https://repl.it/repls/ImpracticalCookedCompilerbug)。

精彩！你的客人来了。他们再次爱上了它...PastaScript 的又一次胜利！

## 结论

忘记所有烹饪书籍，学习 JavaScript。但是不要在你的意大利面中滥用胡椒或盐。这对你的心脏有害。别忘了时不时喝点水，运动运动！