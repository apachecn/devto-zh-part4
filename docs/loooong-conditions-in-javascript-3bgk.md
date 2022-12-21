# JavaScript 中的 Loooong 条件

> 原文：<https://dev.to/themisir/loooong-conditions-in-javascript-3bgk>

没有人喜欢长队(至少我不喜欢😁)代码中。因为这使得代码以后很难阅读和理解。

有时我们需要给`if (condition)`添加很多条件，这使得我们的代码看起来像一个💩。

```
if (conditionA && conditionB && someSalt !== true && typeof module !== 'undefined' && a && b && (a+b<10 || a+b>0))
{
  console.log('Bye, World');
} 
```

条件树呢？

```
if (conditionA)
{
  // do smth

  if (conditionB)
  {
    // ...

    if (conditionC || a+b!=10)
    {
      console.log('Bye, World');
    }
  }
} 
```

在我的上一个项目中，我需要写很多这样的 if 条件。所以，我决定创建一个库，让我的代码看起来干净易读。还有，我不喜欢在 Js 种树🙄。

而且，就是这样。我将介绍我的新 js 库，名为: [condjs](https://github.com/TheMisir/condjs) 。

下面是使用[条件](https://github.com/TheMisir/condjs) :
建立条件的样子

```
const Condition = require('condjs');

// the old way:
if (conditionA && conditionB && someSalt !== true && typeof module !== 'undefined' && a && b && (a+b<10 || a+b>0))
{
  console.log('Bye, World');
}

// the condjs way:
Condition.with(conditionA)
  .and(conditionB)
  .and(someSalt !== true)
  .and(typeof module !== 'undefined')
  .and(a)
  .and(b)
  .and(c => c
    .or(a+b<10)
    .or(a+b>0))
  .then(() => {
    console.log('Bye, World');
  }); 
```

而且，🌲🌲🌳🌴🌲🎄。

```
// the old way:
if (conditionA)
{
  // do smth

  if (conditionB)
  {
    // ...

    if (conditionC || a+b!=10)
    {
      console.log('Bye, World');
    }
  }
}

// the condjs way:
var cond = Condition.with(conditionA);

cond.then(() => {
  // do smth
});

cond.and(conditionB);

cond.then(() => {
  // ...
});

cond.and(c => c
  .or(conditionC)
  .or(a+b!=10));

cond.then(() => {
  console.log('Bye, World');
}); 
```

请让我知道你对的想法📚库，感谢您花时间阅读我的第一篇 DEV 文章😊！