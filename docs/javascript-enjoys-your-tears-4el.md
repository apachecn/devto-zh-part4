# JavaScript 喜欢你的眼泪

> 原文：<https://dev.to/rfornal/javascript-enjoys-your-tears-4el>

视频:

*   [JavaScript 和朋友演示:2020 年 3 月 17 日](https://www.youtube.com/watch?v=G3edxhIZPbw)

## JavaScript 享受你的眼泪

关于 JavaScript 的同步和异步行为，我已经讲过几次了。实际谈话是半 ***现场编码*** 谈话。在演讲结束后，把实际的代码放在那里让人们玩，这让我很紧张，所以我写了这篇文章。

[单线程异步 JavaScript？](https://sessionize.com/s/bob-fornal/single-threaded_and_asynchronous_ja/22652)

## 剧透...

从本质上讲，JavaScript 是一种同步、阻塞、单线程的语言。这意味着一次只能发生一件事。

当人们说 JavaScript 是一种异步语言时，他们的意思是你可以操纵 JavaScript 异步地运行。

异步的边界

*   用户交互
*   网络 IO
*   磁盘 IO
*   进程间通信
*   倍

## 定义

**并行:**多线程处理和调度(同一时间)。
**异步:**单线程和事件循环。

### 管理人...

**并发:**可以在同一时间段内发生的更高级别的任务。
**争用:**多件事需要同时发生。

## JavaScript 引擎详情

这些不是 JS 引擎的一部分；它们包含在浏览器或 NodeJS 运行时环境中:

*   设置超时
*   事件循环
*   Web APIs
*   信息排队
*   任务排队

这里有一个“JavaScript 的调用栈/事件循环/回调队列(以及它们是如何相互作用的)”的很好的可视化:[放大镜](http://latentflip.com/loupe/)。

*(延时 4ms 参见这篇关于 [MDN setTimeout](https://developer.mozilla.org/en-US/docs/Web/API/WindowOrWorkerGlobalScope/setTimeout#Reasons_for_delays_longer_than_specified) 的文章。*

 *> 在现代浏览器中，当由于回调嵌套(其中嵌套级别至少有一定深度)而触发连续调用时，或者在一定数量的连续间隔之后，setTimeout()/setInterval()调用被限制为至少每 4ms 一次。- MDN

## 回调地狱

**《末日金字塔》:**

*   问题的症状，而不是真正的问题。

**控制反转:**

*   传递回调时的信任点...它的行为是否符合预期。

**合理:**

*   时间依赖性需要嵌套；非线性思维。

### 注意事项...

**数据协调:**

*   以跟踪回调中的数据。

**分割回调:**

*   区分成功和错误。

**错误优先:**

*   与分割回拨相同的信任问题。

**许诺地狱...**

*   不良风格的流量控制。
*   嵌套承诺而不是垂直链接。

## 下面的代码会做什么？

```
export class ThoughtExercise {
  _time = 10000;
  _wrapper = null

  constructor() {};

  changeColor = () => {
    this._wrapper = document.getElementById('thought-wrapper');
    this._wrapper.style.backgroundColor = 'red';        
  };
  changeLayout = () => {
    let p = document.createElement('p');
    p.setAttribute('id', 'thought-run');
    p.innerText = 'Thought Exercise ...';
    this._wrapper.appendChild(p);
  };
  wait = () => {
    const start = Date.now();
    while(Date.now() < start + this._time) {};
  };
  event = () => {
    this.changeColor();
    this.changeLayout();
    this.wait();
  };

  start = () => {
    const button = document.getElementById('thought-button');
    button.classList.remove('hide');
    button.addEventListener('click', this.event);
  };
} 
```

Enter fullscreen mode Exit fullscreen mode

### 答案

这段代码本质上连接了一个按钮，当点击时触发****change layout***和 ***wait*** 函数。*

 *当点击按钮时，这段代码将被线程锁定，直到 ***这段。*** 已经过了 _ 时间。直到 10，000 毫秒后，背景颜色才会改变。

## 非异步代码

给定以下代码...

```
 export class NonAsynchronous {
  _numbers = [1, 2, 3];

  constructor() {};

  forEachSync = (items, callback) => {
    for (const item of items) {
      callback(item);
    }
  };

  forEachAsync = (items, callback) => {
    for (const item of items) {
      setTimeout(() => {
        callback(item);
      }, 0, item);
    }
  };

  runSync = () => {
    console.log('The Start');
    this.forEachSync(this._numbers, (number) => {
      console.log(number * 2);
    });
    console.log('The End');
  };

  runAsync = () => {
    console.log('The Start');
    this.forEachAsync(this._numbers, (number) => {
      console.log(number * 2);
    });
    console.log('The End');
  };

  start = (async = false) => {
    if (!async) {
      this.runSync();
    } else {
      this.runAsync();
    }
  } 
} 
```

Enter fullscreen mode Exit fullscreen mode

基本上这里可以出现两种不同的** *开始* *:非异步和异步；每个都有一个运行选项(* **runSync** *和 ***runAsync*** )。每个都作为关联的 forEach 功能运行。

当 ***this.runSync*** 触发时，我们应该在控制台中看到以下内容...

[![](img/fd221367263ad2d6165f55bd788b6823.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--zsjHsDxR--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/tdjhysr76yglfmwdtwun.png)

当***this.runAsync 触发时，我们应该看到以下内容...

[![](img/2395b44cfe297c0e9f02eb0f32159e93.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--04hb2Eea--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/pemhhhe95e7gtwpn4ldf.png)

注意这里的区别。当同步运行时，一切都按照我们期望的顺序发生。异步运行时，得到安慰的数字出现在正常 JavaScript 执行流程之外。

## (模拟简单网络)

**回调**

*   撕裂整个应用程序的接缝；需要时，某些引导可能无法完成。

**处理逻辑错误的问题。**

*   难以理解:嵌套、从不调用、重复调用、同步调用(阻塞)

简单网络，模拟 10 秒超时。

```
export class SimpleNetwork {
  _time = 10000;

  constructor() {};

  networkRequest = () => {
    setTimeout(() => {
      console.log(`Async Code after ${this._time}ms.`);
    }, this._time);
  };
  start = () => {
    console.log('The Start');
    this.networkRequest();
    console.log('The End');
  };
}; 
```

Enter fullscreen mode Exit fullscreen mode

在这段代码中，我们基本上模拟了一个网络请求；使用 setTimeout 提供 10 秒钟的延迟。我们应该看到网络请求在正常的 JavaScript 执行流程之外完成...

[![](img/001d19cb7c56fbfcace786eb7d317376.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--VLxJUIMD--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/hlqbjcsnt17ferzg5yma.png)

运行这段代码时，最后一行实际上会在十秒钟的延迟后显示出来。

## (模拟复杂网络)

复杂的网络，通过嵌套超时来模拟。

```
export class ComplexNetwork {
  _time = 0;

  constructor() {};

  first = () => {
    setTimeout(() => {
      console.log('2');
      this.second();
      console.log('4');
    }, this._time);
  };

  second = () => {
    setTimeout(() => {
      console.log('3');
    }, this._time);
  };

  start = () => {
    console.log('1');
    this.first();
    console.log('5');
  };
} 
```

Enter fullscreen mode Exit fullscreen mode

在检查这个模拟时，我们应该期望顺序应该是:1、5、2、4、3(因为 setTimeout /异步操作的顺序)...

[![](img/7bd2c8e19650e1969e7fecc2b84bfda2.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--cJF-kZWY--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/qdlysrbf7gkjw3w97xtu.png)

## ES2015 简单承诺

完成和错误事件处理控制反转问题。

**承诺信任**

1.  仅解决一次
2.  不是成功就是错误
3.  传递/保留的消息
4.  例外变成了错误
5.  一旦解决就不可改变

带有超时和承诺的简单异步。问题:

*   传递值
*   嵌套语法
*   处理失败...

也称为:承诺、未来、延期。

```
export class SimplePromise {
  _time = 0;

  constructor() {}

  timeout = () => {
    setTimeout(() => {
      console.log('setTimeout Fired');
    }, this._time);    
  };

  promise = () => {
    new Promise((resolve, reject) => {
      resolve('Resolved');
    })
    .then(res => console.log(res))
    .catch(err => console.log(err));    
  };

  start = () => {
    console.log('The Start');

    this.timeout();
    this.promise();

    console.log('The End');
  };
} 
```

Enter fullscreen mode Exit fullscreen mode

这里的代码 ***应该*** 触发‘开始’，然后触发超时和承诺功能，最后触发‘结束’。在这种情况下，操作顺序应该是“开始”,应该显示“结束”。由于 promise 立即被解析，并且没有任何东西将它带出正常的 JavaScript 执行流，因此它应该显示 next。最后，将显示超时功能。

[![](img/31b404b7c98a28a0426e8538c515c1d0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--VhlCIjPQ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/0kclrvxewi52sydqs87a.png)

## ES2015 复诺

具有超时和链式承诺的复杂异步。

*   模块化和可读，但有点不可靠。

```
export class ComplexPromise {
  _time = 0;

  constructor() {}

  timeout = () => {
    setTimeout(() => {
      console.log('setTimeout Fired');
    }, this._time);    
  };

  promise1 = () => {
    return new Promise((resolve, reject) => {
      resolve('Resolved 1');
    })
    .then(res => console.log(res))
    .catch(err => console.log(err));    
  };

  promise2 = () => {
    return new Promise((resolve, reject) => {
      resolve('Resolved 2');
    })
    .then(res => {
      console.log(res);
      this.promise3();
    })
    .catch(err => console.log(err));    
  };

  promise3 = () => {
    new Promise((resolve, reject) => {
      resolve('Resolved 3');
    })
    .then(res => console.log(res))
    .catch(err => console.log(err));    
  };

  start = () => {
    console.log('The Start');

    this.timeout();
    this.promise1();
    this.promise2();

    console.log('The End');
  };
}; 
```

Enter fullscreen mode Exit fullscreen mode

在这里，我们看到类似于简单的承诺。最大的区别是连锁承诺 2 和 3。这里，我们应该看到与简单承诺示例相同的情况，所有承诺都在超时运行之前完成...

[![](img/8c83875368ef37d6eee8569a0284a0f9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--IhcdmEHP--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/zbmchr78qq9ebweq68fw.png)

## 发电机节流

协作并发与抢先并发。

*   状态机的语法形式。
*   关于解决“推理”的问题。
*   允许非运行到完成行为。仅限局部阻塞。
*   生成器返回一个迭代器。

```
export function * throttle(func, time) {
  let timerID = null;
  function throttled(arg) {
    clearTimeout(timerID);
    timerID = setTimeout(func.bind(window, arg), time);
  }
  while(true) throttled(yield);
}

export class GeneratorThrottle {

  constructor() {};

  start = () => {
    thr = throttle(console.log, 3000);
    thr.next('');
  };
}; 
```

Enter fullscreen mode Exit fullscreen mode

这里，当发电机启动时，***【thr】***初始化，三秒后运行 ***console.log*** 。

[![](img/2acb96d1c74d9867fe7269eb8fbbac6a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--7oQo_xxc--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/yie7w0tsbgbnifupu501.png)

现在，我们可以看到在初始化之后，下一个函数被调用了三次...但只有一个控制台在三秒钟的窗口结束时开火。

## 用户交互

```
export class UserInteraction {

  constructor() {};

  dragStart = (event) => {
    event.dataTransfer.setData('text/plain', event.target.id);
    console.log('drag start', event);
  };

  dragOver = (event) => {
    event.preventDefault();
    event.dataTransfer.dropEffect = 'move';
    console.log({ x: event.pageX, y: event.pageY });
  };

  drop = (event) => {
    const id = event.dataTransfer.getData('text');
    console.log('drop', id);
    const element = document.getElementById('drag');
    event.target.appendChild(element);
  };
} 
```

Enter fullscreen mode Exit fullscreen mode

基本上，这段代码让我们看到拖放事件没有线程锁定 JavaScript。

## 事件监听器

事件监听器是同步的(不是异步的)

```
export class EventListeners {
  _btn = null;
  _time = 100;

  constructor() {};

  output = (content) => {
    console.log(content);
  };

  setupListeners = () => {
    this._btn.addEventListener('click', this.output.bind(null, 'Click Handler 1'));
    this._btn.addEventListener('click', this.output.bind(null,'Click Handler 2'));
  };

  triggerListeners = () => {
    setTimeout(() => {
      console.log('The Start');
      this._btn.click();
      console.log('The End');
    }, this._time);
  };

  start = () => {
    this._btn = document.getElementById('event-listener-link');
    this.setupListeners();
    this.triggerListeners();
  };
} 
```

Enter fullscreen mode Exit fullscreen mode

我们应该看到点击事件都按顺序触发...

[![](img/5b3264dc6a8b21756245a8a785e27c6a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--BxIbyQdL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/yc6cq949bb56f2l4ik4j.png)

## 网络工作者

**工人数量**

*   该数量因浏览器而异。最优好像是 20 左右。请参见(stack overflow on Number of Web Workers Limit)[[https://stack overflow . com/questions/13574158/Number-of-Web-Workers-Limit](https://stackoverflow.com/questions/13574158/number-of-web-workers-limit)。

这里是***crunch-numbers . js***，用作网络工作者...

```
onmessage = function() {
  for (let step = 0, len = 10; step <= len; step++) {
    postMessage(step * 10);
    const start = Date.now();
    while (Date.now() < start + 1000) {};
  }  
} 
```

Enter fullscreen mode Exit fullscreen mode

这是使用(或不使用)web worker 代码的代码...

```
export class WebWorkers {
  _worker = new Worker('scripts/presentation/crunch-numbers.js');
  _inlineProgress = null;
  _workerProgress = null;

  contructor() {};

  crunchNumbersInline = (callback) => {
    for (let step = 0, len = 10; step <= len; step++) {
      callback(step * 10);
      const start = Date.now();
      while (Date.now() < start + 1000) {};
    }
  };

  displayPercentInline = (percent) => {
    console.log(`inline percent: ${percent}`);
    this._inlineProgress.value = percent;
  };

  displayPercent = (message) => {
    console.log(`web-worker percent: ${message.data}`);
    this._workerProgress.value = message.data;
  }

  runSync = () => {
    this._inlineProgress = document.getElementById('inline-worker');
    this.crunchNumbersInline(this.displayPercentInline);
  };

  runAsync = () => {
    this._workerProgress = document.getElementById('web-worker');
    this._worker.postMessage('start');
    this._worker.onmessage = this.displayPercent;
  };

  start = (async = false) => {
    if (!async) {
      this.runSync();
    } else {
      this.runAsync();
    }
  };
} 
```

Enter fullscreen mode Exit fullscreen mode

如果没有相关的 HTML 页面运行，很难看到这里发生了什么。这表明内联进程被线程锁定，百分比显示什么也不做，直到时间到期，然后在一次“跳转”中显示 100%

在网络工作者的例子中，每 10%的增量都能正确显示，而 JavaScript 不会被线程锁定。

## 加载定时

原始内容(期望通过代码更改)
正确更改内容(通过代码)

它上面的显示是...

```
class LoadTiming {
  _time = 10000;

  constructor() {};

  loadSync = () => {
    const element = document.getElementById('first-timing');
    if (element) {
      element.innerHTML = 'Changed Content Correctly (via code)';
    }
  };

  loadAsync = () => {
    setTimeout(() => {
      const element = document.getElementById('second-timing');
      if (element) {
        element.innerHTML = 'Changed Content Correctly (via code)';
      }
    }, this._time);
  };

  start = () => {
    this.loadSync();
    this.loadAsync();
  };
}

const code11 = new LoadTiming();
code11.start(); 
```

Enter fullscreen mode Exit fullscreen mode

如您所见，上面的代码会立即加载同步和异步代码。由于这里的 JavaScript 加载在头部内容中，所以它在主体内容(DOM)就位之前运行，并且同步功能在 ***getElementById*** 上无声地失败。异步版本有足够的延迟来确保 DOM 准备就绪，并且可以更新代码中看到的内容。

## 设置超时计时器

在这段代码中，我们想看看 ***setTimeout*** 延迟实际有多长。

超时延迟有多长？

*   ***(慎用延时测试...【1000 次迭代)***

```
export class SetTimeoutTimer {
  _repetitions = 0;
  _totalRepetitions = 1000;
  _delay = 0;

  _totalActualDelay = 0;

  constructor() {};

  getActualDelay = () => {
    return this._totalActualDelay / this._totalRepetitions;
  };

  iterate = () => {
    let start = new Date();
    setTimeout(() => {
      this._totalActualDelay += new Date() - start;
      this.testDelay();
    }, this._delay);
  };

  testDelay = () => {
    if (this._repetitions++ > this._totalRepetitions) {
      console.log(`Requested Delay: ${this._delay}, Acual Average Delay: ${this.getActualDelay()}`);
      return;
    }
    this.iterate();
  };

  start = (delay = 0) => {
    this._delay = delay;
    this._repetitions = 0;
    this._totalActualDelay = 0;
    this.testDelay();
  };
} 
```

Enter fullscreen mode Exit fullscreen mode

这里的答案不是 42。*默认为 4ms。我在不同的机器和浏览器上看到了从 4 毫秒到大约 8 毫秒的变化...此外，正如您在这里看到的，它实际上不是一个整数(不会在 4 毫秒时触发，只是在 JavaScript 可以处理它的一段时间之后)。*

 *[![](img/49c5471354dc2c26e520ae54be1d8c77.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--71J4Pq_m--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/9p6is975o3opnuzwib5d.png)

## ES2017 异步/等待

*   扩展承诺的使用。
*   编写看上去和感觉上同步的异步代码。
*   清理语法，使其更具可读性。

```
export class AsyncAwait {
  _time = 2000;
  _resolve = true;
  _success = `Doing something here ... after ${this._time}ms.`;
  _fail = `Failed here ... after ${this._time}ms.`;

  constructor() {};

  asyncProcess = () => {
    return new Promise((resolve, reject) => {
      setTimeout(() => { (this._resolve === true) ? resolve(this._success) : reject(this._fail); }, this._time);
    });
  };

  asyncAwait = async () => {
    try {
      console.log(await this.asyncProcess());
    } catch (error) {
      console.log(error);
    }
  };

  start = (resolveState = true) => {
    this._resolve = resolveState;
    console.log('The Start');
    this.asyncAwait();
    console.log('The End');
  };
} 
```

Enter fullscreen mode Exit fullscreen mode

基本上，当这段代码启动时，它运行一个异步/等待版本的 promise。在一次谈话中，我实际上被问到它如何处理来自承诺的拒绝，我必须查找它(try/catch 块)。

下面是正确解析的异步/等待...

[![](img/654ab97e23a5cefffdde8ff271e810b7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--gEgjfosQ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/eh9jolevvxj4695clbsc.png)

...拒绝的代码也一样...

[![](img/a8e20cbe7caedfbced079ab77276b3a0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--CMNEPYyU--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/apsxharl6bwu8vukntbb.png)

## 总结

我们检查了:

*   使用回调同步和异步代码。...排除故障
*   ES2015 承诺链。
*   发电机(节流)
*   用户互动。
*   事件侦听器(同步)。
*   网络工作者。
*   装载时间。
*   ES2017 异步/等待。

## 结论

所有这些都来自于我几次关于 JavaScript 的同步和异步行为的演讲。实际谈话是半 ***现场编码*** 谈话。在演讲结束后，把实际的代码放在那里让人们玩，这让我很紧张，所以我写了这篇文章。

[单线程异步 JavaScript？](https://sessionize.com/s/bob-fornal/single-threaded_and_asynchronous_ja/22652)***