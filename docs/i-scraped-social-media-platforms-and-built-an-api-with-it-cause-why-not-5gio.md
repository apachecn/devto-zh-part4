# 我开发了社交媒体平台，并用它建立了一个 api，为什么不呢🤷‍♂️

> 原文：<https://dev.to/alaadesouky/i-scraped-social-media-platforms-and-built-an-api-with-it-cause-why-not-5gio>

> 首先，我绝不是专业的软件工程师，所以这不会是你会看到的最干净的代码。我用这篇博客来记录我的编码过程，分享我的想法和我解决问题的方法，也作为对我做错/做对事情的反馈。

这个项目的灵感来自于韦斯博斯的 [Twitter 和 Instagram 抓取](https://www.youtube.com/playlist?list=PLu8EoSxDXHP4xVFdNGakvzJ1OUQ6S_3l3)项目。

你可以在这里找到回购:[状态-刮刀](https://github.com/AlaaDesouky/status-scraper)

### 那么，它到底是做什么的呢？

它是一个 api，接受一个`social media flag`和一个`username`并返回用户状态(例如，关注者、跟随者、帖子、喜欢等的数量)...).

端点为`/scrape/:flag/:username`，目前`:flag`可以是以下任意一种:

*   `t`=>twitter.com
*   `r`=>reddit.com
*   `g`=>github.com
*   `b`=>behance.net
*   `q`=>quora.com
*   `i`=>instagram.com

因此，对`https://statusscraperapi.herokuapp.com/scrape/t/mkbhd`的调用将返回以下响应:

```
{  user:  "mkbhd",  status:  {  twitterStatus:  {  tweets:  "45,691",  following:  "339",  followers:  "3,325,617",  likes:  "25,255"  }  }  } 
```

## 使用的技术

*   结节
*   esm，一个 ECMAScript 模块加载器
*   表达
*   阿克斯
*   再见

## 服务器配置

```
// lib/server.js
const PORT = process.env.PORT || 3000;
app.listen(PORT, () => console.log(`Server running on port ${PORT}`));

// lib/app.js
class App {
  constructor(app, routePrv) {
    this.app = express();
    this.config();
    this.routePrv = new Routes().routes(this.app);
  }

  config() {
    this.app.use(cors())
    this.app.use(helmet());
  }
}

export default new App().app; 
```

## 项目结构

该应用有三个模块:

### 模块 1 -路由器:

```
// lib/routes/router.js

// all routes have the same structure
export class Routes {
  routes(app) {
    ....
    // @route  GET /scrape/g/:user
    // @desc   log github user status
    app.get("/scrape/g/:user", async (req, res) => {
      const user = req.params.user;
      try {
        const githubStatus = await Counter.getGithubCount(
          `https://github.com/${user}`
        );
        res.status(200).send({ user, status: { githubStatus } });
      } catch (error) {
        res.status(404).send({
          message: "User not found"
        });
      }
    });
    ...
  }
} 
```

### 模块 2 -计数器:

*   充当路由和音频采集之间的中间件。
*   它获取 html 页面并将其传递给 scraper 模块。

```
// lib/scraper/counter.js
class Counter extends Scraper {
  ...
  // Get github count
  async getGithubCount(url) {
    const html = await this.getHTML(url);
    const githubCount = await this.getGithubStatus(html);
    return githubCount;
  }
  ...
}

export default new Counter(); 
```

### 模块 3 -刮刀:

所有的工作都在这里完成，我将解释每一种社交网络方法。让我们开始吧。

#### 碎碎念

Twitter 响应有多个包含我们想要的所有数据的`<a>`元素，看起来像这样:

```
<a class="ProfileNav-stat ProfileNav-stat--link u-borderUserColor u-textCenter js-tooltip js-nav" title="70 Tweets" data-nav="tweets" tabindex=0>
  <span class="ProfileNav-label" aria-hidden="true">Tweets</span>
  <span class="u-hiddenVisually">Tweets, current page.</span>
  <span class="ProfileNav-value"  data-count=70 data-is-compact="false">70</span>
</a> 
```

对于这些元素来说，类`ProfileNav-stat--link`是唯一的。
使用 cheerio，我们可以简单地用类获取所有的`<a>`，遍历它们，并提取`title`属性的数据。
现在我们有了`"70 Tweets"`，只需将其拆分并存储为一个键-值对。

```
// lib/scraper/scraper.js

