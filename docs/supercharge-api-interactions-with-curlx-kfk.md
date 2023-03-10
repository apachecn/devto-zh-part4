# 增压 API 与 curlx 的互动！

> 原文：<https://dev.to/shivkanthb/supercharge-api-interactions-with-curlx-kfk>

[![](img/a6988101a0ce3a4abd130c89f9b4abe8.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--WFpcrIqf--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/po95xupaqswult4ujqyy.png)

我真的很喜欢 GUI http 客户端(如 Postman)提供的功能，如历史、收藏、共享请求的能力等，但我个人更喜欢使用标准的 cURL 来提高速度和易用性。

我构建了 curlx 来带来两个世界的精华。此外，您还会得到一个格式丰富的输出以及响应头。

在引擎盖下，它只是一个 curl 命令，所以如果你已经知道如何使用 curl，用`cx`或`curlx`替换`curl`就可以了。

### 显著特征:

*   历史:

[![](img/f54bc41f4efce437e1a25cc9ca2ebb98.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--yKBBboaP--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/z7s6k0h4ac10qwg7x421.png)

您发出的每个请求都用一个唯一的 id 记录下来，以便将来快速执行。更快地运行请求，而不必再次键入整个请求。

*   收藏:

[![](img/3d0b7ae1ea154f9c5528dd9c520526b2.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--yoqwwr0k--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://curlx.dev/Collection.png)

就像在 Postman 中一样，将您的请求组织成组/集合，帮助您更快地发现或重新运行频繁的请求。

*   共享:

轻松地与队友分享您的请求和收藏。根目录下的 cxdb 文件夹包含历史和集合 json 文件(导出就绪)。

[![](img/0dc006d93263752edd9901b3e65cd1d8.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--eymr3quB--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/7wz8obp082j03xr160tv.png)

它是开源的，所以你可以随时查看，报告错误，添加特性等等。希望得到您的反馈！
Github-[https://github.com/shivkanthb/curlx](https://github.com/shivkanthb/curlx)

在 Twitter 上与我的联系 [@shivkanthb](https://twitter.com/shivkanthb)