# 我如何使用 JavaScript 承诺

> 原文：<https://dev.to/shubho/how-i-use-javascript-promises-19c6>

*最初发表于 [Shubho。dev](https://www.shubho.dev/coding/how-i-use-javascript-promises/)T3】*

JavaScript 中的异步编程对我来说很可怕。我唯一满意的异步范式是 jQuery 的`$.ajax`。然而，在过去的 8 年里，我一直使用纯 JavaScript，当我开始使用 NodeJS 时，我不得不学习承诺。我对像蓝鸟这样的第三方库涉猎不多。我对天生的承诺最有经验。

我对`Promise`(或者一般的异步范式)的主要问题是，当我想在 Promise 语句开始后执行语句时。人们花了一段时间才意识到，承诺一旦兑现，就无法取消。另一个问题是承诺链。这是一个踢球的人。我早期的承诺函数看起来总是像回调地狱的朋友。经过这么多年的努力和几个大项目，我可以有把握地说我喜欢承诺。即使 async/await 是新的时尚，我仍然喜欢承诺。

所以这里是我如何使用承诺使我的编码生活更简单。

## 创建诺言骨架

每当我创建一个返回承诺的新函数时，我首先创建框架。由于该函数只能返回基于承诺的值，所以我总是将该函数的所有语句包装在承诺中。

```
function sample() {
    return new Promise(function(resolve, reject) {
        // The function body
    });
} 
```

Enter fullscreen mode Exit fullscreen mode

上面的示例函数将其整个语句包装在承诺中，并立即返回。你可以`resolve()`或`reject()`你想要的身体输出。这样，我就不会犯不履行承诺的错误。它也帮助我创造承诺链。每当在一个链中，我意识到我需要一个新的函数，我就创建一个适当的框架名称并完成主链。然后我一个一个的回来，完成单个的功能。

## 承诺锁链-分记

承诺链是棘手的。如果我们不小心，我们可以有一个新类型的回调地狱。
一个例子

```
function promiseCallback() {
    return new Promise((resolve, reject) => {
        aNewFunction()
            .then((values) => {
                someOtherFunction(values)
                    .then((someOtherValue) => {
                        // Do something
                        resolve(someOtherValue);
                    })
                    .catch((err1) => {
                        // Error in inner function
                        reject(err1);
                    });
            })
            .catch((err) => {
                // Error in outer function
                reject(err);
            });
    });
} 
```

Enter fullscreen mode Exit fullscreen mode

在上面的示例中，`aFunction()`和`someOtherFunction()`是两个返回承诺的函数。如果你仔细观察，序列看起来像一个回调地狱。内部的然后抓住链条，外部的是独立的。我们不能在一个公共的 catch 块中处理错误，我们需要注意内部函数总是在它们的外部`then()`中的最后一行，否则我们不能控制执行流。

一个更好的方式与链:

```
function promiseCallback() {
    return new Promise((resolve, reject) => {
        aNewFunction()
            .then((values) => {
                return someOtherFunction(values);
            })
            .then((someOtherValue) => {
                // Do something
                resolve(someOtherValue);
            })
            .catch((err) => {
                // Error in outer function
                reject(err);
            });
    });
} 
```

Enter fullscreen mode Exit fullscreen mode

`then`链中的退货只能有三种类型:

1.  **承诺**——链中的`then`函数可以返回承诺。它的结果被传递给下一个`then`。
2.  **标量值** -链中的`then`函数可以返回值，如字符串或数字。该值按原样传递给下一个`then`，链可以继续。
3.  **抛出**—`then`函数可以`throw`一个错误，将执行移到 catch 块。

只要你在`then`内的所有回报都符合上述三种类型，你就不应该在你的承诺链中出现问题。

**注意**
切记始终将`resolve()`或`reject()`放在链条的最后一个`then`或`catch`中。

## 何时创建新的承诺函数

在一个承诺链中，如果有多个 if-else 条件，并且每个条件可以导致不同的承诺结果，这是创建一个返回承诺的新函数的绝佳时机。这样，Promise 链返回一个调用新函数的语句。

## 一步处理标量值或承诺函数

假设我们有一个函数，它使用学生的点名号码来获取学生的分数。但是，该函数要么接受一个编号作为输入，要么接受学生的姓名作为输入。只能使用辊号从 DB 获得标记。下面是一些伪代码。

```
function getMarks(obj) {
    let rollNumberPromise = null;
    if ('rollNumber' in obj) {
        rollNumberPromise = Promise.resolve(obj.rollNumber);
    } else if ('studentName' in obj) {
        rollNumberPromise = getRollNumberFromName(obj.studentName);
    }

    if (!rollNumberPromise) {
        reject('Nothing worked');
    }

    rollNumberPromise
        .then((rollNumber) => {
            return get_marks_from_db(rollNumber);
        })
        .then((marks) => {
            resolve(marks);
        })
        .catch((err) => {
            reject(err);
        });
}

function getRollNumberFromName(studentName) {
    return new Promise(function(resolve, reject) {
        fn_to_get_roll_number_from_db(studentName)
            .then((rollNumber) => {
                resolve(rollNumber);
            })
            .catch((err) => {
                reject(err);
            });
    });
}

function fn_to_get_roll_number_from_db(studentName) {
    return new Promise(function(resolve, reject) {
        // some code
    });
}

function get_marks_from_db(rollNumber) {
    return new Promise(function(resolve, reject) {
        // some code
    });
} 
```

Enter fullscreen mode Exit fullscreen mode

`getMarks(obj)`取一个对象作为输入。我们创建一个局部变量`rollNumberPromise`。如果 rollNumber 已经存在，我们使用`Promise.resolve()`将该值保存在变量中。这将创建一个承诺，当用值调用时，该承诺将被解析。如果发送了学生的名字，那么我们将对函数`getRollNumberFromName(studentName)`的调用保存到本地变量中。调用`rollNumberPromise.then()`会返回一个 rollNumber，不管它是从数据库接收的还是作为输入直接发送给函数的。以这种方式使用它可以确保`getMarks()`有一个单一的承诺链，而不是基于传递的输入是数字还是名字的 if-else 条件。

## 最后唤起一个承诺

如前所述，一旦承诺，一旦援引，就不能取消。任何不依赖于 Promise 输出并且无需异步调用就可以独立执行的语句都应该在函数中启动 Promise 链之前完成。一旦承诺链开始，任何后续步骤都必须在`then`链内。唯一的例外是，当您不关心承诺值，并且希望承诺在后台执行，而您的主要功能保持运行时。

## 结论

承诺是困难的。然而，通过练习和遵循一些规则，与他们一起工作是一种魅力。我严格遵守以上规则，这几天承诺从不出错。找出你觉得舒服的地方，并建立自己的规则。