# 在 GitHub 页面中发布一个带有自定义子域的 React 应用

> 原文：<https://dev.to/danivijay/the-lazy-man-s-journey-to-publish-an-react-app-in-github-pages-with-custom-subdomain-43li>

注意:如果你对背景故事不感兴趣，可以跳过下一段。

从上周开始，我每天学习一个单词，并通过推特([https://twitter.com/UtmostDev/status/1156447349373194240](https://twitter.com/UtmostDev/status/1156447349373194240))记录下来。几个帖子之后，我意识到，我选择的平台是错误的。由于我不是一个经常发微博的人，这将会慢慢地支配和污染我的信息。但是我不打算在短期内放弃#AWordEveryDay。我唯一的选择是为它找一个新家。我从零开始开发网站已经有一段时间了，我选择尽快做同样的事情作为复习。

我明显的选择是`create-react-app`。制作网站非常简单，大约花了三个小时才准备好初稿([https://github.com/danivijay/AWordEveryDay](https://github.com/danivijay/AWordEveryDay))。

由于我的代码位于 GitHub 中，下一步是搜索如何使用 GitHub 页面部署 react 应用程序。然后真正的问题开始了。有大量的教程一遍又一遍地说着同样的事情——使用 GH-pages([https://www.npmjs.com/package/gh-pages](https://www.npmjs.com/package/gh-pages))。但在撰写本文时，该包并没有像预期的那样工作([https://stack overflow . com/questions/57314473/the-file-argument-must-be-of-type-string-received-type-undefined-on-NPM-run-d](https://stackoverflow.com/questions/57314473/the-file-argument-must-be-of-type-string-received-type-undefined-on-npm-run-d))。即使降级也不能解决问题(可能是缓存问题)。但是经过一些研究，我意识到我们也可以直接从主文件夹的`docs`中进行部署。将`build`改名为`docs`，推送后，我的应用程序在几分钟内就启动并运行了！！

第二个问题是将我的子域路由到 web 应用程序。发现路由我的子域很简单，只需添加一条 CNAME 记录，名字为子域名(awordeveryday)和 domain . github . io .(danivijay . github . io .-结尾需要点号)。最初页面显示了一个证书错误，但是匿名打开解决了这个问题(又是缓存问题？).

最后但并非最不重要的是，我注意到设置中的自定义域配置在我每次推送时都会重置。在我的 docs 文件夹(我正在发布)中添加一个名为`CNAME`的带有我的自定义域(awordeveryday.danivijay.com)的文件解决了这个问题。

网站在[https://awordeveryday.danivijay.com](https://awordeveryday.danivijay.com)开始运行，可以在[https://github.com/danivijay/AWordEveryDay](https://github.com/danivijay/AWordEveryDay)找到源代码