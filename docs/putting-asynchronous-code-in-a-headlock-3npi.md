# 将异步代码放在 Headlock 中

> 原文：<https://dev.to/krtb/putting-asynchronous-code-in-a-headlock-3npi>

<figure>

[![The Simpsons GIF](img/c66c014bad4c3bd0ad726b042675f9d3.png)](https://i.giphy.com/media/3o6MblbJPZ4eO5FBxC/giphy.gif)

<figcaption>If only we were all as enlightened as Homie</figcaption>

</figure>

## 大意

在我的上一篇文章中，我简单介绍了什么是异步函数以及它们与 AJAX 的关系，AJAX 在同步 JavaScript 领域中使用了异步函数。

在这里，我将花一些时间更深入地研究异步与同步，以及为了实现异步而应用的不同模式。

## 这个为什么

*   当试图访问数据库或 API 时，这是非常有用的信息

## 那个啥

#### 同步例子

```
// An example of an index.js file, running Node, would see this in the terminal

console.log('This is synchronous code, or blocking')
console.log('waiting for the first to complete before running')

```

#### 异步示例

```
// An example of an index.js file, running Node, would see this in the terminal

console.log('This is synchronous code, or blocking')

setTimeOut(()=> console.log('Waiting to run, not causing a blockage'), 2000)

console.log('waiting for the first to complete before running')

```

setTimeOut()函数是一个被认为是*“非阻塞”*的函数的例子。

*   上面给出的代码
    *   在异步示例中，第二个 setTimeOut()调用将在 2 秒后运行。
    *   第一个和最后一个呼叫将显示在您的终端上，在分配的时间后，中间的功能。

#### 到目前为止我们学到了什么？

*   同步码(*阻塞*)

    *   阻塞-仅在工作完成后才结束工作
    *   需要 2 个或更多线程，否则会导致程序崩溃/冻结
    *   例如，当在外部 url 调用数据库/api 时，会出现这种情况
    *   该单线程专注于完成它在调用堆栈中找到的第一个任务，并将代码中的其余任务挂起，直到它完成向您返回所请求的信息
*   异步代码(*非阻塞*)

    *   非阻塞:立即返回，稍后中继已完成的工作
    *   仅依赖于至少一个线程，您的程序仍将安全运行
    *   访问像 API 这样大的东西会导致检索所需数据的速度变慢。
    *   你的程序可以自由地运行它的其他任务，并且在事件循环中，它会返回来提供需要的信息
    *   总而言之，异步不在你的考虑范围内，而同步调用需要你的程序的全部注意力。

## 该如何如何

既然我们已经讨论了术语层，我们可以开始研究工程师在代码中处理异步调用时使用的通用模式或方法。

*   我们必须记住的是，我们的函数会在你调用它们的时候尝试返回信息，但是如果我们向外延伸并依赖外部资源来响应...我们永远无法确定我们将等待的时间。如果我们试图返回我们没有的信息，我们的程序会向我们显示那些讨厌的*未定义的*调用之一。那么，我们可以采取哪些措施来解决这个问题呢？

1)回拨

*   当异步操作的结果准备好时，调用*回调*函数。
*   在 JS 中，函数是一个对象
*   同样在 JS 中，函数可以将其他函数作为参数，并且可以由其他函数返回
*   这就是为什么它们被称为高阶函数

> 对其他函数进行运算的函数，无论是将它们作为参数还是返回参数，都称为高阶函数

