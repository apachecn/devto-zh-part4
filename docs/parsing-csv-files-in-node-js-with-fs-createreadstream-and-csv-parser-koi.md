# 使用 fs.createReadStream()和 csv-parser 解析 Node.js 中的 CSV 文件

> 原文：<https://dev.to/isalevine/parsing-csv-files-in-node-js-with-fs-createreadstream-and-csv-parser-koi>

*封面图片鸣谢: [**猎人 x 猎人**漫画](https://en.wikipedia.org/wiki/Hunter_%C3%97_Hunter)作者富坚义博，由你真正迷上。< 3*

啊，好的 ol' CSV 文件:纯文本呈现的电子表格/数据库数据。漂亮！

解析 CSV(**C**omma-**S**separated**V**alue)文件需要逐行读取一系列记录。通常(但[不一定是](https://en.wikipedia.org/wiki/Comma-separated_values#Standardization))，第一行被解释为每列的标题。第一行之后的所有行都被认为是单独的记录，每个用逗号分隔的值[属于一个单独的列。因此，CSV](http://www.quickmeme.com/img/9f/9f55f04273cff24436e87bad7d5fd404caec71626e655b94b148c41e82013c4a.jpg) [的每一行必须具有与其他行](https://en.wikipedia.org/wiki/Comma-separated_values#Specification)完全相同的值数量。(好的，我意识到**写关于 CSV 的**是非常棘手的——请看下面的可视化！)

# 挑战

在最近的一次外卖挑战中，我被要求解析一个 CSV 购买清单，其中包括:

*   日期(字符串形式)
*   item(作为整数)-在这个例子中，我们将使用 candy！
*   价格(浮动)
*   名称(字符串形式)

下面是我们将要使用的示例代码(并透露一下我最近看电视的情况):

**原始 CSV:**

```
PURCHASE DATE,CANDY PURCHASED,CASH PAID,BUYER NAME
2016-04-03,1000,10000.11,Charlie Kelly
2017-11-14,1000,12000.22,Frank Reynolds
2018-01-20,2000,40000.33,Frank Reynolds
2018-03-20,2000,40000.44,Mac
2019-01-02,2000,50000.55,Sweet Dee
2019-01-02,1500,13500.66,Dennis Reynolds 
```

**更多可读格式:**

```
PURCHASE DATE, | CANDY PURCHASED, | CASH PAID, | BUYER NAME
---------------|------------------|------------|-----------------
2016-04-03,    | 1000,            | 10000.11,  | Charlie Kelly
2017-11-14,    | 1000,            | 12000.22,  | Frank Reynolds
2018-01-20,    | 2000,            | 40000.33,  | Frank Reynolds
2018-03-20,    | 2000,            | 40000.44,  | Mac
2019-01-02,    | 2000,            | 50000.55,  | Sweet Dee
2019-01-02,    | 1500,            | 13500.66,  | Dennis Reynolds 
```

# 我们的工具

我们将使用两个特定的 Node.js 工具来实现我们的目标:

1.  Node.js [fs(文件系统)模块](https://nodejs.org/api/fs.html#fs_file_system)，具体是 [fs.createReadStream()方法](https://nodejs.org/api/fs.html#fs_fs_createreadstream_path_options)
2.  npm 包， [csv-parser](https://www.npmjs.com/package/csv-parser) ，它将我们的 csv 转换成 JSON

因为`fs`模块是 Node.js 自带的，所以不需要外部包。对于我们的 csv-parser npm 包，通过在您的终端中运行`$ npm install csv-parser`来安装它。

让我们通过用`require` :
导入两者来开始我们的`app.js`代码

```
// app.js

const csvParser = require('csv-parser');
const fs = require('fs'); 
```

以这种方式导入`csv-parser`将允许我们调用`csvParser()`作为一个方法，这将允许我们一行一行地通过管道输入它的数据。现在我们准备好开始了！

# 读取 CSV 文件

[![gif of anime hand turning pages on a black book with a pentagram on the cover](img/f0391225145578c9cb4459969a35093f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--GIR5K2Cn--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://giffiles.alphacoders.com/142/142523.gif) 
*我们来读一些外部数据吧！*

上面的 CSV 数据当前存储在`example_data.csv`中，与`app.js`位于同一目录。让我们创建一个保存文件路径的变量:

```
// app.js

const filepath = './example_data.csv' 
```

现在让我们使用 fs.createReadStream()方法:

```
fs.createReadStream(filepath)
    .on('error', () => {
        // handle error
    })

    .pipe(csvParser())
    .on('data', (row) => {
        // use row data
    })

    .on('end', () => {
        // handle end of CSV
    }) 
```

让我们分解每个步骤:

*   `fs.createReadStream(filepath)`初始化`fs.ReadStream`对象——因为这是幕后操作，我们不会与`fs.ReadStream`明确互动。
*   在我们开始尝试通过管道传输数据之前，检查给定文件路径的错误。
*   `.pipe(csvParser())`开始将数据传输到我们的 ReadStream，read stream 现在正在监听接下来的两个事件:
    *   `.on('data', (row) => {} )`逐行返回 CSV 的每一行，可以在回调函数中作为`row`访问。
    *   `.on('end', () => {} )`监听 CSV 的结尾。在读取整个文件后，可以使用该事件调用所需的方法。

# csv 解析器将 csv 数据转换为 JSON

让我们看看`csv-parser`是如何将每一行转换成可用的 JSON 数据的。

记住，CSV 的第一行被记录为列标题！在我们的 JSON 中，这将显示为每个`row`对象的键，它们的值是各自字段中的数据。这将使访问每一行中的数据更加简单。

这是我们完整的代码:

```
const csv = require('csv-parser');
const fs = require('fs');

const filepath = "./example_data.csv"

fs.createReadStream(filepath)
    .on('error', () => {
        // handle error
    })

    .pipe(csv())
    .on('data', (row) => {
        console.log(row);
    })

    .on('end', () => {
        // handle end of CSV
    }) 
```

运行`$ node app.js`输出:

```
// $ node app.js

Row {
'PURCHASE DATE': '2016-04-03',
'CANDY PURCHASED': '1000',
'CASH PAID': '10000.11',
'BUYER NAME': 'Charlie Kelly' }
Row {
'PURCHASE DATE': '2017-11-14',
'CANDY PURCHASED': '1000',
'CASH PAID': '12000.22',
'BUYER NAME': 'Frank Reynolds' }
Row {
'PURCHASE DATE': '2018-01-20',
'CANDY PURCHASED': '2000',
'CASH PAID': '40000.33',
'BUYER NAME': 'Frank Reynolds' }
Row {
'PURCHASE DATE': '2018-03-20',
'CANDY PURCHASED': '2000',
'CASH PAID': '40000.44',
'BUYER NAME': 'Mac' }
Row {
'PURCHASE DATE': '2019-01-02',
'CANDY PURCHASED': '2000',
'CASH PAID': '50000.55',
'BUYER NAME': 'Sweet Dee' }
Row {
'PURCHASE DATE': '2019-01-02',
'CANDY PURCHASED': '1500',
'CASH PAID': '13500.66',
'BUYER NAME': 'Dennis Reynolds' } 
```

有几点需要注意:

*   `csv-parser`为 row-JSON 的`console.log()` -ing 做一些修饰打印。**如果您遇到控制台输出必须匹配特定格式的挑战，请记住这一点！**
*   第一行的键已经存储为字符串，所以为了访问每个值，我们必须使用括号符号:`row["PURCHASE DATE"]`。如果第一行中的标题是没有特殊字符或空格的单个单词(即，如果我们在第一行中有日期而不是购买日期)，它们可以用点符号来访问:`row.DATE`

# 使用行数据

让我们修改我们的`.on('data', (row) => {} )`回调以使用该行的数据，并打印出描述每一次糖果购买的句子:

```
 .pipe(csv())
    .on('data', (row) => {
        let str = `${row["BUYER NAME"]} bought ${row["CANDY PURCHASED"]} pieces of candy on ${row["PURCHASE DATE"]} and paid $${row["CASH PAID"]}.`;
        console.log(str)
    }) 
```

当然，您可以在每个 JSON 行上使用析构来避免写出字符串中的`row["PURCHASE DATE"]`和其他冗长的键名...但是我将把它留到另一篇文章中。；)

运行`$ node app.js`现在输出:

```
// $ node app.js

Charlie Kelly bought 1000 pieces of candy on 2016-04-03 and paid $10000.11.
Frank Reynolds bought 1000 pieces of candy on 2017-11-14 and paid $12000.22.
Frank Reynolds bought 2000 pieces of candy on 2018-01-20 and paid $40000.33.
Mac bought 2000 pieces of candy on 2018-03-20 and paid $40000.44.
Sweet Dee bought 2000 pieces of candy on 2019-01-02 and paid $50000.55.
Dennis Reynolds bought 1500 pieces of candy on 2019-01-02 and paid $13500.66. 
```

瞧啊。CSV 已解析！

# 结论

使用`csv-parser`包在 Node.js 中处理 CSV 非常简单明了。然而，有很多其他方法可以实现这一点，包括许多其他 npm 包选项(惊喜惊喜)，以及纯粹使用`fs`模块。

[这个来自 Dev.to user 的代码片段很好地演示了如何只使用`fs`](https://dev.to/itsasine/comment/4ie4)——长话短说，使用`fs.createReadStream()`返回解析为 utf-8 编码字符串的承诺。但是，您必须手动处理第一行标题和换行符！

有其他策略推荐吗？欢迎在下面随意分享和评论！