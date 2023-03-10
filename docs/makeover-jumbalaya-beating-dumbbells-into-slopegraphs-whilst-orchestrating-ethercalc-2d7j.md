# 改造 Jumbalaya:一边演奏 EtherCalc，一边把哑铃打成斜坡图

> 原文：<https://dev.to/hrbrmstr/makeover-jumbalaya-beating-dumbbells-into-slopegraphs-whilst-orchestrating-ethercalc-2d7j>

今天早上， [@kairyssdal](http://twitter.com/kairyssdal) 发了一张来自 [@axios](http://twitter.com/axios) 的图片:

> 令人困惑，但很有趣。
> 
> 数据显示我们是一个新闻消费伪君子的国家——Axios[https://t.co/O0lPSc4OV3](https://t.co/O0lPSc4OV3)
> 
> — Kai Ryssdal ( [@kairyssdal](http://twitter.com/kairyssdal) ) [June 11, 2019](https://twitter.com/kairyssdal/status/1138405440578961408?ref_src=twsrc%5Etfw)

如果你在做正确的事情，阻止邪恶的社交媒体 javascript，你可以在这里找到 Axios 的故事[和下图:](https://www.axios.com/news-consumption-read-topics-56467fe6-81bd-4ae5-9173-cdff9865deda.html)

[![](img/6499561ebf43b92a4648f071b74c9d78.png)](https://i2.wp.com/rud.is/b/wp-content/uploads/2019/06/axios-01.png?ssl=1)

我要说:图表令人困惑。我知道他们想做什么，但这是一个明显的例子，其中斜率图肯定是比方向哑铃图更好的选择。当时我有大约 5 分钟的空闲时间，所以我[做了一个快速改造](https://twitter.com/hrbrmstr/status/1138413330236878848?s=20)和[一个简短的操作指南](https://twitter.com/hrbrmstr/status/1138416265020747776?s=20)。这篇文章是对上述主题的扩展，如果你正处于决定是阅读还是继续前进的过程中，下面是我们将要介绍的内容:

*   在抓取或手动数据输入之间做出选择
*   快速介绍[以太计算器](https://ethercalc.net)
*   为什么您会考虑使用 EtherCalc 在 Excel 或 Google Sheets 上手工输入数据
*   将数据从 EtherCalc 拉入 R
*   用捕获的数据制作坡度图
*   用捕获的数据定制斜率图，以讲述一个或多个故事

如果你感兴趣的话，请继续读下去。

### 刮还是不刮

即使我想抓取这个网站，Axios [很清楚地表明他们不是什么好人](https://www.axios.com/legal),因为——虽然它没有提到抓取——ToS 链接确实表明了这一点:

> (a)您不得许可、出售、出租、租赁、转让、分配、分发、托管或以其他方式商业利用本网站或本网站上显示的任何内容；(b)您不得对网站的任何部分进行修改、衍生作品、反汇编、反向编译或反向工程；(c)您不会为了建立类似或竞争性网站、产品或服务而访问本网站；以及(d)除非在此明确说明，否则不得以任何形式或手段复制、复制、分发、再版、下载、展示、张贴或传播本网站的任何部分。除非另有说明，否则网站功能的任何未来版本、更新或添加都将受这些条款的约束。网站(或网站上展示的任何内容)上的所有版权和其他所有权声明必须保留在所有副本上。

(哦，不，我是从他们的网站上复制的，所以我已经违反了他们的[不可执行的]条款！)

有一个叫做[“合理使用”](https://fairuse.stanford.edu/overview/fair-use/what-is-fair-use/)的东西，我正在做的这个改造 100%包括在里面。Axios ToS 和许多其他网站的 ToS 试图禁止这种合理使用，他们通常会在法庭上输掉这场战斗。我已经并将在这篇文章中引用它们作为来源，这篇文章本身属于“评论和批评”。与许多不道德的搜集者不同，他们只是在收集他们没有工作可做的数据，他们的工作不会为一般社区的更好利益服务，这篇文章完全致力于分享和教育。

事实上，Axios 很可能有如此严厉的 ToS，因为所有可怕的不道德的刮板谁只是想要免费的，未引用的新闻内容。

*无论如何* …

即使我能抓取，他们也没有嵌入 javascript 数据对象，也没有加载 XHR JSON 数据块来制作图形。他们使用加载基础图像的习语，然后通过标记执行注释:

[![](img/d7fd3adebeb57616f812edc102d54a27.png)](https://i0.wp.com/rud.is/b/wp-content/uploads/2019/06/axios-graphic-1.png?ssl=1)

使得不值得花时间去刮。

这意味着数据输入。[![🙁](img/af4e4a9fa45afec8aa151b7ea52f04bf.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--IwZUrXu3--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://s.w.oimg/core/emoji/12.0.0-1/72x72/1f641.png)

### 使用 EtherCalc 进行~~乐趣和利润~~数据录入

我不喜欢 Microsoft Excel(即使是它的现代版本),因为它对数据输入来说太过分了。我也不喜欢在谷歌表单中输入数据，因为这意味着我需要与云连接。因此，对于小型的本地数据输入需求，我会求助于 [EtherCalc](https://ethercalc.net) 。不需要互联网接入，也没有臃肿的应用程序运行。

EtherCalc 是一个多用户的基于浏览器的电子表格，由 javascript 支持(浏览器内和后端)。你可以用
安装它

```
$ npm install -g ethercalc 
```

Enter fullscreen mode Exit fullscreen mode

这假设您有一个工作的 [Node.js](https://nodejs.org/en/) 设置和 [npm](https://www.npmjs.com/) 。

当你跑步:

```
$ ethercalc 
```

Enter fullscreen mode Exit fullscreen mode

你会得到一个可以用你的浏览器点击的 URL。下面是我的数据输入完成后的样子:

[![](img/7fb0205c6d8082195397bfc20ef520e8.png)](https://i2.wp.com/rud.is/b/wp-content/uploads/2019/06/ethercalc-1.png?ssl=1)

它可以使用 Redis 或本地文件系统作为持久层，并支持多人同时编辑同一文档。

此时，我可以手动将它保存到一个 CSV 文件中，并以传统的方式读取它，但是 EtherCalc 有一个 API ！所以我们可以使用`{httr}`调用来获取数据，就像这样:

```
library(hrbrthemes)
library(tidyverse)

httr::GET(
  url = "http://localhost:8000/a983kmmne1i7.csv"
) -> res

(xdf <- httr::content(res))
## # A tibble: 14 x 3
## topic actually_read say_want_covered
## <chr> <dbl> <dbl>
## 1 Health care 7 1
## 2 Climate change 5 2
## 3 Education 11 3
## 4 Economics 6 4
## 5 Science 10 7
## 6 Technology 14 8
## 7 Business 13 11
## 8 National Security 1 5
## 9 Politics 2 10
## 10 Sports 3 14
## 11 Immigration 4 6
## 12 Arts & entertainment 8 13
## 13 U.S. foreign policy 9 9
## 14 Religion 12 12 
```

Enter fullscreen mode Exit fullscreen mode

其中`a983kmmne1i7`是活动文档标识符。

现在我们有了数据，是时候开始改头换面了。

### 第一阶段:基本坡度图

(如果你需要一本关于 slopegraphs 的入门书，一定要通过[@ visualing data](http://twitter.com/visualisingdata)查看[这个资源](http://www.visualisingdata.com/2013/12/in-praise-of-slopegraphs/)。)

我们需要对斜坡上的情况做出决定。我选择将回答者*实际阅读的内容*放在左边，然后将他们*说他们想要覆盖的内容*放在右边。不考虑顺序，我们需要做一些数据争论来尝试图表:

```
ggplot() +
  # draw the slope lines
  geom_segment(
    data = xdf,
    aes(
      x = "Actually read", y = actually_read,
      xend = "Say they\nwant covered", yend = say_want_covered
    )
  ) +
  # left and right vertical bars
  geom_vline(aes(xintercept = c(1, 2)), color = "#b2b2b2") +
  # left and right category text
  geom_text(data = xdf, aes("Actually read", actually_read, label = topic)) +
  geom_text(data = xdf, aes("Say they\nwant covered", say_want_covered, label = topic)) +
  scale_x_discrete(position = "top") 
```

Enter fullscreen mode Exit fullscreen mode

[![](img/4290e85db23ed895099bda51a4f6acb9.png)](https://i0.wp.com/rud.is/b/wp-content/uploads/2019/06/slope-01a.png?ssl=1)

这张图表不会很快赢得任何(好)奖项。除了不对齐的类别标签，类别没有按照传统的顺序排列(排名#1 在左边的顶部)，而且我们肯定需要图表上的更多信息(标题、副标题、题注等)。).我们将重新排列标签，去掉一些美学问题，并切换主题:

```
xdf <- mutate(xdf, dir = factor(sign(actually_read - say_want_covered))) # get the category order right
xdf <- mutate(xdf, actually_read = -actually_read, say_want_covered = -say_want_covered) # reverse the Y axis

ggplot() +
  geom_segment(
    data = xdf,
    aes(
      "Actually read", actually_read,
      xend = "Say they\nwant covered", yend = say_want_covered
    ),
    size = 0.25, color = "#b2b2b2"
  ) +
  geom_vline(aes(xintercept = c(1, 2)), color = "#b2b2b2") +
  geom_text(
    data = xdf,
    aes("Actually read", actually_read, label = topic),
    family = font_rc, size = 4, hjust = 1, nudge_x = -0.01
  ) +
  geom_text(
    data = xdf,
    aes("Say they\nwant covered", say_want_covered, label = topic),
    family = font_rc, size = 4, hjust = 0, nudge_x = 0.01
  ) +
  scale_x_discrete(position = "top") +
  labs(
    x = NULL, y = NULL,
    title = "14 Topics Ranked by What Americans Read vs Want Covered",
    subtitle = "'Read' rank from Parse.ly May 2019 data.\n'Want covered' rank from Axios/SurveyMonkey poll conducted May 17-20, 2019",
    caption = "Source: Axios <https://www.axios.com/news-consumption-read-topics-56467fe6-81bd-4ae5-9173-cdff9865deda.html>\nMakeover by @hrbrmstr"
  ) +
  theme_ipsum_rc(grid="") +
  theme(axis.text = element_blank()) 
```

Enter fullscreen mode Exit fullscreen mode

[![](img/8d16e9b388b5304da8986d057dd2e31d.png)](https://i1.wp.com/rud.is/b/wp-content/uploads/2019/06/slope-02a.png?ssl=1)

这看起来好多了，由于初始线程的时间限制，我就此打住。然而，坡度线往往很难跟踪，我们真的应该用它们来讲述一个故事。但是我们想关注什么故事呢？

### 故事时间

不管故事如何，我们想要立即修改的一个美学元素是线条颜色。我们可以使用`dir`列来表示:

```
ggplot() +
  geom_segment(
    data = xdf,
    aes(
      "Actually read", actually_read,
      xend = "Say they\nwant covered", yend = say_want_covered,
      color = dir, size = dir
    )
  ) +
  geom_vline(aes(xintercept = c(1, 2)), color = "#b2b2b2") +
  geom_text(
    data = xdf,
    aes("Actually read", actually_read, label = topic),
    family = font_rc, size = 4, hjust = 1, nudge_x = -0.01, lineheight = 0.875
  ) +
  geom_text(
    data = xdf,
    aes("Say they\nwant covered", say_want_covered, label = topic),
    family = font_rc, size = 4, hjust = 0, nudge_x = 0.01, lineheight = 0.875
  ) +
  scale_x_discrete(position = "top") +
  scale_size_manual(
    values = c(
      `-1` = 0.2,
      `0` = 0.2,
      `1` = 0.2
    ),
  ) +
  scale_color_manual(
    name = NULL,
    values = c(
      `-1` = ft_cols$red,
      `0` = "#2b2b2b",
      `1` = ft_cols$blue
    ),
    labels = c(
      `-1` = "Topics Readers Want Covered < Topics Read",
      `0` = "Topics Read The Same Amount As They Want Covered",
      `1` = "Topics Read < Topics Readers Want Covered"
    )
  ) +
  guides(
    size = FALSE
  ) +
  labs(
    x = NULL, y = NULL,
    title = "14 Topics Ranked by What Americans Read vs Want Covered",
    subtitle = "'Read' rank from Parse.ly May 2019 data.\n'Want covered' rank from Axios/SurveyMonkey poll conducted May 17-20, 2019",
    caption = "Source: Axios <https://www.axios.com/news-consumption-read-topics-56467fe6-81bd-4ae5-9173-cdff9865deda.html>\nMakeover by @hrbrmstr"
  ) +
  theme_ipsum_rc(grid="") +
  theme(axis.text = element_blank()) +
  theme(legend.position = "bottom") +
  theme(legend.direction = "vertical") 
```

Enter fullscreen mode Exit fullscreen mode

[![](img/7387c4880cf45ec7819313f66bf784a3.png)](https://i2.wp.com/rud.is/b/wp-content/uploads/2019/06/slope-03.png?ssl=1)

挑选故事仍然有些困难，传说可能有用，但并不理想。让我们用颜色突出显示不同的斜坡类型，直接注释它们，看看会出现什么:

```
library(hrbrthemes)
library(tidyverse)

httr::GET(
  url = "http://localhost:8000/a983kmmne1i7.csv"
) -> res

(xdf <- httr::content(res))

xdf <- mutate(xdf, dir = factor(sign(actually_read - say_want_covered)))
xdf <- mutate(xdf, actually_read = -actually_read, say_want_covered = -say_want_covered)

arw <- arrow(length = unit(5, "pt"), type = "closed")
# x = c(1.2, 1.8, 1.9),
# y = -c(1, 13, 14),
# xend = c(1.05, 1.7, 1.6),
# yend = -c(1.125, 13, 14)
# ),
# aes(x, y , xend=xend, yend=yend),

ggplot() +
  geom_segment(
    data = xdf,
    aes(
      "Actually read", actually_read,
      xend = "Say they\nwant covered", yend = say_want_covered,
      color = dir, size = dir
    ), show.legend = FALSE
  ) +
  geom_vline(aes(xintercept = c(1, 2)), color = "#b2b2b2") +
  geom_text(
    data = xdf,
    aes("Actually read", actually_read, label = topic),
    family = font_rc, size = 4, hjust = 1, nudge_x = -0.01, lineheight = 0.875
  ) +
  geom_text(
    data = xdf,
    aes("Say they\nwant covered", say_want_covered, label = topic),
    family = font_rc, size = 4, hjust = 0, nudge_x = 0.01, lineheight = 0.875
  ) +
  geom_curve(
    data = data.frame(), 
    aes(x = 1.2, y = -1, xend = 1.05, yend = -1.125), 
    color = ft_cols$red, arrow = arw
  ) +
  geom_segment(
    data = data.frame(), aes(x = 1.6, xend = 1.6, yend = -12.1, y = -12.9), 
    color = "#2b2b2b", arrow = arw
  ) +
  geom_curve(
    data = data.frame(), aes(x = 1.2, y = -14.1, xend = 1.1, yend = -13.6),
    curvature = -0.5, color = ft_cols$blue, arrow = arw
  ) +
  geom_text(
    data = data.frame(
      x = c(1.15, 1.6, 1.2),
      y = -c(1.2, 13, 14),
      hjust = c(0, 0.5, 0),
      vjust = c(0.5, 1, 0.5),
      lab = c(
        "Topics Readers Want Covered < Topics Read",
        "Topics Read The Same Amount\nAs They Want Covered",
        "Topics Read < Topics Readers Want Covered"
      ),
      stringsAsFactors = FALSE
    ),
    aes(x, y, hjust = hjust, vjust = vjust, label = lab),
    family = font_rc, size = 2.5, lineheight = 0.875
  ) +
  scale_x_discrete(position = "top") +
  scale_size_manual(
    values = c(
      `-1` = 0.75,
      `0` = 0.2,
      `1` = 0.2
    )
  ) +
  scale_color_manual(
    name = NULL,
    values = c(
      `-1` = ft_cols$red,
      `0` = "#2b2b2b",
      `1` = ft_cols$blue
    )
  ) +
  labs(
    x = NULL, y = NULL,
    title = "14 Topics Ranked by What Americans Read vs Want Covered",
    subtitle = "'Read' rank from Parse.ly May 2019 data.\n'Want covered' rank from Axios/SurveyMonkey poll conducted May 17-20, 2019",
    caption = "Source: Axios <https://www.axios.com/news-consumption-read-topics-56467fe6-81bd-4ae5-9173-cdff9865deda.html>\nMakeover by @hrbrmstr"
  ) +
  theme(axis.text.x = element_text(size = 12, face = "bold", color = "black")) +
  theme(axis.text.y = element_blank()) 
```

Enter fullscreen mode Exit fullscreen mode

[![](img/cc34266194b52344d451d9c527b78f09.png)](https://i0.wp.com/rud.is/b/wp-content/uploads/2019/06/slope-04.png?ssl=1)

第一个故事表明了受访者潜在的社会期望偏见，因为他们声称更关心医疗保健、气候变化和教育，但实际上更关心更琐碎的事情(体育)、基础事情(政治)和他们几乎无法控制的事情(国家安全)。

让我们切换焦点(只显示修改后的美学以避免代码 DoS):

```
 scale_size_manual(
    values = c(
      `-1` = 0.2,
      `0` = 0.2,
      `1` = 0.75
    )
  ) + 
```

Enter fullscreen mode Exit fullscreen mode

[![](img/e9be160faaa57b8de201db949f00bcff.png)](https://i1.wp.com/rud.is/b/wp-content/uploads/2019/06/slope-05.png?ssl=1)

现在我们来看看一些“期望覆盖范围”的主题在优先级列表中到底排在什么位置。至少医疗保健不是最差的，但是考虑到科技在多大程度上控制了我们的生活，看到这种情况出现在最差的地方有点令人不安。

等级没有差别的类别呢:

```
 scale_size_manual(
    values = c(
      `-1` = 0.2,
      `0` = 0.75,
      `1` = 0.2
    )
  ) + 
```

Enter fullscreen mode Exit fullscreen mode

[![](img/4157cc737c03db9da79a747fb5cc300f.png)](https://i2.wp.com/rud.is/b/wp-content/uploads/2019/06/slope-06.png?ssl=1)

你和我一样猜测为什么人们对这些评价相同(假设调查使用了相似的语言)。

### 鳍

现在你已经得到了数据(哦，对了，我忘了这么做):

```
structure(list(topic = c("Health care", "Climate change", "Education", 
"Economics", "Science", "Technology", "Business", "National Security", 
"Politics", "Sports", "Immigration", "Arts & entertainment", 
"U.S. foreign policy", "Religion"), actually_read = c(7, 5, 11, 
6, 10, 14, 13, 1, 2, 3, 4, 8, 9, 12), say_want_covered = c(1, 
2, 3, 4, 7, 8, 11, 5, 10, 14, 6, 13, 9, 12)), class = c("spec_tbl_df", 
"tbl_df", "tbl", "data.frame"), row.names = c(NA, -14L), spec = structure(list(
    cols = list(topic = structure(list(), class = c("collector_character", 
    "collector")), actually_read = structure(list(), class = c("collector_double", 
    "collector")), say_want_covered = structure(list(), class = c("collector_double", 
    "collector"))), default = structure(list(), class = c("collector_guess", 
    "collector")), skip = 1), class = "col_spec")) 
```

Enter fullscreen mode Exit fullscreen mode

和一些不同的观点，也许你有更好的方式来看待它。请在评论中留下您的任何创作或建议，以改进此处显示的最终版本。