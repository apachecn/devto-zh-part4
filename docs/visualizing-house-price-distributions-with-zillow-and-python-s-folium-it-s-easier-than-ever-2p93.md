# 可视化房价分布

> 原文：<https://dev.to/aagnone3/visualizing-house-price-distributions-with-zillow-and-python-s-folium-it-s-easier-than-ever-2p93>

##### *有了 Zillow 和 python 的叶子，比以前更容易了*

### 等等，可是为什么呢？

我正在关闭我在佐治亚州亚特兰大的第一个家，并一直在大量使用各种房地产网站，如 Zillow，Redfin 和 Trulia。我也一直在摆弄 Zillow 的 API ，尽管在功能和文档方面有些参差不齐。尽管它有缺点，但当我阅读了 [Lukas Frei](https://medium.com/u/5164378fc848) 关于使用`folium`库无缝创建基于地理的可视化的[帖子](https://towardsdatascience.com/rat-city-visualizing-new-york-citys-rat-problem-f7aabd6900b2)后，我受到了充分的启发。几天后，我结合 Zillow 和 leav 制作了一些亚特兰大和美国各地房价的酷炫视觉效果。

### 主题

*   API 集成
*   图形遍历
*   形象化

### 一个小小的工作例子

让我们从使用一些我从 Zillow 网站下载的[预汇总数据](https://github.com/aagnone3/zillium/blob/master/data/State_MedianValuePerSqft_AllHomes.csv)开始。该数据集显示了美国每个州从 1996 年 4 月到 2019 年 5 月每个月的平方英尺价格中位数。自然，人们可以对这些价格随时间的发展建立丰富的可视化；然而，现在让我们坚持使用最新的价格，它在文件的最后一列。

看看排名前 10 的州，并没有多少惊喜。需要说明的是，最初我对其中一些城市的订单感到措手不及，特别是华盛顿和夏威夷名列榜首。但是，回想一下指标中“每平方英尺”的标准化。因此，考虑到加州的面积，我可能更惊讶于它仍然排在第三位。

[![](img/292047d006a27e8a9e3f5eb7e3867ed2.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--cn3Yy45t--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/1%2Am8dv-PmWxEdxXc-3f-O-Wg.png) 

<figcaption>前 10 名价格/平方英尺(2019 年 5 月)</figcaption>

不管怎样，上节目吧！由于这是一篇可视化的文章，我将避免在你面前扔太多行代码，并在文章的结尾将所有内容链接到它。简而言之，我从[叶子回购](https://github.com/python-visualization/folium)下载了一个美国各州的 GeoJSON 文件。这是一个伟大的发现，因为它立即为我提供了无缝流程所需的数据模式；我需要添加的唯一信息是价格数据(在最终的地图中生成颜色)。在提供了这些之后，仅仅 5 行代码就给了我下面的情节:

[![](img/7695e49ce455a7ad38dc2a62e686805c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--9z1Eo-Ht--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/1%2AotHI92R87cptloOqONQGxA.png)

<figcaption>2019 年 5 月美国房价/平方英尺热图</figcaption>

### 更进一步

既然我已经把脚趾伸进了 Zillow 和 leav 的水里，我已经准备好沉浸其中了。我决定画一张亚特兰大房价的热图。Zillow API 的一个缺点是它的搜索功能非常有限——我找不到任何基于经纬度坐标的搜索方法，这对于创建粒度热图来说非常方便。尽管如此，我还是把它作为一个机会来温习一些爬虫风格的代码；我使用一个城市名称的初始搜索结果作为未来调用的种子，以获得这些房屋的 [comps](https://en.wikipedia.org/wiki/Comparables) (通过 [GetComps](https://www.zillow.com/howto/api/GetComps.htm) 端点)。

值得注意的是，Zillow 确实有很多基于 URL 的搜索[(示例)](https://www.zillow.com/atlanta-ga/houses/2-_beds/2.0-_baths/?searchQueryState=%7B%22pagination%22:%7B%7D,%22mapBounds%22:%7B%22west%22:-84.88217862207034,%22east%22:-84.07880337792972,%22south%22:33.53377471775447,%22north%22:33.999556422130006%7D,%22usersSearchTerm%22:%22Atlanta,%20GA%22,%22regionSelection%22:%5B%7B%22regionId%22:37211,%22regionType%22:6%7D%5D,%22isMapVisible%22:true,%22mapZoom%22:11,%22filterState%22:%7B%22price%22:%7B%22min%22:300000,%22max%22:600000%7D,%22monthlyPayment%22:%7B%22min%22:1119,%22max%22:2237%7D,%22hoa%22:%7B%22max%22:200%7D,%22beds%22:%7B%22min%22:2%7D,%22baths%22:%7B%22min%22:2%7D,%22sqft%22:%7B%22min%22:1300%7D,%22isAuction%22:%7B%22value%22:false%7D,%22isMakeMeMove%22:%7B%22value%22:false%7D,%22isMultiFamily%22:%7B%22value%22:false%7D,%22isManufactured%22:%7B%22value%22:false%7D,%22isLotLand%22:%7B%22value%22:false%7D,%22isPreMarketForeclosure%22:%7B%22value%22:false%7D,%22isPreMarketPreForeclosure%22:%7B%22value%22:false%7D%7D,%22isListVisible%22:true%7D)过滤器，你可以使用这些过滤器，例如通过纬度/经度进行搜索(见下文)。然而，从网页上获取房屋就变成了一项搜集工作，你会受到 Zillow 网页结构突然变化的影响。也就是说，抓取项目会有很多乐趣；如果你想把这个放进我做的东西里，请告诉我！

回到选择的路径，我提到我使用最初的结果作为某个城市的家庭网络的入口点。有了这些入口点，我不断递归调用每个家庭的 comps。这里的一个重要假设是，Zillow 对房屋相似性的定义，除了其他因素之外，还包括位置邻近性。如果没有位置邻近性，基于 comp 的住宅遍历相对于位置将非常不平滑。

那么，我们有什么算法可以以不同的方式遍历节点网络呢？当然，广度优先搜索(BFS)和深度优先搜索(DFS)很快就会浮现在脑海中。出于好奇，看看下面它的基本逻辑流程。除了集合成员保护之外，只有当新的 homes 满足在`meets_criteria`函数中断言的约束时，它们才被添加到集合中。现在，我在预定义的根纬度/经度位置和当前家的位置之间做一个简单的 L2 距离检查。这一标准鼓励搜索停留在根的本地，目的是为了一个良好连接和粒度的热图。下面的实现通过弹出列表的末尾(第 5 行)并添加到列表的末尾(第 14 行)来使用 DFS，但是通过更改任一行(但不是两行)来使用列表的前面，可以快速实现 BFS。

让这个算法在亚特兰大的房屋上运行 10，000 次迭代，在几分钟内就会产生下面的地图！更重要的是，由 folium 生成的[网页](https://anthonyagnone.com/wp-shares-aagnone/atlanta_heatmap.html)是交互式的，允许像缩放和平移这样的常见地图导航工具。为了证明它的模块化，我还生成了一些[波士顿、马萨诸塞州](https://anthonyagnone.com/wp-shares-aagnone/boston_heatmap.html)和[西雅图、华盛顿州](https://anthonyagnone.com/wp-shares-aagnone/seattle_heatmap.html)的小比例价格地图。

[![](img/ec7a4745d433b6bad3945a342707bdc1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--AWF-IEmo--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/1%2AQ81SBWiXe77l8xq8oqpgyw.png) 

<figcaption>亚特兰大房价热图。点击查看互动版[。](https://anthonyagnone.com/wp-shares-aagnone/atlanta_heatmap.html)</figcaption>

### 代码

正如我所承诺的，这是一个项目。它有一个 Make+Docker 设置，便于使用和再现。如果你想了解这两个工具是如何完美地结合在一起实现可再现数据科学的，[请继续阅读这里的](https://anthonyagnone.com/2019/07/10/towards-efficient-and-reproducible-ml-workflows-part-1-analysis/)。无论哪种方式，[自述文件](https://github.com/aagnone3/zillium/blob/master/README.md)都会让你立刻上手，无论是通过脚本还是 Jupyter 笔记本。快乐的 viz！

### 现在怎么办？

接下来，我们可以沿着许多不同的方向发展这个逻辑。我在下面详细介绍了一些激励措施，但我更喜欢朝着最能获得支持、影响和协作的方向前进。你怎么想呢?