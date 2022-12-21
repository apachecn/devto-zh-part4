# javascript 中的重庆数组

> 原文：<https://dev.to/osamaqarem/chunking-arrays-in-javascript-164p>

我最近在 react native 应用程序上向 SQLite 数据库插入大数据时遇到了一个问题。我特意创建了一个大型模拟数据集来测试如果用户尝试类似的事情会发生什么。

我立刻遇到了 SQLite 的限制，特别是第 9 个。

*   [https://www.sqlite.org/limits.html](https://www.sqlite.org/limits.html)

```
QueryFailedError:
too many SQL variables (code 1 SQLITE_ERROR):, while compiling:
INSERT INTO "table_name"("Id", "columnOne", "columnTwo") VALUES (...) 
```

Enter fullscreen mode Exit fullscreen mode

显然，这是为了防止过度的内存分配。你可以通过上面的链接了解更多信息。

有问题的查询:

```
// TypeORM
await connection
  .createQueryBuilder()
  .insert()
  .into("table_name")
  .values(largeArray.map(item => item))
  .execute(); 
```

Enter fullscreen mode Exit fullscreen mode

我们能做的是将数据分块。然后，我们可以运行多个查询而不是一个查询来插入相同数量的数据。

为了说明我们目前拥有的数据，我将使用一个字符串数组:

```
const beforeChunking = ["1", "2", "3", "4", "5"]; 
```

Enter fullscreen mode Exit fullscreen mode

我们想要的是一个具有指定块大小的数组。例如，块大小为`2` :

```
const afterChunking = [["1", "2"], ["3", "4"], ["5"]]; 
```

Enter fullscreen mode Exit fullscreen mode

我们可以通过利用`splice()`来实现这一点。我们创建一个函数，它接收要分块的数据和所需的块大小。

```
const getArrayAsChunks = (array, chunkSize) => {
  let result = [];
  let data = array.slice(0);
  while (data[0]) {
    result.push(data.splice(0, chunkSize));
  }
  return result;
}; 
```

Enter fullscreen mode Exit fullscreen mode

`splice()`将从`data`中移除元素的大小为`chunkSize`。剩余的元素将在数组中向后移动，这样一切都解决了。

现在我们可以像这样运行我们的插入查询:

```
const chunksArray = getArrayAsChunks(largeArray, 100);

chunksArray.map(async oneChunk => {
  await connection
    .createQueryBuilder()
    .insert()
    .into("table_name")
    .values(oneChunk.map(item => item))
    .execute();
}); 
```

Enter fullscreen mode Exit fullscreen mode

在低端 android 手机上插入~2000 行只用了不到两秒钟，所以我就这样了。但是我们在这里可以做的一个性能改进是自己定义 SQL 查询，而不是让我们的 ORM 来处理；多次生成查询会产生成本。

* * *

这篇文章最初发表在我的博客上。