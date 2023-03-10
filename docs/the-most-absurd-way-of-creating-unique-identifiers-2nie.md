# 创建唯一标识符的最荒谬的方式。

> 原文：<https://dev.to/torstendittmann/the-most-absurd-way-of-creating-unique-identifiers-2nie>

今天我遇到了一个问题。在大学里，坐在我旁边的那个人正在试用 PouchDB，他对标识符感到困惑。对于他来说，为什么必须自己确定每个文档的 ID 是不合逻辑的。在我的上一个项目中，我只是生成了一个随机数，并询问它是否已经存在。实际上相当糟糕，但它的工作。

然后我问了我的朋友谷歌，在 Stackoverflow 上偶然发现了一个解决方案。

那一刻，我震惊于这种方法是如此简单，我怎么从来没有想到这个主意。

只需使用当前 UNIX 时间戳和`new Date().getTime()`创建一个 ID

但是经过短暂的思考后，我问了自己一个问题。当我的代码在一行中生成许多记录时，这种方法仍然有效吗？

在演示中，我使用 PouchDB。

使用 PouchDB 可以创建一批带有 [`db.bulkDocs()`](https://pouchdb.com/api.html#batch_create)
的文档

```
db.bulkDocs([
{
    title : 'Record 1', 
    _id: +new Date()
},
{
    title : 'Record 2', 
    _id: +new Date()
}
]).then(function (result) {
    console.log(result);
}).catch(function (err) {
    console.log(err);
}); 
```

Enter fullscreen mode Exit fullscreen mode

如您所料，只创建第一个条目，第二个条目将返回一个错误，因为它是在相同的时间戳中完成的，并以相同的`_id`结束。

我需要比毫秒更精确的东西。我得到了`performance.now()`的帮助。

与 JavaScript 可用的其他计时数据(例如 Date.now)不同，performance.now()返回的时间戳不限于一毫秒的分辨率。相反，它们将时间表示为精度高达微秒的浮点数。

与 Date.now()不同的是，performance.now()返回的值总是以恒定的速率增加，与系统时钟无关(系统时钟可能手动调整或由 NTP 等软件调整)。

所以如果我把这两种方法结合起来，我会得到一个非常准确的唯一标识符。

让我们创建这个简单的函数:

```
function uniqueID() {
    return new Date().getTime().toString().concat(performance.now());
} 
```

Enter fullscreen mode Exit fullscreen mode

并输出一些数据:

```
console.log(new Date().getTime());
// Output: 1568115683706

console.log(performance.now());
// Output: 218.28000000095926

console.log(uniqueID());
// Output: 1568115683706218.28000000095926 
```

Enter fullscreen mode Exit fullscreen mode

即使这在我看来是完全荒谬的，我也很难想象他会因为一个已经存在的 ID 而出错。

因为在当前 UNIX 时间戳的每一毫秒中，都会添加运行时间的千分之五毫秒(5 微秒)的值。

让我们像这样使用上面的 uniqueID()函数:

```
db.bulkDocs([
{
    title : 'Record 1', 
    _id: uniqueID() // 1568116510818456.76499999899534
},
{
    title : 'Record 2', 
    _id: uniqueID() // 1568116510819456.94000000003143
}
]).then(function (result) {
    console.log(result);
}).catch(function (err) {
    console.log(err);
}); 
```

Enter fullscreen mode Exit fullscreen mode

如您所见，在两个条目之间，结果的差异足够大。

当然，如果数百万用户使用同一个数据库，这种方法会导致问题。但在小范围内，它应该不会遇到问题。

我总是乐于接受想法或建议。你认为这种方法怎么样？

## ![GitHub logo](img/292a238c61c5611a7f4d07a21d9e8e0a.png) [托斯滕德曼](https://github.com/TorstenDittmann) / [荒诞的杜德](https://github.com/TorstenDittmann/absurdUID)

### 基于 UNIX 时间戳和运行时生成唯一字符串的 Javascript 函数。

<article class="markdown-body entry-content container-lg" itemprop="text">

# 这太荒谬了

基于 UNIX 时间戳和运行时生成唯一字符串的 Javascript 函数。

# 为什么？

今天我遇到了一个问题。在大学里，坐在我旁边的那个人正在试用 PouchDB，他对标识符感到困惑。对他来说，为什么要自己决定每个文档的 ID 是不合逻辑的。在我的上一个项目中，我只是生成了一个随机数，并询问它是否已经存在。实际上相当糟糕，但它的工作。

然后我问了我的朋友谷歌，在 Stackoverflow 上偶然发现了一个解决方案。

那一刻，我震惊于这种方法是如此简单，我怎么从来没有想到这个主意。

只需使用当前 UNIX 时间戳和`new Date().getTime()`创建一个 ID

但是经过短暂的思考后，我问了自己一个问题。当我的代码在一行中生成许多记录时，这种方法仍然有效吗？

对于…

</article>

[View on GitHub](https://github.com/TorstenDittmann/absurdUID)