// Get twitter status
async getTwitterStatus(html) {
  try {
    const $ = cheerio.load(html);
    let twitterStatus = {};
    $(".ProfileNav-stat--link").each((i, e) => {
      if (e.attribs.title !== undefined) {
        let data = e.attribs.title.split("  ");
        twitterStatus[[data[1].toLowerCase()]] = data[0];
      }
    });
    return twitterStatus;
  } catch (error) {
    return error;
  }
} 
```

#### Reddit

Reddit 用户页面右侧有一个`<span id="profile--id-card--highlight-tooltip--karma">`写着用户的总业力，所以非常容易获得。但是当悬停在上面时，它会显示帖子/评论因果报应。

Reddit 响应有一个`<script id="data">`，包含嵌套在一个对象中的这两段数据。

```
window.___r = {"accountManagerModalData":....
...."sidebar":{}}}; window.___prefetches = ["https://www....}; 
```

只需提取`<script>`数据并将它们解析成 json。但是我们需要去掉开头的`window.___r =`，结尾的`; window.___prefetches....`，以及后面的所有东西。

这可能是:D 有史以来最懒/最糟糕的事情了
我基于“=”进行了拆分，计算了从那个`;`开始的字符数——当然是用一个网络应用程序——并将它们从字符串中分割出来。现在我有一个字符串中的纯对象。

```
// lib/scraper/scraper.js

  // Get reddit status
  async getRedditStatus(html, user) {
    try {
      const $ = cheerio.load(html);
      const totalKarma = $("#profile--id-card--highlight-tooltip--karma").html();

      const dataInString = $("#data").html().split(" = ")[1];
      const pageObject = JSON.parse(dataInString.slice(0, dataInString.length - 22));
      const { commentKarma, postKarma } = pageObject.users.models[user];

     return {totalKarma, commentKarma, postKarma};
    } catch (error) {
      return error;
    }
  } 
```

#### 领英

它以状态码 999 回应！就像，真正的 linkedin。

我试着发送一个定制的 head 请求，这个请求对每个人都有效，但是对我无效。和`csrf-token`有关系吗？我不太确定。
无论如何，这是一个死胡同，继续下一个。

#### Github

这一个相当简单，有五个`<span class="Counter">`显示了存储库、星星等的数量..遍历它们来提取数据，用`Cheerio`我可以得到元素的父元素，这是一个`<a>`，它包含这些数字所代表的内容。将它们存储为键值对，我们就可以开始了。

```
// lib/scraper/scraper.js

 // Get github status
  async getGithubStatus(html) {
    try {
      const $ = cheerio.load(html);
      const status = {};
      $(".Counter").each((i, e) => {
        status[e.children[0].parent.prev.data.trim().toLowerCase()] = e.children[0].data.trim();
      });
      return status;
    } catch (error) {
      return error;
    }
  } 
```

#### 行为

也是一个简单的例子，一个包含所有所需数据的对象的`<script id="beconfig-store_state">`。将其解析成 json 并提取它们。

#### Youtube -你伤了我的心

Youtube 的回应非常混乱，它有一大堆没有任何 id 或类别的标签。我想获得该频道的订户数量和总视频浏览量，两者都可以在`About`选项卡中找到。

期望的`<script>`与`Github`相似，我可以使用同样的`split, slice, parse`东西，我就完成了。

但是，这两个简单的数字在对象中嵌套了 12 层，并且涉及到数组，这基本上是地狱。

因此，我编写了一个小的助手函数，它接受大的 JSON/object 和要提取的对象键，并返回所有匹配的数组。

```
// lib/_helpers/getNestedObjects.js

export function getNestedObjects(dataObj, objKey) {
  // intialize an empty array to store all matched results
  let results = [];
  getObjects(dataObj, objKey);

  function getObjects(dataObj, objKey) {
    // loop through the key-value pairs on the object/json.
    Object.entries(dataObj).map(entry => {
      const [key, value] = entry;
      // check if the current key matches the required key.
      if (key === objKey) {
        results = [...results, { [key]: value }];
      }

      // check if the current value is an object/array.
      // if the current value is an object, call the function again.
      // if the current value is an array, loop through it, check for an object, and call the function again.
      if (Object.prototype.toString.call(value) === "[object Object]") {
        getObjects(value, objKey);
      } else if (Array.isArray(value)) {
        value.map(val => {
          if (Object.prototype.toString.call(val) === "[object Object]") {
            getObjects(val, objKey);
          }
        });
      }
    });
  }

  // return an array of all matches, or return "no match"
  if (results.length === 0) {
    return "No match";
  } else {
    return results;
  }
} 
```

虽然我很高兴`getNestedObjects`真的起作用了——[试试](https://github.com/AlaaDesouky/getNestedObjects)，但是这并没有持续很久。
不知何故，收到的 html 不包含那个`<script>`，我不知道为什么。我检查了它是否有号码，但是一个死胡同。
谢谢，youtube。

#### Quora

响应有多个`<span class="list_count">`，和 Github 一模一样。

#### Instagram

从字面上看，以上每个问题的答案都有问题:

*   ✅多个`<script>`标签具有相同的`type="text/javascript"`
*   ✅ `split, slice, parse`
*   ✅:数字和物体嵌套得很深

```
 // Get instagram status
  async getInstagramStatus(html) {
    try {
      const $ = cheerio.load(html);
      // get the script containing the data
      let script;
      $('script[type="text/javascript"]').each((i, e) => {
        if (e.children[0] !== undefined && e.children[0].data.includes("window._sharedData =")) {
          return (script = e.children[0].data);
        }
      });

      // get json fromat string
      const dataInString = script.split(" = ")[1];

      // convert to json object
      const pageObject = JSON.parse(dataInString.slice(0, dataInString.length -1));

      // extract objects with status
      const [{ edge_followed_by }] = getNestedObjects(pageObject, "edge_followed_by");
      const [{ edge_follow }] = getNestedObjects(pageObject, "edge_follow");
      const [{ edge_owner_to_timeline_media }] = getNestedObjects(pageObject, "edge_owner_to_timeline_media");

      return {
        followers: edge_followed_by.count,
        following: edge_follow.count,
        posts: edge_owner_to_timeline_media.count
      };
    } catch (error) {
      return error;
    }
  } 
```

至少我用到了助手。

### 总结归纳

这是一个很酷的项目，我学到了很多东西。
我还用`React`和`Next`创建了一个与 api 交互的前端应用，你可以在这里查看:[状态记录器](https://status-logger.alaadesouky.now.sh/)
也许我以后会为它写一篇博文。

同时，请随意分享你的意见，无论是好是坏。此外，如果你有任何其他社会媒体网络刮。