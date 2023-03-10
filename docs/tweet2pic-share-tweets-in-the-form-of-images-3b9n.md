# Tweet2Pic -以图片的形式分享推文

> 原文：<https://dev.to/haideralipunjabi/tweet2pic-share-tweets-in-the-form-of-images-3b9n>

我在这个博客上的第一篇文章，我认为最好是写我做过的最好的应用(代码方面)。

我在 2014 年加入 Twitter，但直到 2018 年才开始正确使用它(我创建了一些 Twitter 机器人，让我对 Twitter 感兴趣)。2018 年 8 月左右的某个时候，我想在 Instagram 上分享一些推文，并搜索了一个可以将推文转换为图像的应用程序。我什么也没找到，最后通过截图分享了它，但这个应用程序的想法一直困扰着我。

经过一番谷歌搜索，我偶然发现了卡梅隆·亚当斯的实验[截图一条推文](http://themaninblue.com/experiment/screenshot-a-tweet/) ( [相关博文](https://themaninblue.com/2018/03/26/screenshot-a-tweet/))。有了这个工具，我开始为 Android 开发一个类似的应用程序。

我使用了 Twitter 的 oEmbed API 来生成一个更好看的 tweet，我可以把它转换成图片。该 API 也有一些额外的功能，如*一个黑暗的主题，包括父线程，隐藏媒体*，这是我在应用程序中包括的。在 1.2 版本中，作为一个额外的特性，我增加了一个**方形图片**特性，生成一个方形图片(针对 Instagram)。

在最初发布的 3 周后，我发布了 1.4 版本(当前版本)，它增加了一些额外的特性并修复了一些错误。我不会深入每个 bug 的全部细节，但是会为阅读这篇博客的开发者写一个总结性的描述/修复。

*   1.1 版(在 1.0 后一天发布)增加了设置 UI(有选项可以记住黑暗主题、隐藏媒体、正方形图像等选项)，并增加了 15 种语言的翻译(有一天我可能会发一篇关于使用代码生成翻译的帖子)。
*   1.2 版(在 1.1 版后一天发布)增加了方形图片功能，并修复了如果使用不同的选项就无法保存同一条推文的多个副本的错误。我最初只使用 Tweet ID 作为文件名，但是为使用的每个选项添加了额外的文本。
*   1.3 版(1.2 版一周后发布)修复了图库无法显示使用应用程序下载的图片的大问题。我了解到，在添加/删除任何图像后，需要有计划地刷新图库。([所以帖子说帮了我](https://stackoverflow.com/questions/2170214/image-saved-to-sdcard-doesnt-appear-in-androids-gallery-app)
*   1.4 版(在 1.3 版 10 天后发布)增加了直接分享到 Instagram 故事的选项，并用复选框取代了 Switched(Reddit 上的人让我相信这是更好的 UX)

如果你是安卓用户，你应该试试我的应用。

[![Google Play Badge](img/36a0d9330742816ba257ac3838c105e6.png)](https://play.google.com/store/apps/details?id=org.hackesta.tweet2pic&pcampaignid=MKT-Other-global-all-co-prtnr-py-PartBadge-Mar2515-1)