[HOFs 上精彩的 JavaScript 阅读链接](https://eloquentjavascript.net/05_higher_order.html)

*   对于回调，我们通常将第二个参数传递给第一个函数，该函数将引用第一个函数中的嵌套函数。

[![](img/628f414573a66528b744a64f711b225e.png)](https://i.giphy.com/media/UuTcNAIkDN96/giphy.gif)

```

console.log('This is synchronous code, or blocking');

findSong(1, (song) => {
  console.log('OUR SONG', song);
});

console.log('waiting for the first to complete before running')

function findSong(id, callback) {
   //Simulating a code delay below

    setTimeout(() => {
      console.log('Searching for your song...');
      callback({ id: id, song: 'only 4 u' });
    }, 2000);

}

```

### 下行？

*   如果不是从我上面的简短解释中看出来的话，问题是有一个拖鞋斜坡，你会突然发现自己在里面...唤回地狱。

[![](img/b19c24155c6f01ecb5c7d5906d1e397d.png)](https://i.giphy.com/media/tkvD32sUDfBD2/giphy.gif)

*   回调地狱解释...
    *   正如你从我上面纠结的解释中看到的，构建更复杂的回调会让你陷入...良好的..见鬼了。不仅向其他工程师解释你的代码变得越来越复杂，反过来，你也变得越来越难以理解你的代码在第一时间做了什么。
    *   如果你碰巧发现自己在这个被遗忘的地方，一定要记住使用帮助函数，或**名字函数**，在尝试通读代码时是有帮助的。当您将它们集成到嵌套的回调函数中时，请记住它不会被调用，而只是传递一个对位于文件中其他地方的函数的引用。

所以，让我们继续前进，直到我们找到一个至少更易于管理的解决方案。

2)承诺

*   那些是什么？
    *   技术定义是承诺，“持有异步操作的最终结果”
    *   当异步操作完成时，它要么出错，要么产生您试图处理的值。
    *   在这里，你被“承诺”，你将得到一个异步操作的结果。

[![](img/71283921769177da369325f9d2a678e4.png)](https://i.giphy.com/media/G0NsQBs7rwLF6/giphy.gif)

*   一个'*许诺对象*'可以有三种基本状态
    *   待定状态
        *   启动异步操作
    *   履行状态(已解决)
        *   这意味着异步操作成功完成。
    *   拒绝状态(失败)
        *   当我们试图执行操作时，出现了问题

下面是一个 promise 实例的例子。它采用一个带有两个参数的函数， *resolve 和 reject* 。

```
//somewhere in the code will want to consume this promise object, which will eventually hold our data that is promised to us in this async operation.
const firstPromise = new Promise((resolve, reject) => {
})

```

*   解决和拒绝都是功能
    *   用于将异步操作的结果发送给该承诺的使用者。
    *   当在*拒绝*函数内部传递消息时，最好传递一个错误对象

        ```
        reject(new Error('You've been rejected!'))

        ```

[![](img/33546e6c21e1b0eb283a42835f96ea13.png)](https://i.giphy.com/media/7h7KKsA78bLB6/giphy.gif)

*   。catch /。然后是方法

    *   。然后
    *   有利于继续对返回的数据做更多的工作。

        ```
        .then(result => console.log(result))

        ```

    *   。捕捉
    *   重要的是用来捕捉任何可能发生的错误
    *   当您创建*错误实例*时，它们具有消息属性，您可以使用这些属性来查看您*可能*为自己包含的警告。

        ```
        .catch(err => console.log('You hit an error!',err.message))

        ```

从上面的解释中得到的关键是，无论你在哪里发现回调，在大多数情况下，你都应该修改这个函数来返回一个承诺。

### 消费提示

承诺是通过连锁*消耗*的*。然后*方法和遍历嵌套数据，直到我们得到我们试图获得的信息的核心。我们可以创建有希望的函数，每个函数完成一项任务，并且更容易修改和阅读。

### 谈妥承诺

如果使用单元测试，您可以很容易地使用通过使用 promise 方法解决的 promise。

```
const completed = Promise.resolve()
completed.then(result => console.log(result))

```

您也可以带着错误进行测试

```
const failed = Promise.reject(new Error('your reason'))
// it is best practice to console log only the message property, instead of the entire error object
failed.catch(error => console.log(error.message))

```

### 并行运行

```
 const promiseOne = new Promise((resolve) => {

  setTimeOut(()=>{
   console.log('completed!')
   resolve(1)
}, 2000)

})

 const promiseTwo = new Promise((resolve) => {

  setTimeOut(()=>{
   console.log('completed!')
   resolve(1)
}, 2000)

})

//all method will return a new promise once all promises in this array are resolved
Promise.all([promiseOne, promiseTwo]).then(result => console.log(result))

```

*   承诺。所有
    *   仍然只有一个线程启动多个操作
    *   结果将以数组形式提供
    *   如果这些承诺中的一个失败了呢？
    *   如果我们的任何承诺被拒绝，我们的结果将是失败的，即使有承诺被履行
*   承诺.比赛
    *   如果您希望*不想等待*完成所有承诺，请使用此选项
    *   结果将不是一个数组，而是第一个实现的承诺的值

3)异步和等待

```
async function doSomethingCool(){

const artist = await findArtist(1)  //await keyword released thread to do other work
const album = await findAlbums(artist.albumName)
const song = await findSong(album[0])

console.log(song)
}

doSomethingCool() 
// returns a promise that once fulfilled doesn't result in a value.

```

*   异步和等待
    *   建立在承诺之上
    *   句法糖
    *   我们的代码看起来可能是同步的，但是看起来有点像连锁承诺，使用。然后()

```
findArtist(1)
.then(artist => getAlbums(albums.artistName))
.then(album => findSong(album[0]))
.then(songs => console.log('songs', songs))
.catch(err => console.log('Error', err.message))

```

### 试抓块

*   为了捕捉我们的错误，必须包装我们的代码

```
async function doSomethingCool(){

try {

const artist = await findArtist(1)  //await keyword released thread to do other work
const album = await findAlbums(artist.albumName)
const song = await findSong(album[0])

console.log(song)
} catch (err) {
  console.log('Error'), err.message
}

}

doSomethingCool() 

```

# TLDR；

使用承诺，或者更抽象地说，async/await，允许我们的代码继续前进，并释放我们的单线程来承担其他任务。然后，一旦我们的承诺实现了，我们就可以通过. Then()方法或. catch()方法使用该信息来遍历数据，看看我们如何才能冷静地处理我们的 bug。尽管回调/高阶函数有其好处，但最好避免陷入*‘回调地狱’*。祝你好运！

[![](img/3e9b1a69119a4fb8a9f064b6e4b13531.png)](https://i.giphy.com/media/uA6sERHUlCXYI/giphy.gif)