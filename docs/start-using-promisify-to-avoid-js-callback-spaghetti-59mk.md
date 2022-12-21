# 使用 Promisify 避免 Javascript 回调意大利面

> 原文：<https://dev.to/iggredible/start-using-promisify-to-avoid-js-callback-spaghetti-59mk>

异步代码在 JS 编程中很常见，比如从端点获取数据和读取 dir/文件。通常，它们要求我们传递一个*回调*函数，该函数将在动作完成时执行。

# 回调异步的问题

回调异步的问题是它们会变得混乱。

如果我想读取一个文件(使用 [fs.readFile](https://nodejs.org/api/fs.html#fs_fs_readfile_path_options_callback) ，可以这样做:

```
fs.readFile('./file/location.md', 'utf-8', function(err, val){
  if(err) throw new Error ("Something terrible happened")
  console.log("Content: ", val)
})
console.log("Waiting...") 
```

Enter fullscreen mode Exit fullscreen mode

你会注意到`"Waiting"`显示在`"Content"`之前。这是因为 JS 自动将**所有的**异步函数移到行的后面(不管它们执行得有多“快”)。

如果我们需要在下一个动作中使用异步函数的结果，这是一件大事。如果我们需要使用我们的回调函数的结果，下面的方法不起作用:

```
let pathToNextLocation; 
fs.readFile('./file/location1.md', 'utf-8', function(err, val){
  if(err) throw new Error
  pathToNextLocation = val; 
})

console.log(pathToNextLocation); 
```

Enter fullscreen mode Exit fullscreen mode

我们需要这样做:

```
let pathToNextLocation
fs.readFile('./file/location1.md', 'utf-8', function(err, val){
  if(err) throw new Error
  pathToNextLocation = val; 
  fs.readFile(pathToNextLocation, 'utf-8', function(err, val) {
    // do stuff!
  })
}) 
```

Enter fullscreen mode Exit fullscreen mode

如果我们需要依次执行四个异步函数会怎样？我们必须将它嵌套四层。这是一根大意大利面。

[![big spaghetti](img/88782d619d630288341658ec9f1d7702.png)](https://i.giphy.com/media/r9jG5FH7chblC/giphy.gif)

# 处理异步的更好方法:承诺

处理异步函数的一个更好的方法是使用承诺。承诺和回调一样，是异步的。与回调不同，它们可以被链接。

Promise 有两个参数，我们需要`resolve`它——把它想成 Promise 在完成后返回值的方式。

```
new Promise((resolve, reject) =>
  resolve('Hello promise')
)
.then(value => console.log(value)) 
```

Enter fullscreen mode Exit fullscreen mode

这个`then`链真的很牛逼，因为现在我们可以这样做:

```
asyncReadFile('./file/to/location1.md', 'utf-8')
.then(value => {
  return anotherPromise
})
.then(value => {
  return anotherPromise
})
.then(value => {
  return yetAnotherPromise
})
// and so on 
```

Enter fullscreen mode Exit fullscreen mode

这看起来比回拨意大利面好多了。

# 把两者放在一起:用承诺代替所有的回电

我们学到了两件事:

1.  过多的回调会导致代码混乱
2.  连锁承诺很容易读懂

然而，回调函数并不等同于承诺。`fs.readFile`不要食言。我们不能仅仅用`then`链把几个`fs.readFile`连在一起。

> “嗯，我想知道是否有一种方法可以将它们转化为承诺，这样我就可以将它们链起来，让它们看起来更漂亮？”-我在想

绝对的！！promise rify 就是这么做的。

Promisify 是构建在 Node 8+中的 util 的一部分。它接受一个接受回调函数的函数(哇，好满嘴)。结果函数是一个返回承诺的函数。让我们直入主题吧。等我们自己跑了之后就有意义了。

让我们在包含要读取的其他文件名称的目录中创建几个文件。然后我们将读取第一个文件——看看我们是否能读到最后一个文件。

```
// file1.md
file2.md

// file2.md
file3.md

// file3.md
Finished!

// reader.js
const fs = require("fs");
const { promisify } = require("util");

const promiseReadFile = promisify(fs.readFile);

promiseReadFile("file1.md", "utf-8")
  .then(content => {
    const nextFileToRead = content.trim();
    return promiseReadFile(nextFileToRead, "utf-8");
  })
  .then(content => {
    const nextFileToRead = content.trim();
    return promiseReadFile(nextFileToRead, "utf-8");
  })
  .then(content => {
    console.log(content.trim());
  }); 
```

Enter fullscreen mode Exit fullscreen mode

现在让我们`node ./reader.js`看看会发生什么。您应该看到打印的`"Finished!"`。

太棒了。这是我不介意吃的意大利面。

[![delicious spaghetti](img/0abb08ebda40ef774779318acbb1307a.png)](https://i.giphy.com/media/3o72F2CaK3Hk53WxGg/giphy.gif)

Javascript 有另一种处理承诺的方式: [async/await](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/async_function) 。

为了测试你的理解能力，你能把上面的承诺代码从`then`转换成`async/await`吗？

感谢阅读。祝你快乐！如果你有问题，请告诉我！

# 资源

1.  [util promise](https://2ality.com/2017/05/util-promisify.html)
2.  [node8 util promisify 太牛逼了](https://medium.com/greyatom/node8s-util-promisify-is-so-awesome-9819f1b56d18)
3.  [允诺](https://javascript.info/promisify)
4.  [理解节点承诺和回调](https://medium.com/trabe/understanding-nodes-promisify-and-callbackify-d2b04efde0e0)
5.  [承诺文件](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)
6.  [nodeJS](https://dev.to/martinnrdstrm/callback-functions-innodejs--2607)中的回调函数
7.  [Javascript 和异步魔法](https://levelup.gitconnected.com/javascript-and-asynchronous-magic-bee537edc2da)
8.  [JavaScript 是同步还是异步？承诺到底是什么？](https://medium.com/better-programming/is-javascript-synchronous-or-asynchronous-what-the-hell-is-a-promise-7aa9dd8f3bfb)