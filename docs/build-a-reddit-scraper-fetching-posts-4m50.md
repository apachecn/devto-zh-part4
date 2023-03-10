# 构建 Reddit 刮刀:获取帖子

> 原文：<https://dev.to/imtyrelchambers/build-a-reddit-scraper-fetching-posts-4m50>

### **得到我们的帖子**

使我们的 Reddit 刮刀成为实际产品的第一步是获得实际帖子。我住在 nosleep subreddit，但是你可以替换任何 subreddit，结果都是一样的。

如果你访问 https://reddit.com/r/nosleep.json，我们可以看到一大堆 JSON。这太棒了，因为我们可以用它来工作。

这个 JSON 代表了网站上的数据，它将是我们构建 scraper 的基础。每个 subreddit 都有一个 JSON 扩展，可以用来访问这些数据。他们还有一些我们可以使用的其他查询，比如:？极限还是？数数。但是，我们不需要使用它，因为我们希望它默认为最高值。

由于 Reddit 的 API 和数据提供给你的方式，你不能一次抓取所有 subreddit 帖子，我们需要对同一个 url 进行多次调用，但在进行过程中修改“after”属性。

after 属性指定要获取的下一组(~250)项，有点像分页。

```
export const fetchPosts = async (subreddit, setLoading, setPosts, category) => {
  const sr = subreddit.replace(/\s/g, '').trim();
  let endpoint = "";

  const link = `https://www.reddit.com/r/${endpoint}`;
  let posts = [];
  let after = ``;
  if ( !sr || sr.length === 0 ) return alert("Must include a subreddit");

  for ( let i = 0; (i < 10 && after !== null); i++ ) {
    await Axios.get(`${link}&after=${after}`).then(res => {
      after = res.data.data.after;
      posts = [...posts, ...res.data.data.children];
    }).catch(err => err);
  }

  return setLoading(false);  

} 
```

Enter fullscreen mode Exit fullscreen mode

这不是全部功能。我已经删除了一些不直接面向获取的行。

这个函数的主要目的是对 Reddit 进行 API 调用。我想把重点放在带有 get 请求的 for 循环上。Chrome 对我大喊在循环中使用函数，所以也许可以看看更好的方法，但不管怎样，我们还是继续吧。

我们希望循环，直到 after 属性变为 null。由于 Reddit 最多只能返回大约 1000 个帖子，我们可以将限制设置为 10 个..这是任意的，我可能会一直循环，直到 after 为 null，而不是限制变量计数，但这更像是一个故障保险。

所以，我们需要定义并附加 after 查询，你可以像我一样做，或者在第一次循环后添加它，无论如何，但我必须做的主要事情是在下一次请求时使用当前的 after 属性。这样，我们可以继续循环和调用，同时逐步实现 Reddit API 的分页风格。如果我们不改变这个值，它会一直抓取同一组数据，直到 i === 10。

在每个成功的请求之后，我们将包含我们的帖子的数据传播到一个变量中，稍后我们将使用该变量保存到我们的数据库中。我们还用来自该请求的值更新我们的“after”变量。

整个过程大约需要 10 秒钟来获得 1000 个帖子(实际返回多少是不确定的)。

在该函数结束时，我们将加载状态设置回 false。如果我们不这样做，它将继续显示我们的加载程序，这只是适得其反。

继续前进。

```
 posts.shift();
  posts.map(x => results.push(x.data));
  deletePostsCollection();
  saveToDatabase(posts);
  saveSubredditToLocalStorage(subreddit);
  await setPosts([...results]);
  return setLoading(false); 
```

Enter fullscreen mode Exit fullscreen mode

这看起来有点恶心，但是对我来说很有效(一旦我重构了它，我会更新这篇文章)。至此，我们已经设置好了 indexedDb 数据库，别担心，我会介绍如何完成。

我删除了第一个条目，因为它通常是 Reddit 上的一些公告，而不是实际的帖子。

我要做的是创建另一个数组，我可以把我们将要使用的实际的、字面上的数据分散到这个数组中。我之所以这么做，是因为这在我头脑的自然数据流中有意义。

以确保我们没有重复的数据，或来自另一个子编辑的数据等。，我从当前数据库中删除了每一个帖子，然后保存到数据库中，这些数据是我们刚刚收到的。至此，一切都是最新的。

然后我保存了 subreddit，但这只是为了用户界面的目的，现在这样做有点夸张，但它是有效的。接下来，我更新我们的本地组件状态。这对过滤很有用。

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

这是我创建的保存数据的函数。为了控制我保存的内容及其格式，我创建了一个新的数组。因为我们在这一点上的数据是在一个“数据”对象中，我不想像那样保存它，必须键入“x.data.author(例如)”，我宁愿键入“x.author”。

“window.db”是我为了访问我的 Dexie 数据库而创建的一个全局变量的引用，在任何地方，随着我继续重构，这将在某个时间点发生变化，我将更新本文。

在这一点上，我们的文章目前正在显示，因为它们保存在状态，但我们刷新，或关闭我们的浏览器，并返回…它仍然不能工作…这是因为格式目前在。

如果我们退一步来看:

```
 posts.map(x => results.push(x.data)); 
```

Enter fullscreen mode Exit fullscreen mode

这是我们推送到我们州的数据。我们推送与数据库中的数据格式相同的结果数组。我们这样做的原因是，当组件加载时:

```
 useEffect(() => {
    getPostsFromDatabase(setPosts);
  }, []); 
```

Enter fullscreen mode Exit fullscreen mode

我们从数据库中提取数据，并将其加载到我们的状态中。

如果你想看看获取我们帖子的函数:

```
export const getPostsFromDatabase = async (setPosts) => {
  const db = window.db;
  const posts = await db.posts.toArray();
  return setPosts([...posts]);
} 
```

Enter fullscreen mode Exit fullscreen mode

这只是获取我们的 posts 集合中的数据，然后用这些数据设置我们的状态。

将我们的数据显示到页面上的最后一步是映射保存我们的帖子的州。我不想让您对我所做的感到困惑，但是正如我所说的，下一步是绘制州地图，并根据您的需要显示每条数据。

我希望这有助于为您指出构建自己的铲运机的正确方向！更多即将到来。