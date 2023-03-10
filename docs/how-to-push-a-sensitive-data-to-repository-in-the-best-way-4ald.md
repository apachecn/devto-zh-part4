# 如何以最佳方式将敏感数据推送到存储库

> 原文：<https://dev.to/plumthedev/how-to-push-a-sensitive-data-to-repository-in-the-best-way-4ald>

## 新方法

“你创造了一些东西，一些伟大的东西”——就像他过去常说的经典。当一个项目的新想法在你脑海中诞生时，你希望它开源，让数百万用户和开发者使用你的优秀应用。

您已经编写了代码，但由于担心泄露敏感数据，您突然不能将代码推送到存储库，您连接到数据库并使用密钥进行授权，您在数据库中的密码由您自己的种子散列，cookies 具有密钥。

如何拯救用户和你的应用程序？这就是我来这里的原因:)

> 我将使用 Node.js 和 TypeScript、Webpack 和 Babel 来创建 CLI，当我键入城市名称时，它会告诉我天气情况。
> 所有天气信息将由 axios 从[OpenWeatherMap.org](https://openweathermap.org/)T3】获取，源代码在我的 [GitHub 存储库中](https://github.com/plumthedev/dev.to-articles/tree/master)

## 建筑

好的项目必须有坚实的基础，即合适的项目结构。

我们需要一个为凭证指定位置的源代码文件夹。
包含敏感数据的文件，我们将使用下划线以大写字母命名，包括它们的名称和内容。在这里看一看[的结构。](https://github.com/plumthedev/dev.to-articles/tree/push-a-sensitive-data/src/js)

## 构建工具

所以，现在我们有了坚实的基础。让我们写一些代码，在我的情况下，它将是 CLI。首先，我试图创建一些类来管理 npm 包。为了使用命令行，我选择了 [commander](https://www.npmjs.com/package/commander) ，这是 node.js 命令行界面的一个很好的解决方案。

接下来，我想给我的控制台消息添加一些颜色。我更喜欢用[粉笔](https://www.npmjs.com/package/chalk)。

我为我的 Cli 和 CLI 命令构建了基类，以便将来更容易开发。再次坚实的基础给我未来节省了很多时间。

OpenWeatherMap.org 强制使用 api 键来获取关于天气的信息，所以在我的目录`src/credentials`中我创建了文件 **OPEN_WEATHER_MAP.js** 来存储敏感数据。我完成了关于必须完成的 api 键和 URL 的信息文件。我没有完成真实数据并将其推送到存储库。**那会很危险！**

```
const OPEN_WEATHER_MAP = {
    API_URL: '<yourOpenWeatherMapApiUrl>',
    API_KEY: '<yourOpenWeatherMapApiKey>'
}

export default OPEN_WEATHER_MAP; 
```

在这种情况下，我不关心敏感数据泄漏。我可以把这个文件推送到存储库，开始用 git 忽略他。由于这一点，git 在未来不会考虑任何文件更改。要开始忽略文件，请在终端中键入:

```
$ git update-index --assume-unchanged src/js/credentials/OPEN_WEATHER_MAP.js 
```

如果您想再次开始跟踪更改:

```
$ git update-index --no-assume-unchanged src/js/credentials/OPEN_WEATHER_MAP.js 
```

之后，我所有的敏感数据都是安全的，其他开发人员可以使用克隆后的项目库。我将其余文件推送到存储库，提交更改，并检查天气。
[![](img/76455790f59f93c1470cc274c3a9a1c0.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--qlV61-V_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ox576u9b3eka65nf0mh8.png)

## 总结

*   将您的敏感数据保存在其他文件中并导出。文件必须标有名称。所有带下划线的大写字母，如`USER_CONFIG.js`。
*   良好、坚实的基础可以在未来节省很多时间。
*   仅提交和推送带有敏感数据的安全值或信息值。不要跳过任何文件，只要在用安全值提交后开始忽略它。
*   要停止跟踪文件，请使用`git update-index --assume-unchanged <path>`
*   要开始跟踪文件，请使用`git update-index --no-assume-unchanged <path>`

感谢阅读！你好，梅子！
GitHub 库:[点击这里](https://github.com/plumthedev/dev.to-articles/tree/push-a-sensitive-data)
作者主页:[点击这里](https://plumthedev.com/en/)