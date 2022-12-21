# 自制 React 钩子:useAsyncEffect 或如何用 useEffect 处理异步操作

> 原文：<https://dev.to/n1ru4l/homebrew-react-hooks-useasynceffect-or-how-to-handle-async-operations-with-useeffect-1fa8>

# TLDR

异步函数缺乏可取消性。我们可以使用生成器函数来模仿可取消的异步函数。我为编写异步效果创建了一个库: [`useAsyncEffect on Github`](https://github.com/n1ru4l/use-async-effect)

# 问题

我们大多数人喜欢使用 async-await 语法！

你们中的一些人(包括我)可能尝试过执行下面这段代码

```
import { useState, useEffect } from "react";

const [state, setState] = useState()
// do not try this at home
useEffect(async () => {
  const data = await fetchSomeData()
  setState(data);
}, []); 
```

Enter fullscreen mode Exit fullscreen mode

这样做的人可能还注意到这段代码会在开发人员控制台中打印一条很大的错误消息:

```
Warning: An Effect function must not return anything besides a function, which is used for clean-up.

It looks like you wrote useEffect(async () => ...) or returned a Promise. Instead, you may write an async function separately and then call it from inside the effect:

async function fetchComment(commentId) {
  // You can await here
}

useEffect(() => {
  fetchComment(commentId);
}, [commentId]);

In the future, React will provide a more idiomatic solution for data fetching that doesn't involve writing effects manually. 
```

Enter fullscreen mode Exit fullscreen mode

# 为什么`useEffect`不接受我的异步函数？

错误消息实际上给出了清楚的解释😅。我们来分解一下！

1.  异步函数总是返回一个`Promise`，因此你不能同步返回一个清理函数。

2.  当`useEffect`的一个依赖关系改变或者组件卸载时，React 调用清理函数。

即使`useEffect`支持解决承诺中的清理功能，这种变化也可能在`Promise`解决(或者更糟，被拒绝)之前发生。结果，清理函数要么调用得太晚，要么永远不会被调用。

## 为什么我甚至需要一个清理功能？

> ![Dan Abramov profile image](img/17c386e6839df2dd490f297a2c139b1e.png)丹阿布拉莫夫[@丹 _ 阿布拉莫夫](https://dev.to/dan_abramov)![twitter logo](img/65e26e35707d96169ec8af6b3cbf2003.png)有没有想过为什么不能把异步函数直接作为 useEffect 实参，而非要放在里面？这不是技术上的限制。它有助于引导您找到能够很好地处理竞争条件的代码。[twitter.com/dan_abramov/st…](https://t.co/Y94AxDCGcu)20:32PM-04 2019 年 7 月丹·阿布拉莫夫[@丹 _ 阿布拉莫夫](https://dev.to/dan_abramov)[@ thepaulcbride](https://dev.to/thepaulmcbride)[@ chrislaughlin](https://dev.to/chrislaughlin)虽然有充分的理由，如果您的效果函数是“异步”的，那么您就没有机会忽略陈旧的获取结果(或者取消获取)🙂https://t.co/N8SE6BkbmC[![Twitter reply action](img/5d5a32424597af8488f7190c7d7d496b.png)](https://twitter.com/intent/tweet?in_reply_to=1146879490347470848)[![Twitter retweet action](img/3d12d4a909b79beaf8d81b6491fd052d.png)](https://twitter.com/intent/retweet?tweet_id=1146879490347470848)[![Twitter like action](img/3f89df2f36e8e5624d2a25952b3ac8b8.png)](https://twitter.com/intent/like?tweet_id=1146879490347470848)

给定这个有效的 react `useEffect`用法:

```
const [data, setData] = useState();
useEffect(() => {
  const runEffect = async () => {
    const data = await fetchSomeData(filter);
    setData(data);
  };
  runEffect();
}, [setData, filter]); 
```

Enter fullscreen mode Exit fullscreen mode

让我们假设组件在`fetchSomeData`承诺仍未解决时卸载。这意味着尽管组件已经被卸载，但仍调用了`setData`。

你可能还记得来自类组件的`Can't call setState (or forceUpdate) on an unmounted component.`警告，这仍然适用于钩子。

更糟糕的是，当过滤器依赖性在`fetchSomeData`解决之前改变时，我们有两个竞争条件冲突。如果由于某种原因，第二个`fetchSomeData`承诺在第一个`fetchSomeData`承诺之前解决了呢？在这种情况下，一旦延迟的承诺得到解决,“新”数据将被“旧”数据覆盖😲。

我们究竟如何防止这类问题？

## 异步-Await 并不完美

在理想的情况下，我们不必关心这些事情，但不幸的是，取消异步函数是不可能的。这意味着我们必须在每次异步操作(`Promise`)后检查当前的`useEffect`周期是否已经结束。

```
const [data, setData] = useState();
useEffect(() => {
  let cancel = false;
  const runEffect = async () => {
    const data = await fetchSomeData(filter);
    if (cancel) {
      return;
    }
    setData(data);
  };
  runEffect();

  // Cleanup function that will be called on
  // 1\. Unmount
  // 2\. Dependency Array Change
  return () => {
    cancel = true;
  }
}, [setData, filter]); 
```

Enter fullscreen mode Exit fullscreen mode

在一个异步函数中，这可能会变得非常乏味，因为它会依次执行许多等待:

```
const [data1, setData1] = useState();
const [data2, setData2] = useState();
const [data3, setData3] = useState();
useEffect(() => {
  let cancel = false;

  const runEffect = async () => {
    const data1 = await fetchSomeData(filter);
    if (cancel) {
      return;
    }
    setData1(data);

    const data2 = await fetch(data1.url);
    if (cancel) {
      return;
    }
    setData2(data);

    const data3 = await fetch(data2.url);
    if (cancel) {
      return;
    }
    setData3(data);
  };
  runEffect();

  // Cleanup function that will be called on
  // 1\. Unmount
  // 2\. Dependency Array Change
  return () => {
    cancel = true;
  }
}, [setData1, setData2, setData3, filter]); 
```

Enter fullscreen mode Exit fullscreen mode

这是我们可以确保在调用清理函数后不调用`setState`的唯一方法，尽管如此，异步操作也就是网络请求(通过`fetch`发起)仍在执行。

# 取消挂起的异步操作

现代浏览器附带了一个名为`AbortController`的新 API，可以用来中止未决的`fetch`请求。

```
const [data, setData] = useState();
useEffect(() => {
  const controller = new AbortController();
  const runEffect = async () => {
    try {
      const data = await fetch(
        "https://foo.bars/api?filter=" + filter,
        { signal: controller.signal }
      );
      setData(data);
    } catch (err) {
      if (err.name === 'AbortError') {
        console.log("Request was canceled via controller.abort");
        return;
      }
      // handle other errors here
    }
  };
  runEffect();

  return () => {
    controller.abort();
  }
}, [setData, filter]); 
```

Enter fullscreen mode Exit fullscreen mode

现在，每当过滤器改变或组件更新时，挂起的网络请求被中止。取数据`Promise`将拒绝，而不是解析，并出现错误👌。

你可以在这里了解浏览器对`AbortController`的支持(当然 IE 不支持`AbortController`😖):【https://caniuse.com/#feat=abortcontroller T2】

[有一个多填充物可用](https://www.npmjs.com/package/abortcontroller-polyfill)。它实际上并不实现取消，因为它必须在浏览器中本地完成。相反，它通过在 fetch 调用被解析/拒绝后抛出中止错误来模拟行为。

此外，这个解决方案只适用于 fetch 调用😕。有些 API 提供了取消异步操作的方法，有些则没有。

例如，这就是今天你如何取消用`useEffect`钩子加载`Image`:

```
export const loadImage = src => {
  const image = new Image();
  const done = false;

  const cancel = () => {
    if (done) {
      // do not change the image instance once it has been loaded
      return;
    }
    // this will abort the request and trigger the error event
    image.src = "";
  };

  const promise = new Promise((resolve, reject) => {
    image.src = src;
    const removeEventListeners = () => {
      image.removeEventListener("load", loadListener);
      image.removeEventListener("error", errorListener);
    };
    const loadListener = () => {
      removeEventListeners();
      done = true;
      resolve(image);
    };
    const errorListener = err => {
      removeEventListeners();
      reject(err);
    };
    image.addEventListener("load", loadListener);
    image.addEventListener("error", errorListener);
  });

  return { promise, cancel };
};

useEffect(() => {
  const task = loadImage(url)
  const runEffect = async () => {
    try {
      const image = await task.promise;
      // do sth with image
    } catch (err) {
      // handle cancel error
    }

  };
  runEffect();

  return () => {
    task.cancel();
  }
}, [url]) 
```

Enter fullscreen mode Exit fullscreen mode

在使用其他不可撤销的异步 API 的环境中，您仍然需要设置和检查一个布尔变量。

希望有一天所有基于异步的 API 都支持使用`AbortController`。

现在，我们必须处理布尔检查和尝试捕捉的混合。

但是，如果我们可以对取消请求和在一个`await`关键字后停止函数执行进行一些抽象，那会怎么样呢？

# 介绍`useAsyncEffect`

你以前听说过生成器函数吗？

```
const generator = function *() {
  yield "bars";
  yield "foo";
  return "fizz"
} 
```

Enter fullscreen mode Exit fullscreen mode

生成函数是一个可暂停函数。`yield`关键字表示功能暂停。让我们运行这个发电机！

```
// create instance of generator
const instance = generator();
// call next to run the generator until the next yield keyword
let result = instance.next();
console.log(result); // {value: "bars", done: false}
// continue calling
result = instance.next();
console.log(result); // {value: "foo", done: false}
// we can continue calling next until done is true
result = instance.next();
console.log(result); // {value: "fizz", done: true} 
```

Enter fullscreen mode Exit fullscreen mode

除了将值从生成器中传递出去，我们还可以将值作为`next`方法的参数传递进来:

```
const generator = function *() {
  const echo = yield "hello";
  console.log(echo);
}

// create instance of generator
const instance = generator();
let result = instance.next();
console.log(result); // {value: "hello", done: false}
// pass string into generator that will be assigned to the echo variable
instance.next("hello generator"); 
```

Enter fullscreen mode Exit fullscreen mode

这太酷了！但是这如何帮助我们解决异步等待问题呢？

## 过去，生成器被用来模拟异步等待行为

自 [ECMAScript 2015(第六版，ECMA-262)](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/function*#Specifications) 以来，发电机就一直存在

直到 [ECMAScript 2017 (ECMA-262)](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/async_function#Specifications) ，异步函数才成为规范的一部分

在 EcmaScript 2015 年到 2017 年期间，出现了各种用生成器模仿 async-await 行为的库。

其中最受欢迎的是 [`co`](https://www.npmjs.com/package/co)

```
import co from 'co';

// wrap generator into function that returns a promise
const asyncFunction = co.wrap(function * () {
  const result = yield fetch(url);
  console.log(result);
  return 1
});

asyncFunction().then((res) => {
  assert.equal(res, 1);
}) 
```

Enter fullscreen mode Exit fullscreen mode

`Co`基本上运行发电机，直到承诺被`yield` -ed，然后等待承诺解析，并继续以解析的承诺值(`get.next(resolvedPromiseValue)`)运行发电机，直到发电机完成(`gen.next(resolvedPromiseValue).done === true`)。

区分 async-await 和 generators 的一点(除了它们的语法)是，在暂停了之后，**生成器不会被强制解析`Promise`或者甚至继续执行生成器函数。**

这基本上意味着我们可以将生成器用作“可取消的”异步等待。

## 咱们建那个`useAsyncEffect`钩子

### 实现

```
import { useEffect } from "react";

const noop = () => {}

const useAsyncEffect = (generator, deps = []) => {
  // store latest generator reference
  const generatorRef = useRef(generator);
  generatorRef.current = generator;

  useEffect(() => {
    let ignore = false;
    let onCancel = noop;

    const runGenerator = async () => {
      // create generator instance
      const instance = generatorRef.current(_onCancel => {
        // allow specifying a onCancel handler
        // that can be used for aborting async operations
        // e.g. with AbortController
        // or simple side effects like logging
        // For usage: see example below
        onCancel = _onCancel || noop;
      });

      // generator result
      let res = { value: undefined, done: false };
      do {
        res = instance.next(res.value);
        try {
          // resolve promise
          res.value = await res.value;
        } catch (err) {
          try {
            // generator also allow triggering a throw
            // instance.throw will throw if there is no
            // try/catch block inside the generator function
            res = instance.throw(err);
          } catch (err) {
            // in case there is no try catch around the yield
            // inside the generator function
            // we propagate the error to the console
            console.error("Unhandeled Error in useAsyncEffect: ", err);
          }
        }

        // abort further generator invocation on
        // 1\. Unmount
        // 2\. Dependency Array Change
        if (ignore) {
          return;
        }
      } while (res.done === false);
    };
    runGenerator();

    // Cleanup function that will be called on
    // 1\. Unmount
    // 2\. Dependency Array Change
    return () => {
      ignore = true;
      onCancel();
    };
  }, deps);
}; 
```

Enter fullscreen mode Exit fullscreen mode

### 用法

```
const [data, setData] = useState();
useAsyncEffect(function * (onCancel) {
  const controller = new AbortController();

  // handle error 
  onCancel(() => {
    console.log("cancel while fetch is still executed, use controller for aborting the request.");
    controller.abort();
  });
  try {
    const data = yield fetch(
      "https://foo.bars/api?filter=" + filter,
      { signal: controller.signal }
    )
    setData(data);
  } catch (err) {
    if (err.name === 'AbortError') {
      console.log("Request was canceled via controller.abort")
      // we know that an 'AbortError' occurs when the request is
      // cancelled this means that the next promise returned by yield
      // will be created but not actively used, thus, we return in
      // order to avoid the promise being created.
      return;
    }
  }

  // set new cancel handler
  onCancel(() => {
    console.log("cancel while doSthAsyncThatIsNotCancelable is still being executed");
  });
  const newData = yield doSthAsyncThatIsNotCancelable();
  setData(newData);

  // all our async operations have finished
  // we do not need to react to anything on unmount/dependency change anymore
  onCancel(() => {
    console.log("everything ok");
  })
}, [setData, filter]); 
```

Enter fullscreen mode Exit fullscreen mode

这个钩子现在允许我们省略组件中所有的布尔检查(`ignore === true`)，同时仍然给我们取消异步操作(可取消的)的权力，或者通过向`onCancel`注册一个处理函数来处理其他副作用。

我希望你喜欢读这篇文章！

你以前用过发电机吗？今天你如何处理与`useEffect`的异步操作？你会在代码中使用`useAsyncEffect`钩子吗？你有任何反馈或发现了一个错误吗？

大家在评论里讨论吧！

此外，请在这些平台上随时关注我，如果你喜欢这篇文章，我保证你会看到更多精彩的内容。我写的是 JavaScript、Node、React 和 GraphQL。

*   [开发到](https://dev.to/n1ru4l)
*   [中等](https://medium.com/@n1ru4l)
*   [推特](https://twitter.com/n1rual)
*   [Github](https://github.com/n1ru4l)

祝你有一个美好而富有成效的一天！