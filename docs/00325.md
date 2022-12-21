# 开源贡献(2019 年 9 月)

> 原文：<https://dev.to/roelofjanelsinga/open-source-contributions-september-2019-48h9>

[![People working together](img/c963166e9167d450311a2ad575551bea.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Bm38TFgP--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/qygo42oi9fqtmm1w3six.jpeg)

# 开源投稿(2019 年 9 月)

上个月，我写了一篇博文，提到了我在 2019 年 8 月的开源贡献。我发现这是一个很好的方法来记录我在那个月学到的所有东西，所以我决定在九月也这样做。这个月我一直在开发 4 个包，其中 3 个已经完成，任何人都可以使用，而其中一个仍然是概念验证，处于非常早期的阶段。

## 已完成的套餐

下面的包已经完成，可以在任何项目中使用:

*   [roelofjan-elsinga/URL-language-extractor](https://github.com/roelofjan-elsinga/url-language-extractor)
*   罗洛夫詹-埃尔辛加/拉勒韦尔-奥尼斯基*
*   [罗浮宫/太阳锁*](https://github.com/roelofjan-elsinga/solarium-luke)

带有星号(*)的两个包是分叉的，因为最初的存储库似乎是不活动的，并且我提议的更改是我的项目进展所必需的。这是我第一次分叉不活动的包，并在我自己的名称空间下发布它们，以及我提议的更改。这是一次非常有趣的经历，因为我能够替换以下配置:

```
{  "require":  {  "ageras/laravel-onesky":  "dev-master#77e2de4a78bf2172df4129045c40350582aeabdb"  },  "repositories":[  {  "type":  "vcs",  "url":  "https://github.com/roelofjan-elsinga/laravel-onesky"  }  ]  } 
```

Enter fullscreen mode Exit fullscreen mode

有了这个:

```
{  "require":  {  "roelofjan-elsinga/laravel-onesky":  "^1.0"  }  } 
```

Enter fullscreen mode Exit fullscreen mode

这确实清理了项目的 composer.json 文件。

## 前期包

下面的包仍处于开发的早期阶段，不应该/不能用于任何项目。它已经在 GitHub 上可用的原因是，我正试图将我将从 JSON 数据生成表单的方式形式化。所以如果你对此有什么想法，我很乐意听听。

*   [roelofjan-elsinga/flat-file-CMS-forms](https://github.com/roelofjan-elsinga/flat-file-cms-forms)

你在 9 月份参与过任何开源项目吗？我很想知道你一直在做什么(当然也想看看代码)，所以请在 [Twitter](https://twitter.com/RJElsinga) 上告诉我。