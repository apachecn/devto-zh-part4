# 将回电转化为承诺

> 原文：<https://dev.to/zellwk/converting-callbacks-to-promises-nhn>

与回调相比，使用承诺(或异步/等待)更容易。当您在基于节点的环境中工作时，尤其如此。不幸的是，大多数节点 API 都是用回调编写的。

今天我想告诉你如何将回电转化为承诺。

在你阅读这篇文章之前，了解一下什么是[承诺](https://zellwk.com/blog/js-promises/)会有所帮助。

## 将节点样式的回调转换为承诺

来自节点 API 的回调具有相同的模式。它们作为最终参数传递给函数。这里有一个关于`fs.readFile`的例子。

```
const fs = require('fs') 

fs.readFile(filePath, options, callback) 
```

此外，每个回调至少包含两个参数。第一个参数必须是错误对象。

```
fs.readFile('some-file', (err, data) => {
  if (err) {
    // Handle error 
  } else {
    // Do something with data
  }
}) 
```

如果遇到这种模式的回调，可以用 Node 的`util.promisify`转换成承诺。

```
const fs = require('fs')
const util = require('util')

const readFilePromise = util.promisify(fs.readFile) 
```

一旦你把回电转换成承诺，你就可以像使用其他承诺一样使用它。

```
readFilePromise(filePath, options)
  .then(data => {/* Do something with data */})
  .catch(err => {/* Handle error */} 
```

偶尔，您可能会遇到不符合 Node 的错误优先回调格式的 API。对于这些情况，不能使用`util.promisify`。你需要写下你自己的承诺。

## 书写自己的诺言

要将回拨转换为承诺，您需要返回一个承诺。

```
const readFilePromise = () => {
  return new Promise ((resolve, reject) => {
    // ... 
  })
} 
```

您运行带有承诺内回调的代码。

```
const readFilePromise = () => {
  return new Promise((resolve, reject) => {
    fs.readFile(filePath, options, (err, data) => {
      // ...
    })
  })
} 
```

如果有错误，你拒绝承诺。这允许用户处理`catch`中的错误。

如果没有错误，你解决承诺。这允许用户在`then`中决定下一步做什么。

```
const readFilePromise = () => {
  return new Promise((resolve, reject) => {
    fs.readFile(filePath, options, (err, data) => {
      if (err) return reject(err)
      resolve(data)
    })
  })
} 
```

接下来，您需要在承诺中为代码提供类似于`filePath`和`options`的参数。为此，您可以使用[休息和传播](https://zellwk.com/blog/es6/#the-rest-parameter-and-spread-operator)操作符。

```
const readFilePromise = (...args) => {
  return new Promise((resolve, reject) => {
    fs.readFile(...args, (err, data) => {
      if (err) return reject(err)
      resolve(data)
    })
  })
} 
```

然后你可以用`readFilePromise`作为承诺。

```
readFilePromise(filePath, options)
  .then(data => {/* Do something with data */})
  .catch(err => {/* Handle error */} 
```

## 将非节点样式的回调转换为承诺

一旦知道如何构造承诺，将非节点样式的回调转换成承诺就很容易了。您遵循相同的步骤:

1.  如果有错误，则拒绝
2.  否则解决

假设您有一个 API，它返回`data`作为第一个参数，返回`err`作为第二个参数。你可以这样做:

```
const shootPeasPromise = (...args) => {
  return new Promise((resolve, reject) => {
    // This is a not a Node styled callback. 
    // 1\. data is the first argument 
    // 2\. err is the second argument
    shootPeas(...args, (data, err) => {
      if (err) return reject(err)
      resolve(data)
    })
  })
} 
```

## 具有多个参数的回调

假设您有一个带有三个参数的回调:

1.  错误对象
2.  一些数据
3.  另一个数据

```
growTrees(options, (error, location, size) => {
  // ... 
}) 
```

你不能写这个:

```
// Note: This does not work 
const growTreesPromise = (...args) => {
  return new Promise((resolve, reject) => {
    growTrees(...args, (error, location, size) => {
      if (err) return reject(err)
      // You can't send two arguments into resolve
      resolve(location, size)
    })
  })
} 
```

上面的代码不起作用，因为承诺只能返回一个参数。如果要返回许多参数，可以使用数组或对象。

```
// Using an array object
resolve([location, size])

// Using an object
resolve({location, size}) 
```

然后，您可以在`then`调用中析构数组或对象。

```
// If you use arrays
growTreesPromise(options)
  .then([location, size]) => {/* Do something */})

// If you use objects
growTreesPromise(options)
  .then({location, size}) => {/* Do something */}) 
```

* * *

感谢阅读。这篇文章最初发表在我的博客上。如果你想要更多的文章来帮助你成为一个更好的前端开发人员，请注册我的时事通讯。