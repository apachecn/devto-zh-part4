# 在 Node.js 中传递命令行参数

> 原文：<https://dev.to/isalevine/passing-command-line-arguments-in-node-js-38i5>

*封面图片鸣谢: [**猎人 x 猎人**漫画](https://en.wikipedia.org/wiki/Hunter_%C3%97_Hunter)作者富坚义博，由你真正迷上。< 3*

欢迎来到我的新系列，*初级 JavaScript 求职:技术和带回家的快速技巧*！作为一名最近的 bootcamp 毕业生，我想分享一些我在初级开发职位的 JavaScript 技术挑战方面的经验。(尽管我不喜欢“少年”这个词...但是*我怎么能抗拒标题中的头韵呢？)*

我已经将[这篇涵盖非常基础的 Mocha/Chai/Sinon 测试的文章](https://dev.to/isalevine/learning-javascript-testing-quickly-with-mocha-chai-and-sinon-and-a-lot-of-gaps-17p0)作为本系列的第 1 部分。让我们直接进入第 2 部分，即...

## 在 Node.js 中传递命令行参数

[![anime gif of volleyball being passed quickly](img/0d6010d8212e22d4edfa442376a6e62c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--5znIJAh_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://pa1.narvii.com/6223/fdb21d185c7f91a0f41001ffc519fe0c1ec4077e_hq.gif)

在几次带回家的挑战中，我被要求创建一个从命令行接受一个或多个参数的应用程序。通常，它们涉及到传递一个文件名，或者一个特定格式的日期，比如 YYYY-MM-DD。

让我们看看`process.argv`的 [Node.js 文档](https://nodejs.org/docs/latest/api/process.html#process_process_argv)，这个属性允许我们访问命令行参数:

> `process.argv`属性返回一个数组，该数组包含 Node.js 进程启动时传递的命令行参数。第一个元素将是 [`process.execPath`](https://nodejs.org/docs/latest/api/process.html#process_process_execpath) 。如果需要访问`argv[0]`的初始值，请参见`process.argv0`。第二个元素是正在执行的 JavaScript 文件的路径。其余的元素将是任何附加的命令行参数。

酷！所以`process.argv`是一个包含用于运行代码的命令行参数字符串的数组。让我们运行`$ node app.js`和`console.log(process.argv)`来看看我们得到了什么:

```
$ node app.js

// console.log(process.argv)

[ '/Users/isalevine/.nvm/versions/node/v11.10.0/bin/node',
'/Users/isalevine/coding/nodejs/csv-parse-practice/app.js' ] 
```

`process.argv[0]`显示了 Node.js 的路径，`process.argv[1]`显示了我们运行的`app.js`文件的路径。两者都可以作为字符串访问。

现在让我们添加一个额外的参数，比如本地文件的文件名。csv 文件:

```
$ node app.js example_data.csv

// console.log(process.argv)

[ '/Users/isalevine/.nvm/versions/node/v11.10.0/bin/node',
'/Users/isalevine/coding/nodejs/csv-parse-practice/app.js',
'example_data.csv' ] 
```

我们的数组中有一个新的字符串:`process.argv[2]`是我们提供的文件名。您可以继续添加任意数量的参数！

```
$ node app.js example_data.csv 2019-01-01 and_so_on and_so_on_again and_so_on_some_more

// console.log(process.argv)

[ '/Users/isalevine/.nvm/versions/node/v11.10.0/bin/node',
'/Users/isalevine/coding/nodejs/csv-parse-practice/app.js',
'example_data.csv',
'2019-01-01',
'and_so_on',
'and_so_on_again',
'and_so_on_some_more' ] 
```

使用`process.argv`的另一个好处是,`process`对象及其属性和内容(比如`.argv`)在代码运行时就可用了，并且可以全局访问。再次，[来自 Node.js 文档](https://nodejs.org/docs/latest/api/process.html#process_process_argv):

> `process`对象是一个`global`,它提供关于当前 Node.js 进程的信息并对其进行控制。作为一个全局变量，Node.js 应用程序始终可以使用它，而无需使用`require()`。

太方便了！现在开始肆虐命令行参数传递吧！

当你在这里的时候，请随意留下关于`process`或 Node.js 命令行参数的评论——我们只是触及了表面！