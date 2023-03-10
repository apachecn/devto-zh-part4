# ID3TagEditor 和 Mp3ID3Tagger 的诞生以及我进入 ID3 标签标准的历程

> 原文：<https://dev.to/chicio/the-birth-of-id3tageditor-and-mp3id3tagger-and-my-journey-into-the-id3-tag-standard-2ob6>

这是我描述我不久前发表的两个开源项目的系列文章的第一篇:ID3TagEditor 和 Mp3ID3Tagger。在这篇文章中，我将谈谈为什么我开始开发它们。

最近我买了一辆新车。经过大量搜索，我最终决定购买雷诺 Clio 2017 款 1.5 dci。我喜欢这辆车。这是我前一辆车的一大进步。它最有趣的功能之一是它的媒体娱乐系统:媒体导航进化系统。该系统有一个 7 英寸的触摸屏，集成了地图和基本的智能手机，集成了 Siri 语音识别和电话呼叫支持。

[![media nav clio](img/fb82da81e716df1357ac6ab5f557c806.png "media nav clio")](https://res.cloudinary.com/practicaldev/image/fetch/s--r_rZ9Oj6--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.fabrizioduroni.it/asseimg/posts/media-nav-clio.jpg)

引起我注意的一件事是，我可以在开车的时候听我的 mp3(在我以前的车上，我有一个标准的 cd 播放器)。所以我准备了一个 u 盘，里面有我的一些 mp3，我开始听它们。我突然有了一个伟大的发现:我的一些歌曲显示在触摸屏上，上面有关于这张专辑的信息，而且它们显示的是这张专辑的封面！！！！！😍。我想:“哇，这太酷了！！我需要开始用这些信息填充我的 mp3。我想看看我每一个 mp3 的专辑封面！！！！！！!"。
这正是我开发 ID3TagEditor 和 and Mp3ID3Tagger 的旅程开始的时刻，但我还没有意识到这一点😁。
所以我坐在我的 MacBook Pro 前，打开 iTunes，开始给我的 mp3 文件添加标签。我把它们存在一个 u 盘里，然后去我的车里测试它们。结果如下:

[![mp3 no cover](img/18c42e7e0e96a86fa4a3a1a27cdbdac9.png "mp3 no cover")](https://res.cloudinary.com/practicaldev/image/fetch/s--HYpm5dJY--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.fabrizioduroni.it/asseimg/posts/mp3-song-no-cover.jpg)

到底是怎么回事？？！！？！😠标题和专辑显示在屏幕上，但封面没有显示。所以我回到我的办公桌，开始下载一些原生的 macOS 应用程序，让用户编辑我发现的叫做 *ID3 标签*的东西。没有一个像预期的那样工作。然后我找到了一个叫 Mp3Tag 的应用，一个在 macOS 上运行的使用 [Wine](https://www.winehq.org/) 的 Windows 应用。所以我下载了它，并试图标记一些 mp3。我把它们放在 u 盘上，然后...

[![mp3 with song cover](img/faad10f0debd77c54eb16814e8706f68.png "mp3 with song cover")](https://res.cloudinary.com/practicaldev/image/fetch/s--UacNcX3L--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.fabrizioduroni.it/asseimg/posts/mp3-song-with-cover.jpg)

MP3 标签工作正常😌。但后来我开始问自己:“其他本地 macOS 应用程序没有做的 Mp3Tag 在做什么？”。发现这一秘密背后的原因的唯一方法是将标记有 Mp3Tag 的 mp3 与标记有其他应用程序之一的 mp3 进行比较。所以我用我最喜欢的十六进制编辑器打开了一个标有 iTunes 的 mp3 和另一个标有 Mp3Tag 的 MP3，并对它们进行了比较...

[![mp3 compare itunes mp3tag](img/18966a2e4b56b55cf38b248071d72973.png "mp3 compare itunes mp3tag")](https://res.cloudinary.com/practicaldev/image/fetch/s--tS6v3NCt--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.fabrizioduroni.it/asseimg/posts/mp3-tag-bit-cover.jpg)

是的，一个字节就能带来很大的不同😮。事实是, [ID3 标准](http://id3.org/d3v2.3.0)接受多种类型的 mp3 附加图片:封面、封底、图标、艺术家照片等。在 ID3 标准的附加图片框架中，图片的类型被表示为紧跟在 MIME 类型之后的字节。
问题是 iTunes 和其他原生 MAC OS“MP3 tagger”应用程序不允许用户修改附加图片的类型。所有这些应用程序将字节设置为 ID3 标准中对应于*“其他”*封面类型的`0x00`。但是我的 Renault Clio 的 Media Nav Evolution 系统只能读取特定类型标签中插入的附加图片框，例如`0x03` *【封面(正面)】*，这是 Mp3Tag 插入的默认类型。具有`0x00` *【其他】*类型的附加图片框被丢弃😒。
我的下一个问题是:“怎么可能没有一个本地的 macOS 应用程序呢？只有跨平台/web 解决方案。我想要一个真正的苹果同性恋会很乐意使用的应用程序……”🍎😛。说实话，我没找到。所以我开始想:“我可以开发这个应用程序，使用一些我在过去几个月里学习的现代框架/编程范例...通过这种方式，我有机会创建我的第一个 macOS 应用程序，并将一些其他有趣的项目添加到我的 [Github 个人资料](https://github.com/chicio/)...通过这种方式，我可以在一个项目中工作，而不必在一个小时内启动命令`npm install`或`npm run build`上百次……”。经过两个月的努力，我们终于发布了:

*   `ID3TagEditor`，一个纯粹的 Swift 框架(只有苹果`Foundation`框架依赖)，读取/修改你的 mp3 文件的 ID3 标签，支持整个苹果生态系统(macOS，iOS，watchOS)和 Linux😍
*   `Mp3ID3Tagger`，一款用 Swift 编写的原生 macOS 应用，使用反应式编程范式，特别是其 Rx(反应式扩展)变体，框架为 RxSwift 和 RxCocoa (Rx？？？？！？！？！？什么？！？！？！？😰).

如果您仍有兴趣了解这两个项目的发展细节，您可以点击以下链接:

*   [ID3TagEditor:一个为 macOS、iOS、tvOS 和 watchOS 读写 mp3 文件 ID3 标签的 Swift 框架](https://www.fabrizioduroni.it/2018/05/08/id3tageditor-swift-read-write-id3-tag-mp3.html)
*   [Mp3ID3Tagger:一个原生的 macOS 应用程序，可以编辑你用 RxSwift 和 RxCocoa 编写的 mp3 文件的 ID3 标签](https://www.fabrizioduroni.it/2018/05/08/id3tageditor-swift-read-write-id3-tag-mp3.html)

[![mp3id3tagger id3tageditor](img/b16dd9ebffe05fe6560530e3c51acd04.png "mp3id3tagger id3tageditor")](https://res.cloudinary.com/practicaldev/image/fetch/s--tWS334qT--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.fabrizioduroni.it/asseimg/posts/mp3id3tagger-id3tageditor.jpg)

我希望你会看到我对这个项目投入了多少爱和激情，我也希望你会发现里面所有的技术细节都很有趣💖。

*原载于[https://www . fabrizioduroni . it](https://www.fabrizioduroni.it/2018/05/07/born-id3tageditor-mp3id3tagger.html)2018 年 5 月 7 日。*