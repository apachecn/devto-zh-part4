# 构建 Reddit 刮刀:设置 Dexie.js

> 原文：<https://dev.to/imtyrelchambers/build-a-reddit-scraper-setting-up-dexie-js-19ci>

欢迎光临！今天我想带你了解一下设置 Dexie.js 的基本知识。

Dexie 是 IndexedDb 的包装器。它允许您保存比 localStorage 或 sessionStorage 更多的数据(它们每个最多可以保存 5mb 的数据)。如果你需要更进一步，你可以使用 IndexedDB。在大多数情况下，我不会真的推荐它，尤其是如果它是需要持久化的数据。然而，对于这个刮刀，我们拉的帖子是否会被擦除并不重要，我们可以再次拉它们。

如果你想了解更多关于 IndexedDb 或 Dexie 的信息，请访问[https://developer . Mozilla . org/en-US/docs/Web/API/indexed db _ API](https://developer.mozilla.org/en-US/docs/Web/API/IndexedDB_API)和【https://dexie.org/】T2。

Dexie 实际上使用起来很有趣，设置起来也很简单，而且他们的文档对开发者也很友好。他们给出了清晰的例子和操作方法，我很欣赏

## **设置 Dexie.js**

我假设你现在已经安装了 Dexie。如果没有，请访问上面的链接并下载该库。

令人欣慰的是，它的设置非常快，他们在首页上给了你一个使用 Dexie 的基本例子。

对于我们的应用程序，我创建了一个`Database.js`文件，其中保存了数据库的配置和实例化。

```
 import Dexie from 'dexie';

const db = new Dexie("Reddex");

window.db = db;
db.version(1).stores({
  posts: "++id, author, title, selftext, ups, url, num_comments, created"
});

db.version(2).stores({
  posts: "++id, author, title, selftext, ups, url, num_comments, created",
  authors: "++id, author",
  subreddits: "++id, subreddit"
});

db.version(3).stores({
  posts: "++id, author, title, selftext, ups, url, num_comments, created, flair",
  authors: "++id, author",
  subreddits: "++id, subreddit"
});

db.version(4).stores({
  posts: "++id, author, title, selftext, ups, url, num_comments, created, flair, postId",
  authors: "++id, author",
  subreddits: "++id, subreddit"
});

export default db; 
```

Enter fullscreen mode Exit fullscreen mode

这是我的全部档案。

为了利用这一点，我们需要导入它`db`。我在这个项目中使用了 React，所以我将它导入到我的 app.js 文件`import db from 'path/to/file'`中。如果你使用的是普通的 JS，你可以做同样的事情，只需要把它导入到主脚本文件的顶部。

`const db = new Dexie("Reddex");`将打开当前数据库(这意味着它将使用当前数据库‘Reddex’)，如果它存在，如果它不存在，它将创建它。我喜欢这样。

每个键代表数据库中的一个表，其中逗号分隔的值代表列。`++id`将把`1`添加到先前的索引中，这样每个条目都有自己的 id，并且继续递增。我们可以通过列名来访问这些值。

因此，我们导入 Dexie 并创建我们的新数据库。在 Dexie 参数中，将是您要创建的数据库的名称。由于我的应用程序被称为 Reddex([https://Reddex . app](https://reddex.app))，这将是我的数据库的名称。

最佳实践是为数据库模型模式的每次更改创建一个新版本。每次我需要在数据库中添加东西时，我都会创建一个新版本。我现在还没有做的一件事是将以前版本的数据库升级到新版本，这一点我将会谈到。

我已经遇到了数据库版本问题，我相信通过升级可以解决这些问题。从文档中，这里有一个执行升级的例子:

```
var db = new Dexie("FriendsAndPetsDatabase");

db.version(2).stores({
    friends: "++id,name,birthdate,sex",
    pets: "++id,name,kind"
}).upgrade (tx => {
    var YEAR = 365 * 24 * 60 * 60 * 1000;
    return tx.friends.toCollection().modify (friend => {
        friend.birthdate = new Date(Date.now() - (friend.age * YEAR));
        delete friend.age;
    });
});
// Always keep the declarations previous versions
// as long as there might be users having them running.
db.version(1).stores({
    friends: "++id,name,age",
    pets: "++id,name,kind"
});
db.open(); 
```

Enter fullscreen mode Exit fullscreen mode

## **保存到我们的德协数据库**

既然我们已经建立了数据库，我们就可以开始保存数据了。

正如我在以前的一篇文章([https://dev . to/imtyrelchambers/build-a-Reddit-scraper-fetching-posts-4m 50](https://dev.to/imtyrelchambers/build-a-reddit-scraper-fetching-posts-4m50))中简要提到的，我简要概述了这个保存过程，为了将类似的概念放在一起，我将在这里再次提及。

```
export const saveToDatabase = async (posts) => {
  const newPosts = []; 
  posts.map(x => newPosts.push(x.data));

  await newPosts.map(x => {
    return window.db.posts.add({
      author: x.author,
      title: x.title,
      selftext: x.selftext,
      ups: x.ups,
      url: x.url,
      num_comments: x.num_comments,
      created: x.created,
      flair: x.link_flair_text
    });
  });
  return true;
} 
```

Enter fullscreen mode Exit fullscreen mode

为了从任何地方访问我们的数据库，我采用的解决方案是创建一个变量，并将其附加到窗口的范围内。我相信有更好的方法。

为了创建这个变量，它是这样运行的`window.db = db;`。然后，我们可以在应用程序的任何地方访问它。

您会注意到我们再次访问窗口范围来获取我们的`db`变量。因为我们有一个名为`posts`的表，这就是我们链接到数据库变量上的内容。它说:我想访问我们的数据库和我们的`posts`集合的`add()`(或表格，取决于你习惯的术语)。

## **从我们的数据库中抓取数据**

既然我们已经保存了数据，我们需要一种方法来获取它。下面我创建了一个函数来完成这个任务。

```
export const getPostsFromDatabase = async (setPosts) => {
  const db = window.db;
  const posts = await db.posts.toArray();
  return setPosts([...posts]);
} 
```

Enter fullscreen mode Exit fullscreen mode

就像保存到我们的数据库一样，我们想要获取包含在我们的`posts`集合中的数据(我称之为集合)。我们必须将这些数据转换成一个数组，以便以后可以像平常一样使用它。

为了做到这一点，我们需要著名的全局变量，我们将使用它再次访问我们的集合。为了获取数据，我们需要访问我们想要从中提取的集合，在我们的例子中，它是`posts`，然后我们需要将`toArray()`链接到它上面。好了，我们完成了。下一步是用我们拥有的数据设置任何状态或变量。

## **从数据库中清除记录**

对于 Reddex，我在写入数据库之前会对其进行擦除。这是因为我不希望重复的条目被保存并在以后被检索。它确保显示的数据与保存的数据相同。

```
export const deletePostsCollection = () => {
  const db = window.db;
  db.posts.clear().then().catch();
} 
```

Enter fullscreen mode Exit fullscreen mode

如果我是一名优秀的开发人员，我可能会在承诺的`then()`和`catch()`部分做一些事情，但是现在我们在这里！(我当然是(拿自己)开玩笑)。这是你显示对用户有意义的任何错误的地方，或者你想做一些随机的事情，一旦承诺解决了，就看你的了。

说到用 Dexie.js 就差不多了！试试看！谢谢你能走到这一步。

**鳍**