# 用马克笔制作地图

> 原文：<https://dev.to/bjfairchild/making-maps-with-markers-2dc7>

如果你有合适的工具，开始使用谷歌地图就像在公园散步一样容易。首先，您需要将 Google Maps 地理编码 API 添加到项目中。您可以在此处获得 API 密钥:

[https://developers . Google . com/maps/documentation/geocoding/get-API-key](https://developers.google.com/maps/documentation/geocoding/get-api-key)

地理编码是将地址(如街道地址)转换为地理坐标(如纬度和经度)的过程，可用于在地图上放置标记或定位地图。要获得您想要的坐标，首先您需要创建一个带有事件监听器的简单搜索栏。您将希望事件侦听器触发一个类似于以下内容的函数:

[![Alt Text](img/1b4d6593d4f688264f84121286792838.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--kPVGYbXC--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/knkhqy13u2oul7pdqcon.png)

这里，我们获取用户在搜索栏中输入的字符串，并用加号替换单词之间的空格。这样做是为了格式化搜索词，让您可以直接将其插入到 url 中。稍加挖掘，您就可以从这个 API 调用将产生的 json 对象中提取纬度经度坐标。

嗯，太好了！你可能会问，下一步是什么？为了渲染地图本身，我们可以使用我们搜索的初始结果来使地图围绕我们的第一个纬度坐标居中。首先，我们将不得不创建 div 标签，我们的地图将生活在那里。

[![Alt Text](img/63cdbfbf1f049988cc6a0213ed521866.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--vnYFwTwY--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/dac2ne6hdny0m2la0mnd.png)

我们在这里添加 ID“map ”,这样我们就可以将这个 div 作为目标，用我们的地图填充它，如下所示:

[![Alt Text](img/3f518128f0e89a57435aff549d7630ba.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--frM6oMqP--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/dyaa3jux413hbtfwovli.png)

这里，我们创建了一个新的地图，它以我们之前搜索得到的 lat-lng 坐标为中心，默认缩放比例为 9。如果我们将地图的中心放在华盛顿州的西雅图，我们会看到如下结果:

[![Alt Text](img/5376c740fa2327401233b30b62dea7bc.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--HfwUCHcS--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/oh984od3g31msmkodu4s.png)

从这一点来说，这是用标记填充地图的一小步。利用我们先前的搜索方法，我们可以获得一组地理编码的坐标，并将它们存储到一个数组中。我们可以将这个标记数组传递给下面的函数，用标记填充我们的地图。

[![Alt Text](img/e5ccfafc02b20d6b96863cc1c801bb32.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--q-Y-JXRD--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/glo39j22knicqiid9pob.png)

这个函数做了一些事情。首先，我们遍历标记数组，在保存在“位置”属性下的每个地理坐标处创建一个标记。我们还指定了要添加标记的地图。为了增加一些特色，您可以为标记设置自定义动画、标题和图像。在这种情况下，我的每个标记看起来都像一个野营帐篷，并且在填充后会从地图顶部掉落。如果将鼠标悬停在该位置上，该位置的名称也会作为工具提示出现在地图上。

[![Alt Text](img/bfa10dc233b86b86fd0a085432e991b0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--4gFTb4cC--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/3f08tocfe5xbpz34sg6a.png)

现在你知道了！使用这些方法，您可以开始创建地图来完善任何可以利用它们的应用程序。