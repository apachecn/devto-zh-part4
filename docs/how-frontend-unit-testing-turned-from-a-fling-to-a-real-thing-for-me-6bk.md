# 对我来说，前端单元测试是如何从一时兴起变成现实的

> 原文：<https://dev.to/programmiri/how-frontend-unit-testing-turned-from-a-fling-to-a-real-thing-for-me-6bk>

*本文来源于[2017 年 9 月 21 日](https://medium.com/@programmiri/how-frontend-unit-testing-turned-from-a-fling-to-a-real-thing-for-me-36bf2844d3a0)。我正在将我的内容从中等移动到开发人员到*

如果你正在成为一名职业程序员，你很难避开单词“ [testing](https://en.wikipedia.org/wiki/Software_testing) ”。当然，我读到过它，甚至花了几个小时用测试驱动开发来编写测试和解决人为的问题。但是我没有机会在日常的编码生活中应用我所学到的知识。这不是我的工作。我们不写测试(但是，我想改变这一点)，所以很长一段时间，我所知道的关于测试更多的是在学术层面上。

# 艳遇是如何开始的

## 宠物项目来救援了！

我很幸运能和有经验的程序员一起做几个宠物项目。我从这些项目中学到的最重要的东西是新的或不同的工作流程、最佳实践和模式。在我目前从事的一个项目中，我终于在真实的环境中编写了真实的测试。见鬼，我玩得很开心！

无论如何，说到单元测试，我仍然是一个该死的初学者。但现在我终于看到并感受到了以前只在书上读到过的所有好处。是的，我有点坠入爱河了！

几天以来，我一直在写一篇关于前端测试的文章，其中有一个来自上述 pet 项目的例子。它帮助我了解更多关于测试的知识。与此同时，我试图构建一个简单的工作流来为非测试环境测试我的代码，这样我将能够交付更稳定的解决方案。我对测试有了新的感觉，但是我不确定它们是否以及如何在我的工作中发挥作用。

## 现实世界的追赶— TIFU

今天，我收到了一张虫票。几个月前我写的一个简单的 Javascript 特性。我看到了门票的描述，看了一眼代码，我对自己非常愤怒。我可以马上看到我的错误，这是一个愚蠢的错误。

**背景**:该脚本应该接受一个整数，并返回一个欧元货币格式的数字——如果需要，用一个点作为千位分隔符，并在结尾加上“欧元”。

这就是我的剧本的样子:

```
function formatSum(sum) {
  var sumNum = sum.toString().split('');
  var sumFormatted = [];

  for (var i = 0; i < sumNum.length; i++) {
    var currNum = sumNum[i];

    if (i != 0 && i % 3 == 0) {
      sumFormatted.push('.');
    }

    sumFormatted.push(currNum);
  }

  if (sumFormatted.length > 0) {
    sumFormatted.push(' Euro');
  }

  return sumFormatted.join('');
} 
```

Enter fullscreen mode Exit fullscreen mode

这是我 4 个月前写的，真的，我并不自豪。很长一段时间没有人注意到这个错误，因为这个函数被用来格式化一个 API 提供的整数——巧合的是——它们在一段时间内都有六位数字。

*   我的第一个想法:*该死，你真笨。*
*   我的第二个想法:*该死，你真他妈的蠢，你只在直播网站上检查了你的剧本。你没有正确测试你该死的代码！*
*   我的第三个想法:*嗯。你现在比以前聪明了。*

## 我和测试:认真起来

下面是我修复这个错误的方法:我编写测试。我没碰过剧本，一点也没改。我打开编辑器，写了起来。的。F *。测试。* *

```
function assertEqual(actual, expected) {
  if (expected === actual) {
    console.info('[SUCCESS] Is ' + expected);
  } else {
    console.error('[ERROR] Expected ' + actual + ' to be ' + expected);
  }
}

function tests() {
  console.log('Test results');

  assertEqual(formatSum(1), '1 Euro');
  assertEqual(formatSum(12), '12 Euro');
  assertEqual(formatSum(123), '123 Euro');
  assertEqual(formatSum(1234), '1.234 Euro');
  assertEqual(formatSum(12345), '12.345 Euro');
  assertEqual(formatSum(123456), '123.456 Euro');
  assertEqual(formatSum(1234567), '1.234.567 Euro');
} 
```

Enter fullscreen mode Exit fullscreen mode

我做了测试。

| ![](img/abad170190c508f6da486c2157033a79.png) |
| --- |
| 我使用了不久前做的一个旧的咕噜设置来练习 TDD。 |

故障单报告了一个带有 5 位数的错误格式，测试重现了这个数字— `123.45 Euro`而不是`12.345 Euro`。

我修改了我的剧本。

```
function formatSum(sum) {
  var sumNum = sum.toString().split('').reverse();
  var sumFormatted = [];

 for (var i = 0; i < sumNum.length; i++) {
    var currNum = sumNum[i];

if (i != 0 && i % 3 == 0) {
        sumFormatted.push('.');
    }

sumFormatted.push(currNum);
  }

if (sumFormatted.length > 0) {
    sumFormatted.reverse();
    sumFormatted.push(' Euro');
  }

return sumFormatted.join('');
} 
```

Enter fullscreen mode Exit fullscreen mode

**注**:不然我没碰。我没有重构它，没有让它变得更好，没有让它变得更漂亮——我只是修复了我的错误。

我又做了一次测试。

| ![](img/ddb6fabc4505ff5f5a672b7731e6e323.png) |
| --- |
| 这才是我喜欢的观点！ |

完成了。我可以推动这种改变。我花了大约 10 分钟骂了我一句，写了我的测试并修改了我的脚本。就是这样！现在，有测试我的功能。我可以重构 ist，写一个新的——任何东西。在测试的时候。

## 我今天学到了什么

*   我真的很喜欢测试。我们现在是认真的了！
*   关于测试，我有太多该死的东西要学，我真的很期待。
*   我不必等待任何人或任何事来编写测试。是的，最好是 100%的代码覆盖率，并且测试所有的东西——但是即使是一个小小的测试也可以改进我的代码，并且从长远来看使我成为一个更好的程序员。嘿，我是一个完美主义者，我努力改进；)
*   测试不需要耗费时间和金钱！相反:如果我一开始就写了正确的测试，第二张 bug 罚单就不会发生。
*   我今天写的代码比三个月前更好。
*   现在，在没有测试的情况下提交(有时甚至是编写)代码有点像开车不系安全带。

* * *

封面图片:照片由[塞里斯·劳](https://unsplash.com/@ceryslowe_1?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com) 上拍摄