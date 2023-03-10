# Jordan 使他的 HTTP 请求更加一致

> 原文：<https://dev.to/aarmora/jordan-makes-his-http-requests-more-consistent-2abb>

这篇文章的目的是了解为什么我在链接检查器中的很多[请求都返回了不准确的状态代码。当你有一个链接检查器试图确定它正在检查的链接是否有效时，有一个相当高的准确率是非常重要的。](https://javascriptwebscrapingguy.com/dead-link-checker-electronjs-v0-0-2ish/https://javascriptwebscrapingguy.com/dead-link-checker-electronjs-v0-0-2ish/)

我几乎所有的 http 网络抓取都使用的这个库已经被[请求](https://github.com/request/request)。我已经很适应了。这种不可靠性迫使我去别处看看是否能得到类似的结果。另一个我听说过很多的是 [axios](https://github.com/axios/axios) ，所以这是我第一次尝试。

## axios vs 请求-承诺…战斗

[![](img/116e22c0724c630648e7843b78b7b41c.png)](https://i.giphy.com/media/l42PAWcdSU44f3YA0/giphy.gif)T3】

```
// const url = 'https://www.sugarandcharm.com'; // axios 404, rp 404
// const url = `http://bethmichelle.com`; // axios 200, rp 403
// const url = 'https://www.courtneyssweets.com/'; // axios 200, rp 403
// const url = 'https://www.closetcooking.com/'; axios 200, rp 403
// const url = 'https://www.confessionsofaconfectionista.com/'; axios 200, rp 200
// const url = 'http://nutsforcooking.com/'; // axios 200, rp 200
// const url = 'http://javascriptwebscrapingguy.com/'; // axios 200, rp 200
// const url = 'http://citadelpackaging.com/'; // axios 200, rp 200
// const url = 'http://reddit.com/'; // axios 200, rp 200
// const url = 'https://amazon.com/'; // axios 200, rp 200
// const url = 'https://audible.com/'; // axios 200, rp 200
// const url = 'https://google.com/'; // axios 200, rp 200
// const url = 'https://reddit.com/r/funny'; // axios 200, rp 200
// const url = 'https://facebook.com/'; // axios 200, rp 200 
```

上面我列出了对不同网站的请求返回的状态代码。每个库至少尝试了五次，甚至更多。让我吃惊的是前四个请求的结果。相对于像 reddit、amazon、audible、google 和 javascriptwebscrapingguy.com[![😉](img/df3a1f8e822c13df03dc5619db176cac.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Z-U79kEY--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://s.w.oimg/core/emoji/11/72x72/1f609.png)这样的巨头，小型网站对请求库发出的请求返回 403，而 axios 会返回 200。

Axios 在实际的网站上并不完美(正如你看到的，https://www.sugarandcharm.com 用两个库返回了 404 ),但这是一个重大的改进。我真的认为在幕后，它们都将使用 nodejs 的 http 能力，并且真的更像中间件。这两者之间显然有些不同，但我不确定是什么。

## 客户端 vs 服务器端 http 请求

[![](img/502139c1075b84ae5936fc842bf689a5.png)](https://i.giphy.com/media/tvGOBZKNEX0ac/giphy.gif)

在我的测试表明这些请求与 axios 更加一致之后，我在我为链接检查器创建的[电子应用程序中使用了它。它立即因一个](https://github.com/aarmora/jordan-does-link-checking-electron/releases) [CORS 错误](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS/Errors)而失败。CORS 错误是浏览器用来保护用户免受恶意域名欺骗的一种错误。如果我试图调用一个不是发出请求的域，并且接收域也不期待它，浏览器将阻止该请求发生。

重要的是要认识到这个*只发生在客户端请求上。浏览器是实施这一点的工具。在使用请求库时，我从未遇到过这个问题，因为它调用 nodejs 脚本，然后 nodejs 脚本通过服务器端请求执行请求，而不利用浏览器。*

Axios 显然默认使用浏览器，如果它可用而 request 不可用的话。因为我知道发生了什么，我只是禁用了电子应用程序中的网络安全，然后请求开始呼叫，没有问题。

```
 win = new BrowserWindow({
    x: 0,
    y: 0,
    width: size.width,
    height: size.height,
    webPreferences: {
      nodeIntegration: true,
      webSecurity: false // This is what I added to the electron set up
    },
  }); 
```

也许这两者之间的区别也是当 request 不能成功调用时，axios 可以成功调用的原因。不管是什么原因，axios 已经被证明更可靠，这是我将来会使用的。

帖子 [Jordan 使他的 HTTP 请求更加一致](https://javascriptwebscrapingguy.com/jordan-makes-his-http-requests-more-consistent/)首先出现在 [JavaScript Web Scraping Guy](https://javascriptwebscrapingguy.com) 上。