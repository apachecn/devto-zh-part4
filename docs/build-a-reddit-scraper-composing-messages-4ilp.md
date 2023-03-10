# 构建 Reddit 刮刀:撰写邮件

> 原文：<https://dev.to/imtyrelchambers/build-a-reddit-scraper-composing-messages-4ilp>

今天我们来谈谈使用 JavaScript 和 React 通过 Reddit API 编写和发送消息(这是好的 SEO 吗？).

这需要用户使用他们的帐户对你说“ok ”,事实上，我所说的大部分都是基于这个。

因此，让我们假设他们说“ok ”,并且您已经设置了您的表单，我们需要做的就是访问该信息并使用 API 发送消息。

这个帖子的上下文将在我们的确认消息组件中。这是我们选择文章并准备发送消息的部分。

## **创建我们的提交处理程序**

我们将进行 post 调用的链接是:

```
https://oauth.reddit.com/api/compose 
```

Enter fullscreen mode Exit fullscreen mode

因为我们最初使用 OAuth 方法进行认证，所以我们需要调用 OAuth 子域端点`api/compose`。

棘手的部分是你不能像平常一样发送一个 post 主体请求，而是我们必须创建一些 FormData。这是我最后让它工作的方式，也许有更好的方式！

让我们来分析一下这个函数。

```
export const sendMessageToAuthors = async (author, subject, message, removeMessagedAuthor) => {
  const tokens = await fetchTokens().catch(err => false);
  const fmtSubject = subject;
  const link = `https://oauth.reddit.com/api/compose`;

  if (!tokens || !author) return toast.error("Something went wrong");
  if (!message ) return toast.error("A messaged is needed to send");
  if ( !fmtSubject ) return toast.error("A subject is needed");
} 
```

Enter fullscreen mode Exit fullscreen mode

我之所以将 Reddit 的令牌存储在数据库中，是因为我现在可以随时随地获取它们。我想如果本地存储被清空，或者这个那个会发生什么。至少我知道这些令牌总是最新的，因为每当加载应用程序时，应用程序都会获取数据库中的 refresh_token，向 Reddit 请求新的 access_token，然后通过将保存在 localstorage 中的 JWT 令牌传递给数据库来保存它。

因此，我们调用该函数来获取我们的令牌，我们将使用这些令牌与 Reddit 握手，也就是说，我们将使用它向 Reddit 显示我们已经过身份验证。

因为发送信息的能力是一种需要高度责任感的能力，所以检查必须是有针对性的。如果丢了什么，我们就回来，如果有什么是假的，我们就回来。这将防止我们发送标题为`undefined`的消息。

#### **格式化我们的表单数据**

该函数的下一部分将被添加到我们的验证检查之下，它是:

```
 const body = new FormData();
  body.set('to', `/u/${author}`);
  body.set("subject", fmtSubject);
  body.set("text", message);
  await Axios.post(link, body, {
    headers: {
      "Authorization": `bearer ${tokens.access_token}`,
      "Content-Type": "application/x-www-form-urlencoded"
    }
  })
  .then(res => {
    toast.success(`Message sent to ${author}`)
    removeMessagedAuthor();
  })
  .catch(console.log); 
```

Enter fullscreen mode Exit fullscreen mode

如果我们的任何变量为空，我们立即返回并逃跑。

否则，我们开始设置表单数据。根据 Reddit API，你的用户名在技术上是一个“subreddit ”,我对此感到困惑。这就是为什么我们需要在实际作者的名字前加上`/u/`。我假设它代表用户的端点，但在文档中被称为子编辑。

在获取令牌的原始请求中，我们没有像以前那样进行加密，而是将 access_token 作为承载令牌。`x-www-form-urlencoded`部分也很重要。

为什么这么问？因为它将我们的`author`、`subject`和`message`作为键/值对发送，每个键/值对由`&`分隔，并使用`=`分配。更多关于 MDN 的信息:[https://developer . Mozilla . org/en-US/docs/Web/HTTP/Methods/POST](https://developer.mozilla.org/en-US/docs/Web/HTTP/Methods/POST)。

这只是告诉服务器我们正在发送什么类型的数据的另一种方式；结构是什么。

祝酒词只是发出一个通知，删除作者只是我个人的想法。它所做的是，它会自动从所有选定邮件列表中删除您发送邮件的作者。这对于快速发送消息很有用。然而在你发送信息之前，点击下一个，发送，下一个等等。，现在它会自动转到下一个，最终将索引重置为 0(稍后会详细介绍)。

#### **将作者保存到数据库**

以防你感兴趣。对于这个特定用例中的 Reddit scraper，我将作者保存到数据库中，因为我们的应用程序将自动在两个不同的已保存消息之间进行选择，一旦我们到达帐户页面，我将向您展示这两个消息。

```
export const saveAuthorToDb = async (author, postId)=> {
  const token = window.localStorage.getItem("token");
  await Axios.post(`${process.env.REACT_APP_BACKEND}/api/profile/saveAuthors`, {
    author,
    postId
  }, {
    headers: {
      token
    }
  })
  .then()
  .catch(console.log);
} 
```

Enter fullscreen mode Exit fullscreen mode

我们将作者的名字发送到我们的后端并保存它。然后，当我们检索它时，我们可以与当前显示的作者的作者进行交叉引用，并从那里开始。

```
const messageHandler = () => {
    let authorExists = false;

    userProfile.authorsMessaged.map(x => x === data.author ? authorExists = true : null);

    if ( authorExists ) {
      setDefaultMessage(userProfile.altMessage);
    } else {
      setDefaultMessage(userProfile.defaultMessage);
    }
  } 
```

Enter fullscreen mode Exit fullscreen mode

#### **处理我们的组件状态**

```
 const [ defaultMessage, setDefaultMessage ] = useState("");
  const [ subject, setSubject ] = useState("");
  const [ redditProfile, setRedditProfile ] = useState({});

  useEffect(() => {
    setSubject(data.title);
    const profile = JSON.parse(window.localStorage.getItem("reddit_profile"));

    setRedditProfile({...profile});
  }, [data.title]);

  useEffect(() => {
    messageHandler();
  }, [data]); 
```

Enter fullscreen mode Exit fullscreen mode

我之所以这样写状态，是因为如果你需要对当前的标题或消息体进行调整，你可以这样做，这不会影响下一篇文章。它会预先填充您创建的消息，或者，您可以添加您自己的消息。

useEffect 只是在应用装载时以及 data 或 data.title 更改时更新我们的状态(您可以引用前面代码片段中的 messageHandler 函数)。

差不多就是这样！只需按照您的喜好构建 UI，这些信息将有助于您发送消息！

在撰写本文时，我还没有按钮上的加载程序。我推荐这个。当有人发送消息时，加载器应该替换按钮。对我来说，这是一个很好的 UX，通过去掉按钮，它可以防止垃圾邮件。

我希望你喜欢这篇文章，现在去用 JavaScript 和 React(我想是更多的 SEO)让自己成为一个了不起的 Reddit 抓取者吧。

再见！