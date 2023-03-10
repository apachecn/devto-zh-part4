# 逆向工程-理解测试中的间谍

> 原文：<https://dev.to/itnext/reverse-engineering-understanding-spies-in-testing-3443>

# 逆向工程——了解测试中的间谍

在 [Twitter](https://twitter.com/chris_noring) 上关注我，很乐意接受您对主题或改进的建议/Chris

[![](img/756e1f7885e3ebdd52c0557ca71175d6.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--_546Qwt0--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/8zip3uj7t1n8o1qs54bn.jpg)

> 我们使用 spy 不仅模拟来自依赖项的响应，还确保我们的依赖项被正确调用。正确的意思是，我们指的是次数，正确的参数类型和数量。我们可以验证很多东西来确保我们的代码行为正确。这个练习是关于理解《茉莉花开》中的间谍，在引擎盖下发生了什么

在这篇文章中，我们试图解释:

*   **为什么**，了解我们为什么使用间谍以及他们有什么好远
*   **什么**，解释间谍能为我们做什么
*   **如何**，揭示他们必须如何在幕后工作，但试图逆向工程他们的公共 API

TLDR，如果你只是想看实现，而不关心我们是如何实现的，那么滚动到底部，那里有完整的代码。:)

## 为什么会有间谍

我们来设定场景。我们有一个业务关键型功能，我们希望将订单发送给用户。应用是用 Node.js 写的，也就是后端的 JavaScript。

我们必须在发货前收到货款。对这段代码的任何更改都应该被我们即将实现的间谍捕获到。

代码是这样的:

```
async function makeOrder(
  paymentService, 
  shippingService, 
  address, 
  amount, 
  creditCard
) {
  const paymentRef = await paymentService.charge(creditCard, amount)

  if (paymentService.isPaid(paymentRef)) {
    shippingService.shipTo(address);
  }
} 
```

我们有函数`makeOrder()`。`makeOrder()`从两个不同的依赖者 a `shippingService`和 a `paymentService`那里获得帮助。重要的是，在我们发货之前，调用`paymentService`来检查我们是否已经收到付款，否则只会对业务不利。

同样重要的是，我们在某个时候调用`shippingService`来确保物品被交付。现在，代码很少如此清晰，所以你可以确切地看到它做了什么，以及删除下面任何代码的后果。重点是我们需要为下面的代码编写测试，我们需要间谍来验证我们的代码被直接调用。

简而言之:

> 间谍们对结果过于自信

## 什么

好了，我们已经在本文的前几行提到了 Spies 可以帮助我们检查一个依赖项被调用了多少次，使用了什么参数等等，但是让我们试着列出所有我们知道的 Jasmine Spies 的特性:

*   **被调用**，验证它已经被调用
*   **Args** ，验证它已被某个参数调用
*   **被调用的次数**，验证它被调用的次数
*   **调用次数和参数**，验证调用的所有次数和使用的所有参数
*   **嘲讽**，返回一个嘲讽值
*   **恢复**，因为间谍取代了原来的功能，我们需要在某个时候将我们的依赖恢复到原来的实现

这是一个相当多的特性列表，它应该能够帮助我们断言上述`makeOrder()`的行为。

## 该如何如何

这是我们开始研究 Jasmine 间谍和公共 API 的地方。从那里，我们将开始勾画出一个实现可能是什么样子。

那好吧。在 Jasmine 中，我们通过调用如下代码来创建一个间谍:

```
const apiService = {
  fetchData() {}
} 
```

然后我们在这样的测试中使用它:

```
it('test', () => {
  // arrange
  spyOn(apiService, 'fetchData')

  // act
  doSomething(apiService.fetchData)

  // assert
  expect(apiService.fetchData).toHaveBeenCalled();
}) 
```

正如你在上面看到的，我们有三个不同的步骤需要注意。

1.  **创造**的间谍与`spyOn()`
2.  **召唤**间谍
3.  断言间谍已被传唤

让我们开始实施

### 创造间谍

通过观察它的使用方式，你会意识到你所取代的是一个真实的功能，而不是一个模拟的功能。这意味着我们最终分配给`apiService.fetchData` **的必须是一个函数**。

谜题的另一部分是我们如何断言它已经被调用。我们有下面一行要考虑:

```
expect(apiService.fetchData).toHaveBeenCalled() 
```

此时我们需要开始实现那行代码，就像这样:

```
function expect(spy) {
  return {
    toHaveBeenCalled() {
      spy.calledTimes()
    }
  }
} 
```

> 等等。你刚才说`apiService.fetchData`是函数。然而在`expect()`中，你把它发送进来，然后像调用一个对象一样调用`calledTimes()`。我迷路了:(

啊，我明白了。你可能有 C#或 Java 等面向对象语言的背景，对吗？

> 你怎么知道的？

在那些语言中，你要么是一个对象，要么是一个函数，永远不会两者都是。但是我们在 JavaScript 中，JavaScript 声明:

*函数是函数对象。在 JavaScript 中，任何不是原始类型(未定义、空、布尔、数字或字符串)的东西都是对象*。

这意味着我们的 spy 是一个函数，但它有方法和属性，就像它是一个对象一样..

> 尼利斯。而且很奇怪..

那好吧。有了这些知识，我们就可以开始实施。

```
// spy.js

function spy(obj, key) {
  times = 0;
  old = obj[key];

  function spy() {
    times++;
  }

  spy.calledTimes = () => times;

  obj[key] = spy;
}

function spyOn(obj, key) {
  spy(obj, key);
}

module.exports = {
  spyOn
} 
```

`spyOn()`调用内部创建函数`_spy()`的`spy()`，该函数知道变量`times`并公开公共方法`calledTime()`。然后我们最终将`_spy`分配给我们想要替换其功能的对象。

### 添加匹配器`toHaveBeenCalled()`

让我们创建文件`util.js`，并使它看起来像这样:

```
// util.js

function it(testName, fn) {
  console.log(testName);
  fn();
}

function expect(spy) {
  return {
    toHaveBeenCalled() {
      let result = spy.calledTimes() > 0;
      if (result) {
        console.log('spy was called');
      } else {
        console.error('spy was NOT called');
      }
    }
  }
}

module.exports = {
  it, 
  expect
} 
```

如你所见，它只包含了一个非常简单的`expect()`和`it()`方法的实现。让我们也创建一个`demo.js`文件来测试我们的实现:

```
// demo.js

const { spyOn } = require('./spy');
const { it, expect } = require('./util');

function impl(obj) {
  obj.calc();
}

it('test spy', () => {
  // arrange
  const obj = {
    calc() {}
  }

  spyOn(obj, 'calc');

  // act
  impl(obj);

  // assert
  expect(obj.calc).toHaveBeenCalled();
}) 
```

我们已经取得了很大的进步，但是让我们看看我们如何才能改进。

### 添加匹配器`toHaveBeenCalledTimes()`

这个匹配器已经写好了它自己，因为我们在记录我们调用某个东西的次数。简单地将下面的代码添加到我们的`it()`函数中，像这样放在`util.js`中:

```
toHaveBeenCalledTimes(times) {
  let result = spy.calledTimes();
  if(result == times) {
    console.log(`success, spy was called ${times}`)
  } else {
    console.error(`fail, expected spy to be called: ${times} but was: ${result}`)
  }
} 
```

### 添加匹配器`toHaveBeenCalledWith()`

现在这个匹配器想要我们验证我们能告诉我们的间谍被这样调用和使用:

```
expect(obj.someMethod).toHaveBeenCalledWith('param', 'param2'); 
```

让我们回顾一下`spy()` :
的实现

```
// excerpt from spy.js

function spy(obj, key) {
  times = 0;
  old = obj[key];

  function spy() {
    times++;
  }

  spy.calledTimes = () => times;

  obj[key] = spy;
} 
```

我们可以看到，我们通过变量`times`捕获了某个东西被调用的次数，但是我们想稍微改变一下。不要使用存储数字的变量，让我们用一个数组来代替它，比如:

```
// spy-with-args.js

function spy(obj, key) {
  let calls = []

  function _spy(...params) {
    calls.push({
      args: params
    });
  }

  _spy.calledTimes = () => calls.length;
  _spy._calls = calls;

  obj[key] = _spy;
} 
```

正如你在`_spy()`方法中看到的，我们收集了所有的输入参数，并将它们添加到一个数组`calls`中。`calls`不仅会记住调用的次数，还会记住每次调用是用什么参数完成的。

**创建匹配器**

为了测试它是否存储了所有的调用及其参数，让我们在我们的`expect()`方法中创建另一个*匹配器*函数，并将其命名为`toHaveBeenCalledWith()`。现在对它的要求是，我们的间谍应该在某个时候用这些参数调用。它没有说什么迭代，所以这意味着我们可以通过我们的`calls`数组循环，直到我们找到一个匹配。

让我们将我们的匹配器添加到我们的`utils.js`中的方法`it()`，就像这样:

```
// excerpt from util.js
toHaveBeenCalledWith(...params) {
  for(var i =0; i < spy._calls.length; i++) {
    const callArgs = spy._calls[i].args;
    const equal = params.length === callArgs.length && callArgs.every((value, index) => { 
      const res = value === params[index];
      return res;
    });
    if(equal) {
      console.log(`success, spy was called with ${params.join(',')} `)
      return;
    }
  }
  console.error(`fail, spy was NOT called with ${params} spy was invoked with:`);
  console.error(spy.getInvocations());

} 
```

上面你可以看到我们如何比较`params`，这是我们对它的称呼，和我们对间谍的调用中的每一个参数。

现在，让我们给`demo.js`和我们的测试方法调用添加一些代码，这样我们就可以试用我们的新匹配器，就像这样:

```
 // excerpt from demo.js

it('test spy', () => {
  // arrange
  const obj = {
    calc() {}
  }

  spyOn(obj, 'calc');

  // act
  impl(obj);

  // assert
  expect(obj.calc).toHaveBeenCalled();
  expect(obj.calc).toHaveBeenCalledWith('one', 'two');
  expect(obj.calc).toHaveBeenCalledWith('three');
  expect(obj.calc).toHaveBeenCalledWith('one', 'two', 'three');
}) 
```

在终端中运行这个命令，我们会得到:

[![](img/cbcee4c85d1e341e76823901456987a2.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--u9q_WnNO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/lebeq3o7ata3kmcxl7no.png)

我们可以看到它像一个魔咒一样工作。前两次成功，最后一次失败，这是理所应当的。

### 复位，最后一片

我们还想增加一项功能，即重置实现的能力。现在，这可能是我们做的最简单的事情。让我们访问我们的`spy-with-args.js`文件。我们需要做到以下几点:

1.  添加对旧实现的引用
2.  添加一个方法`reset()`,让我们回到最初的实现

**添加参考**

在我们的`spy()`函数中添加这一行:

```
let old = obj[key]; 
```

这将把实现保存到变量`old`

**添加`reset()`方法**

只需添加下面一行:

```
_spy.reset = () => obj[key] = old; 
```

`spy()`方法现在看起来应该是这样的:

```
function spy(obj, key) {
  let calls = []
  let old = obj[key];

  function _spy(...params) {
    calls.push({
      args: params
    });
  }

  _spy.reset = () => obj[key] = old;
  _spy.calledTimes = () => calls.length;
  _spy.getInvocations = () => {
    let str = '';
    calls.forEach((call, index) => {
      str+= `Invocation ${index + 1}, args: ${call.args} \n`;
    });

    return str;
  }

  _spy._calls = calls;

  obj[key] = _spy;
} 
```

## 总结

我们已经走到了尽头。我们从一开始就实现了一个间谍。此外，我们已经解释了*几乎*所有东西都是对象，这使得以我们的方式实现它成为可能。

最终结果是一个 spy，它存储了所有的调用和调用它的参数。我们还设法创建了三个不同的匹配器来测试我们的间谍是否被调用，调用了多少次，以及使用了什么参数。

总而言之，这是一次了解间谍本质的成功冒险。

我们确实意识到这只是某个东西的开始，将其投入生产意味着我们可能应该支持一些事情，比如比较某个东西是否被对象调用、支持、模仿等等。我把这个留给你做练习。

作为另一个带回家的练习，看看你能否为我们在开始提到的函数`makeOrder()`编写测试。

### 全码

以下是完整的代码，以防我中途跟丢了你:

**util.js，包含我们的 matcher 函数**

包含函数`it()`和`expect()`及其匹配器的文件。

```
// util.js

function it(testName, fn) {
  console.log(testName);
  fn();
}

function expect(spy) {
  return {
    toHaveBeenCalled() {
      let result = spy.calledTimes() > 0;
      if (result) {
        console.log('success,spy was called');
      } else {
        console.error('fail, spy was NOT called');
      }
    },
    toHaveBeenCalledTimes(times) {
      let result = spy.calledTimes();
      if(result == times) {
        console.log(`success, spy was called ${times}`)
      } else {
        console.error(`fail, expected spy to be called: ${times} but was: ${result}`)
      }
    },
    toHaveBeenCalledWith(...params) {
      for(var i =0; i < spy._calls.length; i++) {
        const callArgs = spy._calls[i].args;
        const equal = params.length === callArgs.length && callArgs.every((value, index) => { 
          const res = value === params[index];
          return res;
        });
        if(equal) {
          console.log(`success, spy was called with ${params.join(',')} `)
          return;
        }
      }
      console.error(`fail, spy was NOT called with ${params} spy was invoked with:`);
      console.error(spy.getInvocations());

    }
  }
}

module.exports = {
  it, 
  expect
} 
```

**间谍实施**

我们的间谍实现`spy-with-args.js` :

```
function spyOn(obj, key) {
  return spy(obj, key);
}

function spy(obj, key) {
  let calls = []
  let old = obj[key];

  function _spy(...params) {
    calls.push({
      args: params
    });
  }

  _spy.reset = () => obj[key] = old;
  _spy.calledTimes = () => calls.length;
  _spy.getInvocations = () => {
    let str = '';
    calls.forEach((call, index) => {
      str+= `Invocation ${index + 1}, args: ${call.args} \n`;
    });

    return str;
  }

  _spy._calls = calls;

  obj[key] = _spy;
}

module.exports = {
  spyOn
}; 
```

**demo.js，用于测试**

最后是我们的`demo.js`文件:

```
const { spyOn } = require('./spy-with-args');
const { it, expect } = require('./util');

function impl(obj) {
  obj.calc('one', 'two');

  obj.calc('three');
}

it('test spy', () => {
  // arrange
  const obj = {
    calc() {}
  }

  spyOn(obj, 'calc');

  // act
  impl(obj);

  // assert
  expect(obj.calc).toHaveBeenCalled();
  expect(obj.calc).toHaveBeenCalledWith('one', 'two');
  expect(obj.calc).toHaveBeenCalledWith('three');
  expect(obj.calc).toHaveBeenCalledWith('one', 'two', 'three');
}) 
```