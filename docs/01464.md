# 使用异步/等待进行轮询

> 原文：<https://dev.to/jakubkoci/polling-with-async-await-25p4>

这篇文章是基于我用轮询功能重构一小段代码的真实经验，所以我不会从头开始。我知道这不是火箭科学，但我过去一直在寻找投票的解决方案，我发现类似的文章非常有帮助。这也很好地展示了`async/await`和高阶函数如何有助于代码的可维护性和可读性。

一段时间以来，我一直在使用下面这段代码来实现一些轮询功能。

```
function originalPoll(fn, end) {
  async function checkCondition() {
    const result = await fn();
    console.log("result", result);
    if (result < 3) {
      setTimeout(checkCondition, 3000);
    } else {
      end();
    }
  }

  checkCondition();
} 
```

它采用函数`fn`并每 3 秒调用一次，直到得到所需的结果(我在这里将其简化为条件`result < 3`，然后它调用作为第二个参数传递的回调函数`end`。该函数以某种方式工作，并做我需要的。但是，不可能在不同的条件下重复使用它。所以我决定稍微改造一下。经过几分钟的思考和调整，我最终完成了这个简化:

```
async function poll(fn, fnCondition, ms) {
  let result = await fn();
  while (fnCondition(result)) {
    await wait(ms);
    result = await fn();
  }
  return result;
}

function wait(ms = 1000) {
  return new Promise(resolve => {
    console.log(`waiting ${ms} ms...`);
    setTimeout(resolve, ms);
  });
} 
```

这个函数仍然重复调用`fn`函数，但是现在它也接受另一个参数`fnCondition`，这个参数是通过调用`fn`函数的结果来调用的。函数`poll`将调用函数`fn`，直到函数`fnCondition`返回`false`。我还提取了`setTimeout`函数，它提高了轮询函数的可读性，并保持了它的简单明了(我不关心等待在这个抽象层次上是如何实现的)。我们还去掉了函数内部的函数，这只是增加了不必要的复杂性。

老实说，我并不是从测试开始的。无论如何，我仍然想检查我的设计，为未来的重构提供一些安全性，并且记录如何调用`poll`函数。我们可以通过添加一些测试来很好地实现所有这些:

```
describe("poll", () => {
  it("returns result of the last call", async () => {
    const fn = createApiStub();
    const fnCondition = result => result < 3;
    const finalResult = await poll(fn, fnCondition, 1000);
    expect(finalResult).toEqual(3);
  });

  it("calls api many times while condition is satisfied", async () => {
    const fn = createApiStub();
    const fnCondition = result => result < 3;
    await poll(fn, fnCondition, 1000);
    expect(fn).toHaveBeenCalledTimes(3);
  });

  function createApiStub() {
    let counter = 0;
    const testApi = () => {
      console.log("calling api", counter);
      counter++;
      return counter;
    };
    return jest.fn(() => testApi());
  }
}); 
```

我使用 Jest 作为测试库。它为测试异步函数提供了强大的支持，并在默认情况下提供了带有断言的存根。函数`createApiStub`在这里只是为了测试和断言，它将代表我们真正的 API 调用或者我们想要轮询的任何函数。

你可以在这个 CodeSandbox 中找到并运行代码:[用 async/await - CodeSandbox](https://codesandbox.io/s/ovqop57mzy) 轮询。