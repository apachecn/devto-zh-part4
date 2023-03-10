# 逆向工程——理解 JavaScript 中的承诺

> 原文：<https://dev.to/itnext/reverse-engineering-understand-promises-1jfc>

在 [Twitter](https://twitter.com/chris_noring) 上关注我，很乐意接受您对主题或改进的建议/Chris

> 在这里，我再次与另一篇关于逆向工程的文章。这个想法，就像上次一样，是为了更好地掌握你的手艺。这次我们来看看 Promises，它是一个异步结构，使您能够以同步的方式查看代码。你问的这个魔法是什么？读一读，你就会发现

如果您错过了我关于这个主题的第一篇文章，就在这里，它是单元测试库的逆向工程:

[![softchris image](img/11287239c3fc0c954046778309092f72.png)](/softchris) [## 逆向工程，如何用 JavaScript 构建测试库

### 克里斯·诺林 18 年 7 月 19 日 8 分钟阅读

#showdev #javascript #tutorial #beginners](/itnext/reverse-engineering-how-you-can-build-a-test-library-53e3)

回到这篇文章。承诺。我们计划在这里采用的方法是查看一下构造的公共 API，并尝试做出一些有根据的猜测，然后开始实现。我们希望对引擎盖下发生的事情有所了解，并希望在这个过程中变得聪明起来。

我们将涵盖以下内容:

*   **为什么**承诺，这是你需要问自己的最重要的问题之一。为什么我要学习/阅读/使用这个？
*   **什么**，承诺的核心概念是什么
*   **实施**，我们将实施一个基本承诺，但我们也将支持所谓的*链接*

准备好了吗？

> 好吧，我们开始吧。

## 为什么

因为 Promises 在 Node.js 的 JavaScript 和 Web 中已经是标准的一部分，这意味着 promise 这个词被采用了，那么，什么是好的同义词呢？嗯，我只是想到了我大脑想到的第一件事，那就是发誓，这让我回到了 1994 年。

手机看起来像这样:

[![](img/0f90a37a44c7bb1821fe08075e26b2b9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--95fL94zp--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/uleoivv2itwe7ch2nu9m.jpg)

MS-DOS 非常受欢迎，每个人都在玩 DOOM 游戏，当他们试图使用电话时，妈妈对你大喊大叫..；)

瑞典获得了一枚足球铜牌，对所有英国人来说，这是我们的 1966 年。

[https://www.youtube.com/embed/CI5xbXZ4w34](https://www.youtube.com/embed/CI5xbXZ4w34)

哦，是的，All-4-One 凭借《我发誓》荣登排行榜榜首

[https://www.youtube.com/embed/25rL-ooWICU](https://www.youtube.com/embed/25rL-ooWICU)

> 哟，我是来听代码老头的

是的，对不起。好的。承诺的伟大之处在于，它们让你以一种看起来同步而保持异步的方式来安排代码。

> 这有什么好的？

考虑另一种选择*回调地狱*，看起来像这样:

```
getData((data) => {
  getMoreData(data, (moreData) => {
    getEvenMoreData(moreData, (evenMoreData) => {
      console.log('actually do something')
    })
  })
}) 
```

你说三级，我也许可以接受。相信我，你不想生活在 3 层或 11 层。所以我们需要承诺。

> 好吧好吧，给我看看

有了承诺，你可以写出这样的结构:

```
getData()
  .then(getMoreData)
  .then(geteEvenMoreData) 
```

当我第一次看到这一切的时候，我就像哇，一切都变了。我实际上可以一行一行地读，正在发生的事情，没有奇怪的列表或任何东西，只是从头开始读。

Promises 使它成为 Web 和 Node.js 的标准，我们不知道没有它我们会做什么。

## 什么

让我们试着建立我们对承诺的了解，这样我们就可以重新创造它。

所以有了承诺，我们就有办法把我们做的任何异步的事情包装在一个承诺结构中，就像这样:

```
const promise = new Promise((resolve, reject) => {
  setTimeout(() => {
    // do something 
    resolve('data')
  }, 2000)
})

promise
  .then(
    data => console.log(data)
    err => console.error(err)
  ) 
```

上面我们可以看到，Promise 采用了一个工厂函数，它有两个参数`resolve`和`reject`，这两个参数都是函数。当`resolve`被调用时，`then()`函数中的第一个回调被调用。相反，当`reject`被调用时，`then`中的第二个回调被调用，并将其记录为错误。

我们还支持一些我们已经展示过的东西，在最后一个叫做*链接*的部分，它简单如下:

```
getData()
  .then(getMoreData)
  .then(geteEvenMoreData) 
```

从代码的角度来看，我们可以看到调用`then`创建了另一个承诺。到目前为止，我们已经提到以一种*同步*的方式来看待*异步*代码是有用的，但是还有更多。

让我们通过创建上面提到的函数来使上面的例子更清晰一点

```
function getData() {
  return new Promise((resolve, reject) => {
    resolve('data')
  })
}

function getMoreData(data) {
  return new Promise((resolve, reject) => {
    resolve(data +' more data')
  })
}

function getEvenMoreData(data) {
  return new Promise((resolve, reject) => {
    resolve(data + ' even more data')
  })
}

function getMostData(data) {
  return data + "most";
}

getData()
  .then(getMoreData)
  .then(getEvenMoreData)
  .then(getMostData)
  .then(data => {
    console.log('printing', data)
  }) 
```

我们的*链接*的额外优势是，我们可以对我们得到的数据进行操作，并将其发送到下一个函数。所以`data`可以作为参数发送给`getMoreData()`，其结果可以发送给下一个函数，依此类推。另外，请注意我们上面有一个叫做`getMostData()`的方法，这里我们甚至没有构造一个新的承诺，但它足以让我们从函数中返回一些东西，并且它正在被解决。

在实现之前，我们再提一件事，错误处理。现在，我们实际上已经展示了错误处理:

```
const promise = new Promise((resolve, reject) => {
  setTimeout(() => {
    // do something 
    reject('error')
  }, 2000)
})

promise
  .then(
    data => console.log(data)
    err => console.error(err)
  ) 
```

调用`reject`导致`then`中的第二个回调被调用。但是还有更多我们可以使用的东西叫做`catch()`。`catch()`的想法是作为一个*包罗万象的*方法。现在，重要的是要知道这是如何工作的。如果我们已经在`then`方法上有一个错误回调，那么`catch`将不会被调用。所以像这样的构造不会像预期的那样工作:

```
getData()
  .then(getMoreData, console.error)
  .then(getEvenMoreData)
  .catch(err => console.error) 
```

我们想要的很可能是这样的场景:

1.  一定要打电话
2.  如果错误，由本地错误处理程序处理
3.  在本地处理完错误后，确保我们缩短了流程

实现应该是这样的:

```
getData()
  .then(getMoreData, (err) => {
    // handle error locally
    console.error(err);
    throw new Error(err);
  })
  .then(getEvenMoreData)
  .catch(err => console.error) 
```

如果你想让它短路的话，上面的方法将会起作用。如果你不这样执行，连锁承诺实际上还会继续`getEvenMoreData`。

这是足够的背景和承诺如何工作的见解。接下来让我们尝试实现它们。

## 实现

当我自己经历这个练习时。我注意到承诺并不像看上去那么简单。

实现承诺有很多事情

*   让解决/拒绝生效+然后
*   连锁承诺
*   错误处理，既有本地错误处理程序，也有 catch 错误处理程序
*   确保我们在 then 回调中处理承诺的返回和更简单的对象

鉴于上述所有场景可能很容易变成一篇 20 分钟的阅读文章，我将努力实现足够多的内容，以获得有价值的见解。

### 承诺带解决/拒绝的构造

我们说过一旦我们开始实现它，我们就称它为`Swear`。

好吧，那么，建筑时间。让我们看看下面的构造，并试着让它工作起来:

```
const promise = new Promise((resolve, reject) => {
  resolve('data')
  // reject('error')
})

promise
  then(data => console.log(data)); 
```

通过观察它，我们可以得出以下结论:

*   **是一个类**，Promise 是一个类或者至少是一个构造函数
*   **工厂函数输入**，Promise 接受一个有两个输入参数`resolve`和`reject`的工厂函数。
*   `resolve`方法应该触发`then`回调

从以上结论我们来速写:

```
// remember, Promise = Swear

class Swear {
  constructor(fn) {
    this.fn = fn;
  }

  _resolve(data) {
    this._fnSuccess(data);
  }

  then(fnSuccess) {
    this._fnSuccess = fnSuccess;
    this.fn(this._resolve.bind(this));
  }
}

const swear = new Swear((resolve) => {
  resolve('data');
})
  .then(data => {
    console.log('swear', data);
  }) 
```

在终端中运行这个命令，我们会得到:

[![](img/55ca5ff223f404d9244fd155b26e3a86.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--fQLngfkE--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/rsmf3ldxn6s6ssslb59m.png)

### 错误回调

好了，到目前为止我们已经支持了`resolve()`也就是*成功*的场景，其中我们使用了`then()`中的第一个回调。现在我们正在寻找支持调用`reject()`，所以下面应该工作:

```
new Swear((resolve, reject) => {
  reject('err')
}) 
```

我们需要以下面的方式改变代码来使它工作:

```
class Swear {
  constructor(fn) {
    this.fn = fn;
  }

  _resolve(data) {
    this._fnSuccess(data);
  }

  _reject(err) {
    this._fnFailure(err);
  }

  then(fnSuccess) {
    this._fnSuccess = fnSuccess;
    this.fn(this._resolve.bind(this), this._reject.bind(this));
  }
}

const swear = new Swear((resolve) => {
  reject('error');
})
  .then(data => {
    console.log('swear', data);
  }, err => console.error(err)) 
```

运行上面的代码，您应该会得到这样的响应:

```
error error 
```

### 链接

在这一点上，我们有一个工作的基本结构。我们做到了还是？

嗯，我们还有路要走。在这之后，我们应该支持*链接*意味着我们应该支持我们可以写这样的代码:

```
const swear = new Swear((resolve) => {
  resolve('data');
})
  .then(data => {
    console.log('swear', data);
    return 'test';
  })
  .then(data => {
    console.log(data)
  }) 
```

这个结构的整个想法是，我们可以从一个承诺中提取回应，并将其重塑为其他东西，就像上面的把`data`变成`test`一样。但是如何支持它呢？从上面的代码中，当我们调用`then()`时，我们应该产生一个`Swear`对象，所以让我们添加那个部分:

```
class Swear {
  constructor(fn) {
    this.fn = fn;
  }

  _resolve(data) {
    this._fnSuccess(data);
  }

  then(fnSuccess) {
    this._fnSuccess = fnSuccess;
    this.fn(this._resolve.bind(this));

    return new Swear((resolve) => {
      resolve(/* something */)
    })
  }
} 
```

好的，我们在`then`的末尾返回`Swear`实例，但是我们需要给它一些数据。我们从哪里得到这些数据？实际上它来自于调用`this._fnSuccess`，我们在`_resolve()`中就是这么做的。所以让我们在那里添加一些代码:

```
class Swear {
  constructor(fn) {
    this.fn = fn;
  }

  _resolve(data) {
    this._data = this._fnSuccess(data);
  }

  then(fnSuccess) {
    this._fnSuccess = fnSuccess;
    this.fn(this._resolve.bind(this));

    return new Swear((resolve) => {
      resolve(this._data)
    })
  }
}

swear
.then(data => {
  console.log('swear', data);
  return 'test';
})
.then(data => {
  console.log(data);
}) 
```

让我们再试一次这段代码:

[![](img/48b3904bf1ac5b1380d7a9a258b59ab7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--EwWEvaVp--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/tnxh0h5ayudz5mii4ibd.png)

我们可以从上面看到，我们的两个`.then()`回调都被击中。

### 实施抓捕

Catch 具有以下能力:

*   如果没有指定`then`错误回调，则捕获错误
*   如果在`then`回调中出现异常，则与错误回调一起工作。

从哪里开始？添加一个`catch()`方法是一个好的开始

```
catch(fnCatch) {
  this._fnCatch = fnCatch;
} 
```

让我们想一想。只有在没有其他错误回调处理过错误的情况下，才应该调用它。它还应该知道错误是什么，不管它发生在承诺链的什么地方。

看看承诺链是如何工作的，错误似乎并没有短路这个链，这意味着如果我们保存错误并传递它，我们应该是好的。我们还应该考虑在处理错误时使用某种类型的*处理的*概念。

好了，下面是实现的全部荣耀:

```
class Swear {
  constructor(fn, error = null) {
    this.fn = fn;
    this.handled = false;
    this._error = error;
  }

  _resolve(data) {
    this._data = this._fnSuccess(data);
  }

  _reject(err) {
    this._error = err;
    if(this._fnFailure) {
      this._fnFailure(err);
      this.handled = true;
    }
  }

  then(fnSuccess, fnFailure) {
    this._fnSuccess = fnSuccess;
    this._fnFailure = fnFailure;
    this.fn(this._resolve.bind(this), this._reject.bind(this));

    return new Swear((resolve) => {
      resolve(this._data)
    }, !this.handled ? this._error : null)
  }

  catch(fnCatch) {
    this._fnCatch = fnCatch;
    if (!this.handled && this._error && this._fnCatch) {
      this._fnCatch(this._error);
    }
  }
}

const swear = new Swear((resolve, reject) => {
  reject('error');
})

swear
.then(data => {
  console.log('swear', data);
  return 'test';
} /*, err => console.error('Swear error',err)*/)
.then(data => {
  console.log(data);
})
.catch(err => console.error('Swear, catch all', err)); 
```

从上面的代码中可以看出，在`then()`方法中，如果错误还没有被处理，我们将它传递给链中的下一个承诺。

```
return new Swear((resolve) => {
  resolve(this._data)
}, !this.handled ? this._error : null) 
```

我们认为如果本地回调处理了错误，就处理了错误，如我们的`_reject()`方法:
所示

```
_reject(err) {
  this._error = err;
  if(this._fnFailure) {
    this._fnFailure(err);
    this.handled = true;
  }
} 
```

最后，在我们的`catch()`方法中，我们都接收回调并调用所述回调，如果错误没有被处理，则存在错误。

```
catch(fnCatch) {
  this._fnCatch = fnCatch;
  if (!this.handled && this._error && this._fnCatch) {
    this._fnCatch(this._error);
  }
} 
```

我们可以删除`_fnCatch()`方法，直接调用`fnCatch`。

**试用**

最大的问题是，这有用吗？

好吧，让我们用一个本地回调和一个类似于
的`catch`方法来尝试一下

```
swear
.then(data => {
  console.log('swear', data);
  return 'test';
} , err => console.error('Swear error',err))
.then(data => {
  console.log(data);
})
.catch(err => console.error('Swear, catch all', err)); 
```

[![](img/349f4dfcda99c52d188b3765dc0e4e28.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--B8rHKP8q--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/tng48str9j487y9cswak.png)

这看起来是意料之中的，我们的本地错误会处理它，而我们的`catch()`方法永远不会被调用。

如果没有本地处理程序，只有一个`catch()`方法呢？

```
swear
.then(data => {
  console.log('swear', data);
  return 'test';
})
.then(data => {
  console.log(data);
})
.catch(err => console.error('Swear, catch all', err)); 
```

[![](img/0d739e53ac4aaeac991699240f6bdec7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--qYzlyCOm--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/54z8ha5cycvznn4exwjq.png)

我们就此打住吧..已经有很多真知灼见了，我们不要把它写成书。

## 总结

总之，我们开始实现承诺的一部分和一些功能，如解决/拒绝、本地错误处理程序、链接、无所不包。我们设法用几行代码做到了这一点，但我们也意识到还有一些事情要做，比如当它返回一个承诺/发誓时，能够在`then()`
中成功回调，在同一个回调或失败回调中引发异常，处理静态方法，如 Promise.resolve，Promise.reject，Promise.all，Promise.any

我要用这些来自全家福
的临别赠言留给你们

```
const swear = new Swear((resolve, reject) => {
  resolve('I swear');
})

swear
  .then(data => {
    return `${data}, by the Moon`
  })
  .then(data => {
    return `${data}, and the stars`
  })
  .then(data => {
    return `${data}, and the sun`
  })
  .then(data => console.log(data)) 
```