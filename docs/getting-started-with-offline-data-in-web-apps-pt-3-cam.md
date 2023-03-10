# Web 应用程序中的脱机数据入门。3

> 原文：<https://dev.to/remotesynth/getting-started-with-offline-data-in-web-apps-pt-3-cam>

在本系列的第 1 部分中，我们查看了 API 来确定用户的在线/离线和连接状态。在[第 2 部分](https://dev.to/remotesynth/getting-started-with-offline-data-in-web-apps-pt-2-39o2)中，我们看到了使用 LocalStorage 离线存储少量数据。在这一部分中，我们将开始研究如何使用 IndexedDB 离线存储大量复杂数据。

## 什么是 IndexedDB？

如果您使用过 NoSQL 数据存储，您会对 IndexedDB 的工作方式感到相对舒适。与 LocalStorage 一样，IndexedDB 中的值以键值对的形式存储数据，但是，与只有字符串值的 LocalStorage 不同，这些值可以是复杂的对象。如您所料，键必须是唯一的，但它可以是对象的一个属性。

在这里我要实话实说，IndexedDB 并不是世界上最简单的东西。这当然远远超出了我在这里所能详细介绍的范围，但是需要了解的关于 IndexedDB 的关键事情是:

*   **异步**——与 LocalStorage 不同，在 IndexedDB 中存储和检索数据不会阻塞 UI。
*   **为存储大量数据而优化**——顾名思义，IndexedDB 中的对象存储是索引的，提供了一种基于这些索引快速检索值的方法，而不是使用游标遍历所有记录。我应该注意，如果您的索引不是惟一的，您仍然需要打开一个游标来获取给定索引值的所有结果。就像我说的，IndexedDB 并不简单。
*   **处理复杂数据**——通常任何站点都有一个 IndexedDB 数据库，但是这个数据库可以包含任意数量的对象存储。顾名思义，对象存储是为存储对象而设计的。
*   **大存储限制** -存储限制的确切大小很难指定，因为它是动态的，取决于可用的磁盘空间，但可以达到 GB 的存储(Raymond Camden 有一篇关于[测试 IndexedDB](https://www.raymondcamden.com/2015/04/17/indexeddb-and-limits) 的存储限制的有点过时但仍然很有趣的帖子)。
*   **事务性**-indexed db 中的每次读写都必须发生在事务的上下文中。对于熟悉传统事务性 SQL 数据库工作方式的人来说，这似乎很熟悉。简而言之，事务确保一组数据库操作从头到尾都是完整的——任何一点的失败都会回滚整个事务。
*   **SQL-less** - IndexedDB 无法使用类似 SQL 的查询语言进行查询。为了能够被搜索，一个值必须被索引，即使这样，你也不能使用类似于 SQL 的`LIKE`来文本搜索一个值。处理需要基于多个索引进行搜索的情况也不是很简单。

所以，我简单快速的概述并不简单快速。如果你想了解更多，我推荐阅读 MDN 上的 IndexedDB 的[基本概念。](https://developer.mozilla.org/en-US/docs/Web/API/IndexedDB_API/Basic_Concepts_Behind_IndexedDB)

## indexed db 入门

在这一节中，我们将了解一些使用 IndexedDB 存储数据的基础知识。我将构建一个非常简单的页面，该页面从 [Cocktail API](https://www.thecocktaildb.com/api.php) 加载数据，然后将其本地存储在 IndexedDB 中，以便可以更快地检索和/或离线加载后续页面。

### 创建数据库

创建数据库时有一些样板文件。

```
let db;
let dbRequest = window.indexedDB.open("Cocktaildb", 1);

dbRequest.onerror = function(event) {
  alert("Database error: " + event.target.errorCode);
};
dbRequest.onsuccess = function(event) {
  db = event.target.result;
  getCocktails();
};
dbRequest.onupgradeneeded = function(event) { 
  const db = event.target.result;

  let cocktailStore = db.createObjectStore("Cocktails", { keyPath : 'idDrink' });
}; 
```

Enter fullscreen mode Exit fullscreen mode

`open()`方法有两个参数。第一个是数据库的名称。第二个是数据库的版本，这是可选的，如果数据库不存在，将默认为 1(否则将默认为现有的版本号)。需要注意的是版本必须是整数，所以使用 1.2 这样的版本和使用 1 是一样的。

如果数据库不存在或者大于现有版本，就会触发`dbRequest.onupgradeneeded`事件。这是您将创建对象存储或对现有数据执行任何必要更新的地方。您还可以在这里创建任何必要的索引。

如果需要，一旦连接已经打开并且任何升级完成，就会触发`onsuccess`方法。

> 我在这里没有涉及很多项目，包括索引，在大多数需要基于属性而不是 id 来查找或更新记录的用例中，索引是很重要的。有很多很好的资源可以更深入地研究 IndexedDB。从 MDN 的使用 IndexedDB 的指南[或谷歌的](https://developer.mozilla.org/en-US/docs/Web/API/IndexedDB_API/Using_IndexedDB)[使用 IndexedDB](https://developers.google.com/web/ilt/pwa/working-with-indexeddb) 的指南开始。

### 插入数据

现在我们已经创建了数据库并打开了连接，是时候填充它了。

```
let cocktailsStore = db.transaction(["Cocktails"], "readwrite").objectStore("Cocktails");
data.drinks.forEach(item => {
  cocktailsStore.put(item);
}); 
```

Enter fullscreen mode Exit fullscreen mode

如前所述，与数据的每一次交互都必须发生在事务的上下文中。`transaction()`方法有两个参数。第一个是将在事务范围内使用的对象存储名称数组，第二个是访问类型，可以是`readonly`或`readwrite`。

因为我们将要插入数据，我们将需要`readwrite`模式。然后，我打开一个到`Cocktails`对象存储的连接。这是在一行中执行的，但可以分开来保存对返回的事务对象和对象存储对象的变量引用。最后，我使用对象存储上的`put()`方法将对象插入数据存储。如果我正在更新记录，`put()`仍然有效。

### 检索数据

现在我们的对象存储已经被填充了，让我们从其中取回数据。

```
let cocktailsStore = db.transaction(["Cocktails"], "readonly").objectStore("Cocktails");
let getCocktailData = cocktailsStore.getAll();
getCocktailData.onsuccess = function(event) {
    if (event.target.result.length === 0) {
      // load the remote data
    }
    else {
      // display the local data
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

该示例从对象存储中获取所有记录。您仍然需要在事务中工作，但是，在这种情况下，我们只需要读取数据。`getAll()`方法获取所有记录，我们可以通过迭代来显示这些记录。

如果您只需要获得一条记录，使用`get()`方法并提供键。为了基于索引而不是键，您可以使用`index()`从返回的对象存储中检索对该索引的引用(即上面代码中的`cocktaildb`),然后在该索引上使用`getAll()`或`get()`。

### 完整示例

下面是一个完整的例子来看看它的作用。我添加了一些额外的细节来清除本地数据，使数据显示的位置更加明显。

[https://codepen.io/remotesynth-the-bold/embed/RXyBeJ/?height=600&default-tab=result&embed-version=2](https://codepen.io/remotesynth-the-bold/embed/RXyBeJ/?height=600&default-tab=result&embed-version=2)

## 何去何从

这只是触及了 IndexedDB 的皮毛——同样，使用 IndexedDB 查看 MDN 的[,或者使用 IndexedDB](https://developer.mozilla.org/en-US/docs/Web/API/IndexedDB_API/Using_IndexedDB) 指南查看谷歌的[,因为你已经准备好深入研究了。如果您出于离线或性能目的缓存数据，您还需要想出一个策略来将本地数据与远程数据同步。在某些情况下，您可能希望总是在用户重新联机后立即执行此操作，但是在其他情况下，数据可能不会经常更改或者不会被用户更改，您可能希望设置一种仅定期刷新的方法。所有这些都取决于您正在构建的应用程序的性质。](https://developers.google.com/web/ilt/pwa/working-with-indexeddb)

我之前说过，IndexedDB 不是世界上最简单的东西。然而，有一些非常好的工具可以使使用它变得更加简单。在本系列的下一部分，我们将研究其中的一些。