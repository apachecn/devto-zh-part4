# 使用 Scrapy 通过 Genius 获取 Parcels 音乐的元数据

> [https://dev . to/openevufcg/使用 scrapy-to-get-metadata-das-music-dos-parcels-through-do-genius-1 dhj](https://dev.to/opendevufcg/usando-scrapy-para-obter-metadados-das-musicas-dos-parcels-atraves-do-genius-1dhj)

您是否曾希望从不提供 API 的服务中获取数据？如果你在 ufcg 进行计算，你可能听说过有人提到注册机器人。你问过他是怎么工作的吗？好吧，在今天的帖子中，你将学习如何做这些事情，并可以利用你的创造力去玩和进一步探索。

## 机器人先生

[![](img/04093e0f218a83cd5a9974547de563a9.png)](https://i.giphy.com/media/l0IyiADcZ3Ecjrn5m/giphy.gif)

**web crawler**可以定义为用于访问网站、提取内容和查找相关页面的脚本或程序，重复此过程直到不再需要搜索页面。
它们非常重要，因为搜索引擎使用它们来返回高效的结果；正因为如此，经常有人把他们称为网络博茨瓦纳。从表面上看，您可以将 crawler 视为一个小家伙，它会为找到的页面提出请求，并在您浏览这些页面时解析您的 HTML。

## 先决条件

要播放本教程中的内容，您需要配置 Python 3。另一种方法是使用 [virtualenv](https://www.digitalocean.com/community/tutorial_series/how-to-install-and-set-up-a-local-programming-environment-for-python-3) ，这样就可以创建一个孤立的开发环境。

除 Python 3 外，我们还将使用**【scrapy】**，这是一个具备必要工具的框架，用于**【提取】**网站数据，**处理**您想要的内容和**虽然您可以使用 Python 本身提供的模块构建 crawler，但随着您的项目的发展，管理您的小机器人的运行可能会变得复杂，因此我们将使用此框架。但是，如果您有兴趣了解其他替代方案，我将在参考资料中保留链接。**

要安装它，请通过以下命令使用 Python 包索引( [`PyPI`](https://pypi.org/) :

`pip install Scrapy`

这样，我们就可以开始实施了。

## 除了字母

如果使用 Spotify 的话，曾经看到过一些歌曲中出现过从[**【genius】**](https://genius.com/)中摘录的歌词和一些有趣的信息。从我最近认识的一个叫[**【parcels】**](https://open.spotify.com/artist/3oKRxpszQKUjjaHz388fVA)(包括提示:如果你喜欢*，你大概会喜欢他们的音乐)中得到这个信息，决定*

 *[![](img/c48a029af8a4618025983fa87962d3be.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--UqcNMU9w--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://spotifysupport.freetls.fastly.net/article-gallery/articles2/android/android_behind_the_lyrics.png)

crawler 首先需要的是一个起点，也称为“**seed**”将用于启动内容提取。就我们而言，这一点将是天才中的乐队页面([这里的](https://genius.com/artists/Parcels)。

一旦我们有了这个，我们就可以开始建造我们的机器人了。创建一个名为“`genius.py`”的 python 文件，其中包含以下内容:

```
import scrapy

class GeniusSpider(scrapy.Spider):
    name = "genius"
    start_urls = ['https://genius.com/artists/Parcels'] 
```

首先，我们导入**【scrapy】**以访问该模块提供的功能。接下来，我们创建了一个名为`Spider`的类，基于`Spider`的 scrapy 它定义了我们可以使用的方法，这些方法可以帮助我们运行 crawler。最后，我们把蜘蛛的名字定义为**天才**，把我们的*种子*定义为地块的页面。

与运行 Python 脚本不同，我们将使用 Scrapy 本身通过 CLI 提供的命令:

`scrapy runspider genius`

[![](img/7c8ec671444363d15fbddc721f0a98b5.png)](https://i.giphy.com/media/J5vyIVNU6PI2QVVMm7/source.gif)

好吧，我们有几个漂亮的床和一些不太多，但这是什么意思？

1.  Scrapy 装载并配置了启动所需的东西；
2.  要求我们在`start_urls`中定义的 url，并下载其内容；
3.  将此内容传递给 parse 方法。因为我们没有创造他，所以什么也没发生，他完成了处决。

## 提取元数据

下一步是将**定义为**scrapy 必须提取内容:我们通过定义**剖析**的方法来做到这一点。在此步骤中，了解页面元素的排列方式非常重要，因为这对于告知 crawler 提取页面时必须“T4”刮去“”的位置至关重要。

[![](img/6b7a98c4c899f0c98ef4bbdc9cd74f63.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--rcVbpnbk--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/YJ9G6sg.png)

从上面的图片中可以看出，在你的右边有一张卡片列表，上面有流行歌曲，点击后，我们就可以找到我们想要的内容。因此，这应该是 crawler 检索链接的位置。

Scrapy 使用与文档树中的元素匹配的“图案”或“模板”选择器提取内容，因此可以使用这些选择器选择 HTML 文档中的节点。为了做到这一点，Scrapy 提供了两种方法:通过 Xpath 和 CSS 选择器；为了简单起见，我们将使用 CSS 选择器。

使用浏览器检查器分析哪些节点包含此链接，然后形成我们的选择器，我注意到包含此卡列表的元素是 div。参见 no GIF:

```
<div class="mini_card_grid-song">
  <a href="...">...</a>
</div> 
```

[![](img/6e10fb951a97b460a832053e3e0013cc.png)](https://i.giphy.com/media/IfgHqsThQhDxkhy6C5/source.gif)

因此，我们可以通知**【scrapy】**他必须得到类似以下的东西:

`div.mini_clard_grid-song a::attr(href)`

这表示我们想要的链接是类元素“`mini_card_grid-song`”的子项，因此“`.`”(如 CSS 中所示)。此外，我们在“`a`”标记之后添加了此代码段“`::attr(href)`”，因为如果只使用该标记设置模式，则会显示整个 HTML 节点，而不仅仅是 url。

要更好地了解 CSS 选择器，请参阅文档中的“[”此链接“](https://docs.scrapy.org/en/latest/topics/selectors.html)”。

因此，我们可以构建自己的方法:

```
 def parse(self, response):
        songs_urls = response.css('div.mini_card_grid-song a::attr(href)').getall()
        for song_url in songs_urls:
            yield scrapy.Request(url=song_url, callback=self.parse_lyrics_page) 
```

方法由参数 **response** 组成，指示 crawler 请求我们的初始 url 后获取的内容。这样，我们就有了从我们的选择器中获取的音乐的 urlscrawler 需要的 URL 列表称为“**边界”**。重要的是要指出，我们使用了`getAll()`，因为我们想提取**【全部】**符合我们通过的模式的选择者，但有时只对第一次出现感兴趣，可以使用`get()`。一旦我们有了 url，我们就通过传递 url 和回调**来进行请求，回调是 crawler 下载我们传递的 URL 后将执行的功能。**

太好了，我们能找到我们的歌页但是一旦我们找到了，我们该怎么办？我们遵循的过程与前面的过程非常相似，只是不同的是，我们可以提取所需的信息，而不是尝试提取链接。从上一步中，我们看到在浏览完音乐网页后要执行的方法是‘t0’，然后将这段添加到您的文件中:

```
 def parse_lyrics_page(self, response):
        title = response.css('div.song_body-lyrics h2::text').get()
        song_metadata = response.css('div.rich_text_formatting p::text').getall()
        artists = set(response.css('span.metadata_unit-info a::text').getall())
        lyric = response.css('div.lyrics p::text').getall()
        annotations_ids = response.css('a::attr(annotation-fragment)').getall()

        item = {
            'title': title,
            'artists': artists,
            'lyric': lyric,
            'metadata': song_metadata,
            'snippet_lyric': [],
            'annotations': []
        }

        if annotations_ids:
            return self.get_annotations(response, annotations_ids, item)
        else:
            return item

    def get_annotations(self, response, annotations_ids, item):
        for annotation_id in annotations_ids:
            url = urljoin(response.url, annotation_id)
            yield scrapy.Request(
                url=url,
                callback=self.parse_annotation_page,
                meta={'item': item}
) 
```

很多，不是吗？但我们还是走吧，就像杰克说的。

对于提取艺术家、字母、标题和元数据等基本信息，我们在提取链接方面没有什么新进展，因为我们只需要提供选择器，他就会得到信息。

但是，标注的行为不同。查看页面时，您会发现应该包含歌曲注释信息的元素只包含一个标识符，该标识符将重定向到包含歌曲内容的另一个页面。所以我们所做的是:

如果有注释，请获取您的 id，并为每个获得的 id 链接我们的初始 url:

`urljoin(response.url, annotation_id)`

然后我们向他们每个人提出申请。但是等一下，这个申请有一个我们以前没见过的东西:一个叫`meta`的参数。

[![](img/23f3115b7b944f084f66ccb588d90724.png)](https://i.giphy.com/media/a5viI92PAF89q/giphy.gif)

`meta`是 Scrapy 为我们提供的在页面之间进行交流的方式。所以我们想说的是，每次他查阅笔记的页面，他都会带走我们从其中提取的信息。这将有助于合并两个对象并返回结果。

现在您位于注释页面上，找到注释选择器并返回结果，然后您就可以进行 crawler 了。应保留如下片段所示的东西:

```
 def parse_annotation_page(self, response):
        snippet_lyric = response.css("meta[property='og:title']::attr(content)").getall()
        annotations = response.css("meta[property='og:description']::attr(content)").getall()
        item = response.meta['item']
        item['snippet_lyric'] = snippet_lyric
        item['annotations']= annotations

    return item 
```

如果没有注释，它将立即返回结果，这是包含我们在第一页上获得的信息的对象。为了保存我们收集到的东西在一个`json`中，我们使用命令:

`scrapy runspider genius -o parcels-lyrics.json`

你会得到一些与:*