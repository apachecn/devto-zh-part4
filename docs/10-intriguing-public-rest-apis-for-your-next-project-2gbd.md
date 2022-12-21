# 为你的下一个项目准备的 10 个有趣的公共 REST APIs

> 原文：<https://dev.to/camerenisonfire/10-intriguing-public-rest-apis-for-your-next-project-2gbd>

在编程社区中，从事兼职项目的概念被抛出了很多。坐在一个空白的代码编辑器前，不知道要构建什么，这可能是令人生畏的。许多博客文章都推荐构建计算器、任务列表和社交媒体克隆等应用程序。虽然这些对学习技术堆栈肯定有帮助，但让我们面对现实吧——这个世界不再需要任何计算器或待办事项应用程序。相反，我们可以围绕面向公众的 REST APIs 构建新的有趣的应用程序。

> 这是我的博客 [Imago Dev](https://imago.dev) 的交叉帖子。

### 什么是 REST API？

可表示状态转移(REST)应用程序编程接口(API)提供了一组方法，程序员可以使用这些方法通过 HTTP 发送和接收数据。因为这些方法是基于 HTTP 的，所以任何编程语言都可以使用 REST APIs。

几乎每一个可以想到的不同领域都有数以千计的 REST APIs 可用。像天气或股票市场这样常用的公共数据有几十个 API 可供使用。许多流行的网络平台，如脸书和 Twitter，也向开发者提供 API。一些专有 API 对您可以调用的次数有限制。许多都需要注册并接收一个私有 API 密钥。最安全的 API 要求设置 OAuth，以便用户安全登录。

你可以在这个 Github 上找到一个庞大的公共 API 列表[，在](https://github.com/public-apis/public-apis) [RapidAPI](https://rapidapi.com) 上找到一个更大的列表。

### 10 个有趣的公共 REST APIs

这个列表当然没有穷尽所有的 cool REST APIs，只是一些我觉得特别简洁并且值得一些附带项目使用的 API。所有这些都是完全免费的，不需要任何东西，只需要获得一个 API 密匙——不需要弄清楚如何处理 OAuth 或为它们的使用付费。

1.  有史以来最大的媒体专营权现在有了一个简单的方法来获得 800 多个口袋妖怪的数据。
2.  [NASA API](https://api.nasa.gov/index.html) 太空，最后的前沿。获取天体、星系等数据。
3.  [公开食品事实](https://world.openfoodfacts.org/data)来自世界各地的大量食品数据。
4.  [TransLoc OpenAPI](https://rapidapi.com/transloc/api/openapi-1-2) 获取城市和大学校园公共交通实时数据。
5.  [都市词典 API](https://rapidapi.com/community/api/urban-dictionary) 人们想出的俚语真让人吃惊。
6.  韦氏词典 API 给那些想要真实单词的定义和同义词的人。
7.  [数字 API](http://numbersapi.com) 关于数字的有趣事实和琐事。
8.  [WeatherBit API](https://www.weatherbit.io/api) 当前和历史天气数据。
9.  [美国政府数据 API](https://www.data.gov/developers/apis) 这是一个相当大的集合，涵盖了数十个美国数据样本，如农业、健康和公共安全。
10.  有史以来最畅销的书。史上最伟大的故事。

### 如何处置他们

所有这些公共 API 都很棒，但是拥有一系列有趣的数据源并不能从本质上帮助解决为新项目做什么的初始问题。

最好的起点是简单地获取和显示数据。也许那是在展示一个时下流行的口袋妖怪或者一个输入单词的定义。对于更有创意的类型，尝试获取数据片段并将其映射到视觉元素，如温度到颜色或根据公交车移动绘制线条。

最难的部分是简单地开始。一旦你克服了获取和显示信息的最初障碍，我相信你会为你的项目想到很多后续步骤！