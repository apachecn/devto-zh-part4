# 基于情景的学习--一种新的学习视角。Node.js -第 1 部分

> 原文：<https://dev.to/ganeshmani/scenario-based-learning-a-new-learning-perspective-node-js-part-1-1bkd>

欢迎阅读这个新的文章系列。基于情景的学习--一种新的学习视角。Node.js -第 1 部分

每当我想学习新技术时，我都能找到很多资源来学习特定编程语言/技术的语法。

但是，我找不到能给我一个生产水平问题场景的资源，从中我可以学到技术。

所以，我想为什么不创建一个对像我这样的开发人员有帮助的程序，通过练习一个问题场景来学习编程语言或技术。

请分享一下。[订阅我的简讯](http://eepurl.com/gvbr7j)获取更多更新。

要学习 Javascript，请阅读这个系列文章[https://cloudnweb.dev/category/web-dev/](https://cloudnweb.dev/category/web-dev/)

### 为什么它很重要？

每一次，我们都学到一些东西。我们去找一些东西来实现它，以便很好地掌握它。从技术初学者的角度来看。太棒了。

但是，在学习技术的初学者和从事生产级应用程序的人之间存在差距。直到我们为一家公司/一个自由职业者项目工作之前，我们真的不知道特定的技术在真正的工业应用中解决了什么样的问题

我要做的是，我将分享我在制作中遇到的所有问题场景。因此，特定技术的初学者可以自己复制场景并从中学习。

基本上，他/她将通过自己的学习获得我的经验。因此，在未来，如果他/她面临同样的问题场景，他/她可以用一种有效的方式来处理它。

### Node.js 体验

这个博客系列来自我的 Node.js 经验。基本上我是 React，Node.js 和 MongoDB 的开发者。

很快，我也会分享 React 的问题场景。本周我将从一个简单的场景开始。在接下来的文章中，我将分享更复杂的场景，您可以从中学习。

基于情景的学习——一种新的学习视角

### 问题场景

最近，我面临一个情况，我需要从互联网上读取一个大文件，并把它写在我的服务器上。

要在 Node.js 中做到这一点，您可以把它想象成只是读取文件并直接写入服务器。

但是这种方法有一个问题，假设我们实现了这样的东西

```
const fs = require('fs');

fs.readFileSync('sample.mkv',(err,data) => {
    if(err) throw err;

    fs.writeFileSync('output',data,(err) => {
        if(err) throw err;
    })
})
```

问题是，

[![](img/46c7e630dc39a77f3b08a8a79ee5349b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--VtWut7un--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cloudnweb.dev/wp-content/uploads/2019/07/bad_code.png)

在 [Node.js 缓冲区](https://nodejs.org/api/buffer.html)中有一个限制，我们不能存储超过缓冲区大小的内容。

为了解决这个问题，我们需要 Node.js 中一个叫做 [Stream](https://nodejs.org/api/stream.html) 的东西。

### 什么是流？

在内存中存储一个完整的文件是非常昂贵的。此外，我们需要在没有这个问题的情况下存储文件。为了解决这个问题，我们使用 Stream，它只不过是处理数据块。

流一个块一个块地处理巨大的数据，一次一个块地存储在内存中。

### 解决办法

我们需要创建一个从源中读取数据的**可读流**和一个将数据写入目的地的**可写流**。

如果你是 Node.js 的新手，我建议你看一些教程，并尝试这个问题场景。

*   [Node.js 速成班](https://www.youtube.com/watch?v=fBNz5xF-Kx4)
*   [node . js Course-Mosh hame dani](https://www.youtube.com/watch?v=TlB_eWDSMt4)

#### 解决方案代码

```
const fs = require('fs');
const stream = require('stream');

//creating a readable stream
const readable = fs.createReadStream('sample');

//creating a writable stream
const writable = fs.createWriteStream('output');

fs.stat('sample',(err,stats) => {

    this.filesize = stats.size;

    this.counter = 1;

    //this is an event which handles the data in chunks
    readable.on('data',(chunk) => {
        let percentageCopied = ((chunk.length * this.counter) / this.fileSize) * 100;
        process.stdout.clearLine(); 
        process.stdout.cursorTo(0);
        process.stdout.write(`${Math.round(percentageCopied)}%`);

        //writing the chunk into writable stream output
        writable.write(chunk);
        this.counter += 1;
    });

    readable.on('end', (e) => {
        console.log("Read is completed");
    });

    readable.on('error', (e) => {
        console.log("Some error occured: ", e);
    });

    writable.on('finish', () => {
        console.log("Successfully created the file copy!");
    });
})
```

它从本地文件读取数据，并从另一个本地文件再次写入数据。出于概念性的目的，我使用了本地文件本身，而不是来自互联网的文件。

这种方法还有一个问题，如果您在这段代码运行时分析机器中的内存管理器。这需要很大的内存。

原因是，磁盘写入速度赶不上磁盘读取速度。读取磁盘将比写入磁盘更快。

美妙的是，我们也能解决这个问题。

### 有效解

Node.js 流对上述问题有一个解决方案，就是 **[背压](https://nodejs.org/es/docs/guides/backpressuring-in-streams/)**

```
 const stream = require('stream');
const fs = require('fs');

let fileName = 'sample'

const readabale = fs.createReadStream(fileName);
const writeable = fs.createWriteStream("output");

fs.stat(fileName, (err, stats) => {
    this.fileSize = stats.size;
    this.counter = 1;
    this.fileArray = fileName.split('.');

    try {
        this.fileoutputName = "output" + "/" + this.fileArray[0] + '_Copy.' + this.fileArray[1];
    } catch(e) {
        console.exception('File name is invalid');
    }

    process.stdout.write(`File: ${this.fileoutputName} is being created:`);

    readabale.on('data', (chunk) => {
        let percentage = ((chunk.length * this.counter) / this.fileSize) * 100;
        process.stdout.clearLine();  // clear current text
        process.stdout.cursorTo(0);
        process.stdout.write(`${Math.round(percentage)}%`);
        this.counter += 1;
    });

    //Note this line : Read Stream pipes the Write Streams
    readabale.pipe(writeable);

    // In case if we have an interruption while copying
    writeable.on('unpipe', (e) => {
        process.stdout.write("Write Failed!");
    });

});; 
```

我们对之前的解决方案所做的唯一改变是将**可读流**通过管道**传输到**可写流****

它会自动控制磁盘读写速度，因此不会阻塞 RAM。

这是一个易于实现的解决方案。同样的概念也可以用在其他一些技术问题场景中

#### 场景#2

考虑一个系统，其中我们实现了一个向 Kafka 提供数据的爬虫。我们需要从 Kafka pipeline 获取数据，并将其存储到数据库中。

#### 场景#3

一个用户正在上传一个巨大的文件，我们需要存储它，但是，我们不能存储超过某个文件大小限制的大小。我们能做的是，实现读取数据并压缩数据的流。将其存储在服务器中。

这篇文章到此为止，希望你喜欢这个系列。如果我能从这个倡议中得到好的回应，我计划在这个系列上写更多的文章。

如果你觉得这有用，请分享它。[订阅我的简讯](http://eepurl.com/gvbr7j)获取更多更新。基于情景的学习--一种新的学习视角。Node.js -第 1 部分

在那之前，快乐编码:-)