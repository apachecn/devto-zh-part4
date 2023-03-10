# Javascript: Entendendo 异步迭代器

> 原文：<https://dev.to/_staticvoid/entendendo-async-iterators-1opo>

一段时间前，我在媒体上做了帖子，在帖子中我讲述了迭代器协议及其使用界面的全部内容。但是，除了像“`Promise.finally`”这样的 API 外，ECMAScript 2018 还为我们提供了另一种处理迭代者的方法。Os *[异步迭代器](https://github.com/tc39/proposal-async-iteration)* 。

## 问题

让我们面对一个相当普通的情况。我们正在和 Node.js 合作，我们需要读取一个文件，一行一行。该节点具有一个名为“[”的此类函数的 API(请参见此处的完整文档“](https://nodejs.org/api/readline.html)”)，该 API 是一个包装程序，因此您可以从一个行到行的输入流中读取数据，而不必从输入缓冲区中执行“*”解析并断开*

她公开了一个事件 API，你可以这样听到:

```
const fs = require('fs')
const readline = require('readline')
const reader = readline.createInterface({
  input: fs.createReadStream('./arquivo.txt'),
  crlfDelay: Infinity
})

reader.on('line', (line) => console.log(line)) 
```

Enter fullscreen mode Exit fullscreen mode

想象一下我们有一个简单的文件:

```
linha 1
linha 2
linha 3 
```

Enter fullscreen mode Exit fullscreen mode

如果在创建的文件中滚动此代码，则控制台中会出现逐行输出。但是，处理事件不是生成可持续代码的最佳方法之一，因为事件是完全异步的，并且可能会中断代码流，因为它们是按顺序触发的，并且只能通过侦听器分配一个操作。

## 解决方案

除了活动 API 以外，`readline`还公开了`async iterator`。也就是说，我们不是在`line`事件中通过侦听程序进行线路读取，而是通过关键词`for`的新用法进行线路读取。

今天我们有一些用于重复连接的选项`for`，其中第一个是最常用的型号，使用计数器和条件:

```
for (let x = 0; x < array.length; x++) {
  // Código aqui
} 
```

Enter fullscreen mode Exit fullscreen mode

也可以用`for … in`表示法读取数组索引:

```
const a = [1,2,3,4,5,6]

for (let index in a) {
  console.log(a[index])
} 
```

Enter fullscreen mode Exit fullscreen mode

在前一种情况下，我们的输出将是数字 1 到 6，但如果使用`console.log(index)`，我们将对数组索引进行对数，即数字 0 到 5。

在下一种情况下，我们可以使用`for … of`表示法直接获取数组的可枚举属性，即其直接值:

```
const a = [1,2,3,4,5,6]

for (let item of a) {
  console.log(item)
} 
```

Enter fullscreen mode Exit fullscreen mode

你是否意识到我所描述的所有使用形式都是同步的，也就是说，我们如何才能按顺序阅读一系列承诺？，假设我们有另一个接口，它总是返回一个 Promise，它将解析到我们的文件行。为了有条不紊地解决这些承诺，我们必须做这样的事情:

```
async function readLine (files) {
  for (const file of files) {
    const line = await readFile(file) // Imagine que readFile é o nosso cursor
    console.log(line)
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

但是，多亏了异步迭代的魔力(如‘t0’)，我们可以做到以下几点:

```
const fs = require('fs')
const readline = require('readline')
const reader = readline.createInterface({
  input: fs.createReadStream('./xpto.txt'),
  crlfDelay: Infinity
})

async function read () {
  for await (const line of reader) {
    console.log(line)
  }
}

read() 
```

Enter fullscreen mode Exit fullscreen mode

注意我们现在使用的是一个新的定义`for await (const x of y)`，即`for await (const x of y)`。

### 为 Await e Node.js

在运行 10.x 版 Node.js 时，本机支持标记“`for await`”。如果使用 8.x 或 9.x 版，则必须使用标记“`--harmony_async_iteration`”启动 Javascript 文件。很遗憾，Node.js 的版本 6 或 7 不支持异步迭代器。

## 迭代器

为了理解异步迭代器的概念，我们需要对什么是迭代器本身进行总结。我前面的文章是一个较大的信息来源，但总之，迭代器是一个暴露函数`next()`的对象，该函数返回另一个标记为`{value: any, done: boolean}`的对象，其中`value`是当前迭代的值，`done`标识是否存在其他值一个简单的例子是遍历数组中所有项的迭代器:

```
const array = [1,2,3]
let index = 0

const iterator = {
  next: () => {
    if (index >= array.length) return { done: true }
    return {
      value: array[index++],
      done: false
    }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

单单一个迭代器没有任何实际用途，为了使我们能够从中受益，我们需要一个`iterable`。un`iterable`是具有键`Symbol.iterator`的对象，它返回函数，我们的迭代器返回该函数:

```
// ... Código do iterador aqui ...

const iterable = {
    [Symbol.iterator]: () => iterator
} 
```

Enter fullscreen mode Exit fullscreen mode

现在我们可以正常使用它，配合`array`，我们将拥有`array`的所有值，一个接一个迭代。

> 如果你想多了解一些关于符号的信息，请看一下我刚刚写的关于主题的另一篇文章

在布下，所有阵列和反对者都有一个`for (let x of [1,2,3])`，这样我们就可以做到`for (let x of [1,2,3])`，并返回我们想要的值。

## 异步迭代器

正如您所预期的那样，异步迭代器与迭代器完全相同，只是我们的迭代器中有一个“`Symbol.asyncIterator`”而不是“`{value, done}`”迭代，而不是返回“`{value, done}`”的对象，我们将有一个用于解析具有相同签名的对象的承诺。

让我们把上面的迭代器变成异步迭代器:

```
const array = [1,2,3]
let index = 0

const asyncIterator = {
  next: () => {
    if (index >= array.length) return Promise.resolve({done: true})
    return Promise.resolve({value: array[index++], done: false})
  }
}

const asyncIterable = {
  [Symbol.asyncIterator]: () => asyncIterator
} 
```

Enter fullscreen mode Exit fullscreen mode

### 异步迭代

我们可以通过调用函数`next()` :
手动迭代任何迭代器

```
// ... Código do async iterator aqui ...

async function manual () {
    const promise = asyncIterator.next() // Promise
  await p // Object { value: 1, done: false }
  await asyncIterator.next() // Object { value: 2, done: false }
  await asyncIterator.next() // Object { value: 3, done: false }
  await asyncIterator.next() // Object { done: true }
} 
```

Enter fullscreen mode Exit fullscreen mode

为了能够通过我们的异步迭代器迭代，我们必须使用`await`，但是记住`await`关键字只能在`async function`内使用，也就是说，我们必须有这样的东西:

```
// ... Código acima omitido ...

async function iterate () {
  for await (const num of asyncIterable) console.log(num) 
}

iterate() // 1, 2, 3 
```

Enter fullscreen mode Exit fullscreen mode

但是，与异步迭代器一样，节点 8.x 或 9.x 不支持异步迭代器，为了在这些版本中使用异步迭代器，我们可以简单地从其对象中提取“`next`”并手动迭代它们:

```
// ... Código do async iterator aqui ...

async function iterate () {
  const {next} = asyncIterable[Symbol.asyncIterator]() // pegamos a função next do iterator

  for (let {value, done} = await next(); !done; {value, done} = await next()) {
    console.log(value)
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

你会发现`for await`更干净，简洁得多，因为它的行为就像一个普通的循环，而且，除了简单得多外，它还通过`done`键单独检查迭代结束。

#### 处理错误

如果我们的诺言在我们的迭代器里被拒绝了怎么办？嗯，就像任何被拒绝的承诺一样，我们可以通过简单的`await`(既然我们使用`await`:

```
const asyncIterator = { next: () => Promise.reject('Error') }
const asyncIterable = { [Symbol.asyncIterator]: () => asyncIterator }

async function iterate () {
  try {
      for await (const num of asyncIterable) {}
  } catch (e) {
    console.log(e.message)
  }
}

iterate() 
```

Enter fullscreen mode Exit fullscreen mode

## 向后退去

异步迭代器的一个相当有趣的地方是，它们具有回退到“
”的功能，这意味着您还可以将其与常用迭代器一起使用，例如，promises 数组:

```
const fetch = require('node-fetch')
const promiseArray = [
  fetch('https://lsantos.dev'),
  fetch('https://lsantos.me')
]

async function iterate () {
  for await (const response of promiseArray) console.log(response.status)
}

iterate() // 200, 200 
```

Enter fullscreen mode Exit fullscreen mode

## 异步发电机

在很大程度上，迭代器和异步迭代器可以从[生成器](https://medium.com/trainingcenter/javascript-entendendo-generators-408cbce9aee)中创建。生成器是允许暂停和恢复其执行的函数，因此您可以运行一个执行，然后通过函数“`next()`”查找下一个值。

> 这是发电机的非常简单的说明，读一读只讲他们的文章是必要的，这样才能更快更深入地了解发电机。

async generators 的行为就像异步迭代器，但是，您必须手动实现停止机制，例如，我们将为 git commits 构建一个随机消息生成器，以使您的同事对您的贡献感到非常高兴:

```
const fetch = require('node-fetch')
async function* gitCommitMessageGenerator () {
  const url = 'https://whatthecommit.com/index.txt'

  while (true) {
    const response = await fetch(url)
    yield await response.text() // Retornamos o valor
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

请注意，我们在任何时候都不会返回对象`{value, done}`，因此循环无法知道执行何时结束。我们可以这样实现一个功能:

```
// Código anterior omitido
async function getCommitMessages (times) {
  let execution = 1
  for await (const message of gitCommitMessageGenerator()) {
    console.log(message)
    if (execution++ >= times) break
  }
}

getCommitMessages(5)
// I'll explain this when I'm sober .. or revert it
// Never before had a small typo like this one caused so much damage.
// For real, this time.
// Too lazy to write descriptive message
// Ugh. Bad rebase. 
```

Enter fullscreen mode Exit fullscreen mode

## Caso de uso

为了更有趣的例子，我们将为一个实际使用案例构造一个异步迭代器。当前，Node.js 的 Oracle 数据库驱动程序支持一个“`getRow()`”API，该 API 查询数据库并返回一个可通过`getRow()`方法逐一读取的记录流。

为了创造这种`resultSet`我们需要在银行进行查询，如下所示:

```
const oracle = require('oracledb')
const options = {
  user: 'usuario',
  password: 'senha',
  connectString: 'string'
}

async function start () {
  const connection = await oracle.getConnection(options)
  const { resultSet } = await connection.execute('query', [], { outFormat: oracle.OBJECT, resultSet: true })
  return resultSet
}

start().then(console.log) 
```

Enter fullscreen mode Exit fullscreen mode

我们的`resultSet`有一种叫`getRow()`的方法，它给我们传回银行下一行应该带来的承诺，这对异步迭代器来说是一个很好的使用案例，不是吗？我们可以创建一个游标，每行返回这个`resultSet`。让我们通过创建一个类`Cursor` :
使其稍微复杂一点

```
class Cursor {
  constructor (resultSet) {
    this.resultSet = resultSet
  }

  getIterable () {
    return {
      [Symbol.asyncIterator]: () => this._buildIterator()
    }
  }

  _buildIterator () {
    return {
      next: () => this.resultSet.getRow().then((row) => ({ value: row, done: row === undefined }))
    }
  }
}

module.exports = Cursor 
```

Enter fullscreen mode Exit fullscreen mode

请注意，光标接收到它必须工作的‘t0’，并按其当前状态存储它。所以，让我们改变我们以前的方法，让我们一次返回游标而不是`resultSet`:

```
const oracle = require('oracledb')
const options = {
  user: 'usuario',
  password: 'senha',
  connectString: 'string'
}

async function getResultSet () {
  const connection = await oracle.getConnection(options)
  const { resultSet } = await connection.execute('query', [], { outFormat: oracle.OBJECT, resultSet: true })
  return resultSet
}

async function start() {
  const resultSet = await getResultSet()
  const cursor = new Cursor(resultSet)

  for await (const row of cursor.getIterable()) {
    console.log(row)
  }
}

start() 
```

Enter fullscreen mode Exit fullscreen mode

这样，我们就可以循环处理所有返回的行，而无需单个承诺解决方案。

## 结论

异步迭代器非常强大，尤其是在动态和异步语言(如 Javascript)中，您可以使用异步迭代器将复杂的执行转换为简单代码，从而隐藏大多数用户复杂性。

不要忘了在我的博客上跟进我的内容，如果[订阅时事通讯](https://blog.lsantos.dev/signup?utm_source=devto&utm_medium=article&utm_campaign=async_iterators)接收每周新闻！