# 构建 Javascript 异步函数错误处理程序

> 原文：<https://dev.to/natelindev/build-a-javascript-async-function-error-handler-4fpp>

#### 背景

如果你是 Js 开发者，你大概用过`async`和`await`，你爱死它们了。

不再有回调地狱或`.then`链。就像写同步程序一样。

**直到**你遇到`UnhandledPromiseRejectionWarning`或`Uncaught (in promise) Error`

您开始使用`try.. catch..`包装每一段代码，但是这看起来工作量很大。

但是我有一个好消息，你可以写一个自定义的异步函数错误处理程序:

#### 解

```
const asyncHandler = fn => async (...args) => {
  try {
    await fn(...args);
  } catch (err) {
    console.log(err);
  }
}; 
```

Enter fullscreen mode Exit fullscreen mode

它接受所有类型的带有任意数量参数的函数。

这样用:

```
const asyncHandler = fn => async (...args) => {
  try {
    await fn(...args);
  } catch (err) {
    console.log(err);
  }
};

function sleep(ms) {
  return new Promise(resolve => setTimeout(resolve, ms));
}

const yourFunction = async () => {
  await sleep(2000);
  throw 'something wrong';
  return 'success';
}

(async ()=> {
  await yourFunction(); // will cause Uncaught error
  await asyncHandler(yourFunction)(); // will handle the error properly
})(); 
```

Enter fullscreen mode Exit fullscreen mode

#### 返回值

当然当你需要异步函数的结果时，你可以:

```
const asyncHandler = fn => async (...args) => {
  try {
    return await fn(...args);
  } catch (err) {
    console.log(err);
  }
};

function sleep(ms) {
  return new Promise(resolve => setTimeout(resolve, ms));
}

const yourFunction = async () => {
  await sleep(2000);
  // throw 'something wrong';
  return 'success';
}

(async ()=> {
  const result = await asyncHandler(yourFunction)(); //will handle the error properly
  console.log(result); // => 'success'
})(); 
```

Enter fullscreen mode Exit fullscreen mode

#### 自定义错误处理

如果你需要一些自定义的错误处理，例如，在`express.js`中使用它

你可以简单地添加一些自定义逻辑

```
const asyncHandler = fn => async (...args) => {
  try {
    await fn(...args);
  } catch (err) {
    logger.error(err);
    const resFn = args.find(arg => arg.name === 'res');
    if (resFn) {
      let errors = {
        message: 'Internal Sever Error',
        error: err
      };

      if (err instanceof mongoose.Error.ValidationError) {
        errors = {
          message: 'Mongoose Model Validation Error',
          error: err
        };
      }
      if (err instanceof mongoose.mongo.MongoError) {
        errors = {
          message: 'MongDB Error',
          error: err
        };
      }

      resFn.status(500).json(errors);
    }
  }
}; 
```

Enter fullscreen mode Exit fullscreen mode

后来你把它用在快速路由器上，就像这样:

```
router.get('/item',
  asyncHandler(async (req, res) => {
    // router logic here
  })
) 
```

Enter fullscreen mode Exit fullscreen mode