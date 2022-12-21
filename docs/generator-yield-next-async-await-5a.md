# JavaScript-Generator-Yield/Next VS . Async-Await

> 原文：<https://dev.to/dg92/generator-yield-next-async-await-5a>

# 发电机(ES6)-

根据用户要求，可以在不同的时间间隔返回多个值并可以管理其内部状态的函数是生成器函数。如果函数使用 function*语法，它将成为 GeneratorFunction。

它们不同于普通函数，普通函数在一次执行中运行完成，而生成器函数可以暂停和恢复，所以它们确实运行完成，但触发器仍在我们手中。它们允许对异步功能进行更好的执行控制，但这并不意味着它们不能用作同步功能。

注意:当一个生成器函数被执行时，它返回一个新的生成器对象。

使用 yield&next 完成暂停和恢复。所以让我们看看它们是什么，它们是做什么的。

# 收益率/下-

yield 关键字暂停生成器函数的执行，yield 关键字后面的表达式的值返回给生成器的调用方。可以认为它是 return 关键字的基于生成器的版本。

yield 关键字实际上返回一个具有两个属性的 IteratorResult 对象，即 value 和 done。(不知道什么是迭代器和可迭代对象然后在这里阅读)。

一旦在 yield 表达式上暂停，生成器的代码执行将保持暂停状态，直到调用生成器的 next()方法。每次调用生成器的 next()方法时，生成器都会恢复执行并返回迭代器结果。

唷..理论讲够了，我们来看一个例子

```
function* UUIDGenerator() {
    let d, r;
    while(true) {
        yield 'xxxxxxxx-xxxx-4xxx-yxxx-xxxxxxxxxxxx'.replace(/[xy]/g, function(c) {
            r = (new Date().getTime() + Math.random()*16)%16 | 0;
            d = Math.floor(d/16);
            return (c=='x' ? r : (r&0x3|0x8)).toString(16);
        });
    }
}; 
```

这里，UUIDGenerator 是一个生成器函数，它使用当前时间(一个随机数)计算 UUID，并在每次执行时返回一个新的 UUID。

要运行上面的函数，我们需要创建一个生成器对象，我们可以在其上调用 next()。

```
const UUID = UUIDGenerator();
// UUID is our generator object
UUID.next() 
// return {value: 'e35834ae-8694-4e16-8352-6d2368b3ccbf', done: false} 
```

UUID.next()这将返回每个 UUID 上的新 UUID。next()under value 键和 done 将始终为 false，因为我们处于无限循环中。

注意:我们暂停在无限循环之上，这很酷，在生成器函数的任何“停止点”,它们不仅可以向外部函数产生值，还可以从外部接收值。

上面有很多生成器的实际实现，也有很多大量使用它的库，co、koa 和 redux-saga 就是一些例子。

# 异步/等待(ES7)

[![Alt async-await](img/8d039e0bbce94899aed095d97d647462.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--u7xux-lD--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://miro.medium.com/max/1400/0%2ALAkE4GiZATgtseM5)

传统上，当异步操作返回使用 Promise 处理的数据时，会传递和调用回调。

**Async/Await** 是一种特殊的语法，以一种更舒适的方式处理承诺，非常容易理解和使用。
Async 关键字用于定义一个异步函数，返回一个 AsyncFunction 对象。

**Await** 关键字用于暂停异步函数的执行，直到承诺被履行，即被解决或拒绝，并在履行后恢复异步函数的执行。当恢复时，await 表达式的值就是已履行承诺的值。

# 要点:

1.  Await 只能在异步函数中使用。
2.  带有 async 关键字的函数将总是返回一个承诺。
3.  在同一个函数下，多个等待将总是按顺序运行。
4.  如果承诺正常解析，那么 wait promise 返回结果。但是在拒绝的情况下，它抛出错误，就像在那一行有 throw 语句一样。
5.  异步函数不能同时等待多个承诺。
6.  如果一条语句不依赖于前一条语句而多次使用 await，就会出现性能问题。

到目前为止一切顺利，现在让我们来看一个简单的例子:-

```
async function asyncFunction() {

  const promise = new Promise((resolve, reject) => {
    setTimeout(() => resolve("i am resolved!"), 1000)
  });

  const result = await promise; 
  // wait till the promise resolves (*)

  console.log(result); // "i am resolved!"
}

asyncFunction(); 
```

asyncFunction 执行在 wait promise 行“暂停”,并在 promise 结束时继续执行，结果成为它的结果。所以上面的代码显示“我决心！”一秒钟后。

# 生成器和异步等待—比较

1.  生成器函数/yield 和异步函数/await 都可以用来编写“等待”的异步代码，这意味着代码看起来好像是同步的，即使它实际上是异步的。

2.  生成器函数是一个 yield 接一个 yield 地执行的，即迭代器(下一个方法)一次执行一个 yield 表达式，而 Async-await 是一个 await 接一个 await 地顺序执行的。

3.  Async/await 使得实现生成器的特定用例更加容易。

4.  生成器的返回值总是{value: X，done: Boolean}，而对于异步函数，它总是一个承诺，要么解析为值 X，要么抛出一个错误。

5.  一个异步函数可以被分解成生成器和 promise 实现，这是很好的了解材料。

如果你想加入我的电子邮件列表，请考虑在这里输入你的电子邮件地址，并在 dev.to 上关注我，阅读更多关于 javascript 的文章，在 [GitHub](https://github.com/dg92) 上查看我的疯狂代码。

如果有什么不清楚或者你想指出什么，请在下面评论。

谢谢:)