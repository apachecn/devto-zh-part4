# 不到 5 分钟的异步 JavaScript

> 原文：<https://dev.to/atlassian/asynchronous-javascript-in-under-5-minutes-5dbg>

JavaScript 利用回调、承诺、异步和等待特性来支持[异步编程](https://developer.mozilla.org/en-US/docs/Learn/JavaScript/Asynchronous/Concepts)。我们不会深入每个主题的太多细节，但这篇文章应该是一个温和的介绍，让你开始。让我们开始吧！

## 示例设置

看看这个简单的例子。我们有一个预填充数字的初始数组，“getNumbers”函数循环遍历数组并输出数组中的每一项，而“addNumber”函数接收一个数字并将其添加到数组中。

```
const numbers = [1, 2];

function getNumbers() {
  numbers.forEach(number => console.log(number))
}

function addNumber(number) {
  numbers.push(number);
}

getNumbers(numbers) // 1, 2
addNumber(3);
getNumbers(numbers) // 1, 2, 3 
```

## 问题

现在，让我们假设我们的两个函数调用都需要一些时间来执行，因为我们正在向后端服务器发出请求。让我们通过使用内置的 setTimeout 方法来模拟它，并将我们的逻辑封装在其中。

```
const numbers = [1, 2];

function getNumbers() {
  setTimeout(() => {
    numbers.forEach(number => console.log(number))
  }, 1000)
}

function addNumber(number) {
  setTimeout(() => {
  numbers.push(number)
  }, 2000)
}

getNumbers(numbers) // 1, 2
addNumber(3)
getNumbers(numbers) // 1, 2 ... Why? 
```

现在看一下控制台。它的行为和以前不一样了。这是因为“addNumber”函数需要 2 秒钟运行，而“getNumbers”函数需要 1 秒钟运行。因此，“addNumber”函数在我们的两个“getNumbers”被调用后执行。“addNumber(3)”函数调用不会等待它的上一行结束。

## 回调

在这种情况下，逐行调用异步调用是行不通的。有没有其他方法可以确保一个函数只在另一个函数完成执行之后才执行？复试可以帮助我们！在 javascript 中，函数可以作为参数传递。因此，我们可以将“getNumbers”函数传递给 addNumber 函数，并在添加一个数字后执行它。

```
const numbers = [1, 2];

function getNumbers() {
  setTimeout(() => {
    numbers.forEach(number => console.log(number))
  }, 1000)
}

function addNumber(number, callback) {
  setTimeout(() => {
  numbers.push(number)
  callback();
  }, 2000)
}

getNumbers(numbers) // 1, 2
addNumber(3, getNumbers) // 1, 2, 3 
```

这是我们代码库的流程。1 秒钟后调用“getNumbers”。2 秒后调用“addNumbers”(在“getNumbers”后 1 秒)。在将数字推送到数组后，它再次调用“getNumbers ”,这需要额外的 1 秒钟。程序在 3 秒钟后完全终止。为了了解更多关于回调的知识，我之前写了一篇[文章](https://dev.to/shimphillip/call-me-maybe-callbacks-for-beginners-k60)深入探讨。

## 承诺

下面是相同代码的重写。我们将不再使用回调并直接调用它，所以让我们修改我们的“addNumber”函数，不再接受第二个参数。相反，它会立即使用`new Promise()`关键字返回一个承诺。承诺可以使用 resolve 和 reject，这是在你采取某些行动后可以调用的论点。如果一切顺利，可以调用 resolve()。

```
const numbers = [1, 2];

function getNumbers() {
  setTimeout(() => {
    numbers.forEach(number => console.log(number))
  }, 1000)
}

function addNumber(number) {
  return new Promise((resolve, reject) => {
    setTimeout(() => {
      numbers.push(number);
      resolve();
    }, 2000)
  });
}

addNumber(3).then(getNumbers) // 1, 2, 3 after 3 seconds 
```

当承诺实际返回时，我们可以通过使用`then`关键字来链接它。然后，您可以传入一个函数定义，在您的承诺完成后调用它！厉害！但是，如果出现网络超时之类的错误呢？我们可以使用 reject 关键字来表示某个操作不成功。让我们手动拒绝它。

```
const numbers = [1, 2];

function getNumbers() {
  setTimeout(() => {
    numbers.forEach(number => console.log(number))
  }, 1000)
}

function addNumber(number) {
  return new Promise((resolve, reject) => {
    setTimeout(() => {
      numbers.push(number);
      const isAdded = false;
      if (isAdded) {
        resolve();
      } else {
        reject("There was an error")
      }
    }, 2000)
  });
}

addNumber(3).then(getNumbers).catch((e) => console.log(e)) // There was an error 
```

注意，我们可以传入一个字符串，这个字符串是通过使用`.catch`捕获的，并且可以通过它的第一个参数获得。我们也可以用 resolve 方法做同样的事情，传入一些数据并在`then()`方法中接收它。

## 异步&等待

让我们使用相同的代码并使用 async 和 await！这里剧透一下！我们仍然需要回报承诺，但是我们处理的方式不同了。看一看。

```
const numbers = [1, 2];

function getNumbers() {
  setTimeout(() => {
    numbers.forEach(number => console.log(number))
  }, 1000)
}

function addNumber(number) {
  return new Promise((resolve, reject) => {
    setTimeout(() => {
      numbers.push(number);
      const isAdded = true;
      if (isAdded) {
        resolve();
      } else {
        reject("There was an error")
      }
    }, 2000)
  });
}

async function initialize() {
  await addNumber(3);
  getNumbers();
}

initialize(); // 1, 2, 3 
```

我们没有将 then 和 catch 链接到 addNumber 调用，而是创建了一个名为 initialize 的函数。使用“await”关键字要求其包装函数在前面加上“async”关键字。此外，await 关键字使我们的代码更直观，因为我们的代码现在逐行读取，即使它是异步的！

那么，错误处理呢？

```
const numbers = [1, 2];

function getNumbers() {
  setTimeout(() => {
    numbers.forEach(number => console.log(number))
  }, 1000)
}

function addNumber(number) {
  return new Promise((resolve, reject) => {
    setTimeout(() => {
      numbers.push(number);
      const isAdded = false;
      if (isAdded) {
        resolve();
      } else {
        reject("There was an error")
      }
    }, 2000)
  });
}

async function initialize() {
  try {
    await addNumber(3);
    getNumbers();
  } catch (e) {
    console.log(e);
  }
}

initialize(); // There was an error 
```

让我们在初始化函数中使用 try 和 catch。如果一个承诺被拒绝，我们的 catch 块将运行。

## 总结

我们学习了一些不同的方法来处理不同的异步 JavaScript。对我来说，我个人更喜欢异步编写，并且一直在等待编写和思考它是多么容易。但是其他的有它们的位置，特别是回调，因为一些 API 只支持它们。感谢您的阅读，让我们用新学到的知识写一些严肃的代码吧！

这段代码的灵感来自布拉德·特拉弗斯的 youtube 视频。