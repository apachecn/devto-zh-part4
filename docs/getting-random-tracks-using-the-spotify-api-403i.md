# 使用 Spotify API 获取随机曲目

> 原文：<https://dev.to/perry/getting-random-tracks-using-the-spotify-api-403i>

虽然 Spotify API 提供了相当多的方式来与最常用的流媒体服务之一进行交互，但它缺乏整体访问所有 Spotify 曲目的能力。这可能是因为有大量的可用歌曲。抓住所有的列表不会非常有效。当我创建一个用户可以发现新音乐的应用程序时，我想用 Spotify 向用户展示看似随机的曲目。在这篇文章中，我将回顾我尝试实现这一目标的几种方法。

这不是一个关于 Spotify API 基础的教程，而是一个模糊的具体问题。查看“入门”指南:[https://developer . Spotify . com/documentation/we b-API/quick-start/](https://developer.spotify.com/documentation/web-api/quick-start/)

使用(节点)JS 开发？这个模块真的让与 Spotify，尤其是 oAuth 的连接变得简单多了:[https://www.npmjs.com/package/spotify-web-api-js](https://www.npmjs.com/package/spotify-web-api-js)

### 流派

我尝试的第一种方法利用了 Spotify 的流派系统。通过获取一个所有流派的列表，选择一个随机的流派并根据该流派获取一首曲目将会产生一首随机的曲目，或者我是这样认为的。首先，Spotify 不会给每首曲目分配一个流派。相反，流派与艺术家相关联。其次，可用流派列表与链接到艺术家的流派不匹配。

根据“可用类型种子”列表，它只包含大约一两百种类型。这些流派都很笼统。链接到艺术家帐户的流派通常更具体。这使得无法使用流派搜索所有艺术家，因此也无法搜索所有曲目。

这个问题已经被其他开发者注意到了。你可以在 GitHub 上查看这个问题:[https://github.com/spotify/web-api/issues/868](https://github.com/spotify/web-api/issues/868)

### 搜索

我尝试的第二种方法利用了一个简单的搜索查询。使用 Spotify 的搜索 API，您可以搜索所有可用的曲目、艺术家、播放列表等。这个方法对我最有效。根据 Spotify 的文档，可以使用通配符进行搜索。通配符可以是任何字符。例如，像这样的搜索查询:`My %`将返回所有以单词`My`开头的曲目。

现在，我必须想出一种方法来创建随机搜索，并从搜索中获得随机轨迹。我能想到的最好的方法是在通配符前面加上一个随机字符，就像这样:`d%`。这将返回所有以`d`开头的音轨。您也可以将通配符放在开头，或者放在开头和结尾。

因为 Spotify 在搜索结果的顶部显示更著名的艺术家和曲目，所以我还必须随机化一个偏移量。根据文件，最大限值为 10.000。偏移量将是第一个返回项的索引，或者是第一个返回的页面(如果设置了限制)。

查看 Spotify 搜索文档:[https://developer . Spotify . com/documentation/we b-API/reference/Search/Search/](https://developer.spotify.com/documentation/web-api/reference/search/search/)

### 获取随机搜索查询

要获得随机搜索查询，您需要从字符列表中获取一个随机字符，并将通配符添加到字符串中。我创建了一个简单的函数来完成这个任务:

```
function getRandomSearch() {
  // A list of all characters that can be chosen.
  const characters = 'abcdefghijklmnopqrstuvwxyz';

  // Gets a random character from the characters string.
  const randomCharacter = characters.charAt(Math.floor(Math.random() * characters.length));
  let randomSearch = '';

  // Places the wildcard character at the beginning, or both beginning and end, randomly.
  switch (Math.round(Math.random())) {
    case 0:
      randomSearch = randomCharacter + '%';
      break;
    case 1:
      randomSearch = '%' + randomCharacter + '%';
      break;
  }

  return randomSearch;
} 
```

Enter fullscreen mode Exit fullscreen mode

要创建随机偏移，只需生成一个 0–10.000 之间的随机数:

`const randomOffset = Math.floor(Math.random() * 10000);`

Spotify 搜索端点允许你过滤不同的类型。要获取跟踪，只需将 type=track 添加到请求中。

现在我们已经有了所有需要的参数，我们可以向 Spotify 搜索端点发出 GET 请求，就像这样:

```
GET https://api.spotify.com/v1/search
type=track
q=getRandomSearch()
offset=randomOffset 
```

Enter fullscreen mode Exit fullscreen mode

### 地点问题

要使用 Spotify API，Spotify 要求用户登录。登录的用户链接到某个位置。这意味着搜索，即使通过 API，也可能有点偏向于来自用户国家的曲目。确实没有办法解决这个问题，但是定义一个`market`是可能的。市场在某种程度上是用户来自的国家。你可以像设置其他参数一样设置市场，但是设置市场意味着你只能从特定国家的艺术家那里得到结果。当然，你也可以随机选择。