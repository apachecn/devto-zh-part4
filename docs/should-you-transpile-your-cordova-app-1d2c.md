# 你应该转存你的科尔多瓦应用程序吗？

> 原文：<https://dev.to/johnnymakestuff/should-you-transpile-your-cordova-app-1d2c>

以下是根据真实故事改编的。

## 刚刚最坏

你已经完成了令人惊叹的跨平台移动应用，你将它发布到商店，然后你得到了这样的评价:

[![bad-review1](img/7996033447c6c913c0caf5f8c47a7dd8.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ltDnSesZ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.learningsomethingnew.com/cordova-babel/bad-review1.png)

嗯（表示踌躇等）..但它可以在我的手机上打开！所以你仔细看看:

[![bad-review2](img/2719fb5a7a9601c45e264b9b8bbecbd0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--R1xiHhZg--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.learningsomethingnew.com/cordova-babel/bad-review2.png)

哦对了，我们从没在旧手机上查过。可恶。让我们在 Android 4.4 模拟器上试一试:

[![error](img/971f69c687cce719ea82709c5bdf6fdb.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--66UhUleb--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.learningsomethingnew.com/cordova-babel/bundle-js-error.png)

因此，快速搜索一些错误就能发现问题所在。我们使用了所有很酷的新 ES6 语法，但是 WebView 太旧了，不支持 ES6。

## 这种情况发生的可能性有多大？有什么影响？

因此，您使用 ES2015+语法的应用程序无法在旧设备上运行。但是多大了？有多少？

为了评估损失，我们需要一些数据。我们基本上是在问 Android 和 iOS 系统 WebView 组件中新浏览器**的采用率是多少。但是 WebView 实际上运行的是什么浏览器呢？这因操作系统和操作系统版本而异，所以让我们来看看两种主要的移动操作系统。**

### 1。机器人

Android 中使用的 WebView cordova 应用程序是系统 WebView 组件。近年来，Android WebView 的工作方式发生了两大变化:

1.  从 Android Lollipop 开始，WebView [通过 app store](https://developer.chrome.com/multidevice/webview/overview#will_the_new_webview_auto-update_) 自动更新。因此，运行 Android 5.1 的设备将支持新的 ES 功能(只要用户实际批准 WebView 应用程序的更新)。

2.  从 Android 7(牛轧糖)开始， [Chrome 和 WebView 是一样的](https://developer.android.com/about/versions/nougat/android-7.0#webview)。这意味着 cordova 使用的 WebView 与 Chrome 应用程序是同一版本，没有必要更新“系统 WebView”应用程序。

> 如果你觉得无聊，可以看看 play store 上对“系统 WebView”应用的一些评论:

[![webview-review](img/87b6967b8107b953a2712a470d45b78b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--HeBf623b--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.learningsomethingnew.com/cordova-babel/webview-review.png)

对于 L 以下的 Android 版本**，WebView 是操作系统的一部分，不更新操作系统就不会更新。
看看 Android 版本的市场份额数字，我们发现我们并不真的需要担心 L 以下的 Android 版本，因为它们占所有设备的约 3%:**

[![android-versions](img/955d05c0364b31b072be97d8587468d5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--f1XmcM4n--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.learningsomethingnew.com/cordova-babel/android-versions.png)

如果你还想迎合这~3%，或者你的应用运行在无法升级的特定专有设备上，那么你有两个选择:

1.  Transpile 一直到 chrome 版本 30(或者更低，如果你需要支持 Android 4.4 以下)

2.  使用废弃的人行横道插件，该插件在您的 apk 中提供 WebView *。它不再被维护(因为上面提到的改变使它变得毫无用处)，如果你使用新的 cordova CLI(第 9 版)，它根本不会被安装。如果你真的必须支持旧设备，我已经设法找到了一个项目的[分支](https://github.com/waitaction/cordova-plugin-crosswalk-webview.git)，它实际上与新的 cordova 版本一起工作，但它可能会在下一个版本中停止工作:)*

### 2。ios

iOS 8 和 Yosemite 推出了新的 WKWebView，它现在废除了旧的 UIWebView(在 nshipster 上这篇非常详细的[帖子中阅读更多信息)。新的 WKWebView 运行与 Safari 相同的 JS 引擎，所以这基本上与 Android 7 对 Chrome 和系统 WebView 所做的改变相同。](https://nshipster.com/wkwebview/)

请注意，在新的 cordova 项目中(目前是版本 9)，cordova 仍然使用旧的和废弃的 UIWebView，所以建议在新的应用程序中升级到 WKWebView。科尔多瓦发布的[公告中有更多相关信息](https://cordova.apache.org/news/2018/08/01/future-cordova-ios-webview.html)

所以，如果我们使用 iOS 8 中引入的 WKWebView，我们会损失多少用户？根据这个，不到 2.3%(版本 8 甚至没有列出):

[![ios-versions](img/c6354a04f658411c9ec5055338edf327.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--hwGyxJ7U--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.learningsomethingnew.com/cordova-babel/ios-versions.png)

如你所见，iOS 的问题没有 Android 严重。这是生产操作系统的公司也生产硬件的额外好处。

### 如何传输文件

我们不能在不谈论巴别塔的情况下谈论运输。
来自巴别塔[的文件](https://babeljs.io/docs/en/):

> Babel 是一个工具链，主要用于将 ECMAScript 2015+代码转换为当前和旧版本浏览器或环境下的向后兼容版本的 JavaScript。

这是一个非常酷的项目，它让我们可以在最新的语言特性被所有浏览器采用之前使用它们。但是您会注意到，至少可以说，巴别塔配置是非常重要的。

幸运的是，现在大多数框架都预配置了 babel，所以如果你生成一个 Vue CLI 3 应用，它会附带一个 webpack 配置来使用 babel。

如果您正在使用一个更新的/不太为人所知的框架，请尝试在 github 上找到一个具有 babel 配置的示例项目。

> 如果你正在使用 Svelte，你可以查看我的帖子，关于如何为 svelte cordova 应用程序配置 babel with rollup】这里。

## 那么，我应该转存我的 cordova 应用吗？

是的你应该。这也是为什么你希望你的 web 应用程序被移植的原因(主要浏览器的最后两个版本是一个很好的标准，但这取决于你的应用程序的客户)。

作为一名开发人员，我总是喜欢使用最新的语言特性，而且我永远不确定我的最终客户的浏览器会支持什么。我不认为留下来仅仅使用已经被广泛支持的语言特性是一个好主意。语言的改进使得开发更快，我们的代码更好。幸运的是，您可以使用 babel 的 [preset-env](https://babeljs.io/docs/en/babel-preset-env#targetsbrowsers) 并使用查询选择浏览器来定义您的目标浏览器(例如:最近 2 个版本，> 5%，等等)。

如果 babel 确保我们的代码可以在所有主流浏览器的最后两个版本上运行，并且我们知道绝大多数 Android 和 iOS 的 WebViews 都是作为操作系统的一部分自动更新的，那么我们就几乎覆盖了 99%。

最后，这里有一个决策树来帮助你决定:

[![decision-tree](img/f13608d913acdab3c0ea4500cccdd2d3.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--8-0RWPyu--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.learningsomethingnew.com/cordova-babel/decision-tree.jpg)