# 第 1 部分–在 monorepo 架构中将 react native app 转换为 react-native-web (react PWA)

> 原文：<https://dev.to/ziaulrehman40/part-1-converting-react-native-app-to-react-native-web-react-pwa-in-monorepo-architecture-4no>

### **TL；博士**:

这个系列讲述的是我的旅程:使用来自 twitter 的[的优秀的](https://twitter.com/necolas/status/913877194199359488?lang=en) [react-native-web](https://github.com/necolas/react-native-web) 项目，用相同(90%+)的代码将现有的 react 原生应用程序转换并呈现在 web 上。

### 先决条件:

本文假设您已经有了一些使用`reactjs`和`react-native`的经验，您知道如何构建 RN 代码库的移动版本，并让它们在模拟器中运行。

### **免责声明:**

我在`Ruby on Rails`全栈开发方面最有经验，主要关注后端和 DevOps，我曾在不同的 JS 框架上断断续续地工作过，但*我(还)不是`babel`和`webpack`配置等*方面的专家。因此，请谨慎行事，并在评论中指出流程中的任何改进。

我还不确定这个系列会有多少部分。此外，我的工作，这个应用程序的转换仍然是在制品，所以谁知道什么和工作结束了。但我还是会分享我的旅程。

还要注意，我们没有任何活动的 android 版本，所以本系列不会解释成功构建 android 的步骤。但是你应该能够让它们以类似的方式运行，我将在下面解释 ios。

如果您愿意，可以跳过演职员表和背景部分，直接跳到“_ 让我们开始真正的工作吧！”一节。_

### 信用点/销售点:

在我们开始我们的旅程之前，我想感谢几个在这个旅程中帮了大忙的人，第一个是[布鲁诺·莱默斯](https://dev.to/brunolemos)，我从[他的精彩博文](https://dev.to/brunolemos/tutorial-100-code-sharing-between-ios-android--web-using-react-native-web-andmonorepo-4pej)中获得了初始`react-native-web`设置的`react-native monorepo setup`。另一个是托马斯·格拉迪内斯，他非常友好地通过电子邮件帮助我解决了我在这个过程中遇到的所有问题。

## 背景:

在我正在开发的一款产品上，他们在 iOS 应用商店发布了一款**全功能的 react-native 应用(它没有 android 版本，我们也在计划中，但这不是这里的主题)。我们最近从`56.x to 59.9`** 升级了它的 RN 版本**，app *在 app store 上是*，但是还没有很多用户，因为它是一家初创公司，仍然试图启动他们的初始合同和一切。总之。**

*突然有一天早上，*我们召开了一个紧急会议，项目经理告诉我们，我们有 X 和 Y 客户，我们将与他们合作，这些是业务关键交易。**问题是，他们都需要一个网络应用程序，而不是移动应用程序，而且越快越好。我们需要在一周左右的时间内将我们的 react-native 应用程序移植到 web 版本，**知道我们是只有两个开发人员的团队。没错。就这样。

顺便说一句，我们俩都没有任何将应用程序转换成网络的经验。因此，我受命做一些研发工作，为这种转换提供一个可行的计划，使摩擦和时间要求最小化。因此，由于来自业务方面的压力，我们不得不选择学习曲线最少甚至没有的领域。

**在一个完美的世界**，我们将只是把我们的代码通过一些代码转换器，将我们的`react-native`应用程序转换成`reactjs`网络应用程序。**但是我们并不是生活在一个完美的世界里，不是吗？**事实证明，react 社区在混合应用程序和 pwa 方面取得了巨大进展，但它还没有安静下来， [ionic 推出了 react beta】，微软有一个名为](https://ionicframework.com/blog/announcing-the-ionic-react-beta/) [ReactXP](https://github.com/microsoft/reactxp) 的项目， [expo 也在致力于其应用程序的网络兼容性](https://www.google.com/search?client=opera&q=expo+react+web&sourceid=opera&ie=UTF-8&oe=UTF-8)。几乎感觉我们需要这种转换早了几年。

因此，记住我们的短时间通知和业务重要性，我们只是决定保持我们的学习曲线较低，不要太担心未来，并且**使用 [react-native-web，](https://github.com/necolas/react-native-web)目前官方支持 RN 0.55，我们在 0.59** 上，因为我们已经看到一些人提到他们在 RN 0.59 上成功运行 RN-web，一些人提到一些黑客[，比如在为 web](https://github.com/necolas/react-native-web/issues/1172#issuecomment-472196873) 编译时让`react-native-web`认为它的 Rn 0.55。所以我们决定与 RN-web 合作，尽快在 web 上运行一些东西。

我不得不做一些重做来使它工作，这就是为什么我认为值得详细写一下，这样人们就不会卡在我卡的地方，不得不重新设置一切，希望它能工作，就像重新启动 [![🙂](img/2ea3454d48c385c4b31b2c45b77b4229.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--zCyXRrdx--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://s0.wp.com/wp-content/mu-plugins/wpcom-smileys/twemoji/2/72x72/1f642.png)

## 维修性和未来？

这是一个棘手的问题，RN web 上有积极的进展，尽管就提交而言它看起来不是很活跃，并且它很难跟上最新的 RN 版本，但是随着越来越多的人开始使用它，越来越多的人将合作并参与它的开发，我们应该会看到更好的未来。

monorepo 架构本身非常惊人，与`RN`配合得天衣无缝(除了链接问题)，但我们可以预见的唯一问题是`react-native-web`的积极发展。尝试并让一些东西运行起来是很棒的。但是，如果你面临太多的问题和/或缺少你不能没有的功能，并且你碰巧有很多时间过渡到`ionic`或`ReactXP`或类似的东西，你可以尝试一下。但是正如我提到的，这些还没有成熟，所以也祝你好运。

## 让我们开始真正的工作吧！

在开始之前，我应该强调版本控制的重要性，只要坚持提交每一个小步骤，并且使用正确的提交消息，你将会节省很多时间。

好了，我们开始吧。

### 初始单回购设置:

正如我上面提到的，我正在使用 monorepo 架构在移动和 web 之间共享代码，我关注了[一篇关于这个问题的优秀博客文章](https://dev.to/brunolemos/tutorial-100-code-sharing-between-ios-android--web-using-react-native-web-andmonorepo-4pej)。文章中还链接了一个[样板回购](https://github.com/brunolemos/react-native-web-monorepo)，你可以使用。但是我更喜欢从零开始设置这整个事情，正如我链接的帖子中所描述的，所以对实际发生的事情有更好的理解。事实证明，发生了很多事情。

*顺便说一句，有一个关于`monorepo vs multi-repo`架构的[有趣辩论，](https://medium.com/@mattklein123/monorepos-please-dont-e9a279be011b)我们不会走那条路。但是值得一提的是，当我们使用 RN-web 这样的东西时，拥有这种架构是最有意义的。如果我们只是在 web 和 native react 之间共享服务和 reducers 等，可能就没那么多了。*

好了，现在我们有了 monorepo 设置，基本的样板文件可以在手机和网络上运行。**让我们开始将现有的移动应用程序导入该架构。**在我们这样做之前，我必须指出，您可以尝试将现有的应用程序就地转换为 monorepo 设置，但这对我们不起作用，因为它花费了太多时间，所以我们决定将其移植过来。

### mono repo 设置的小演练

在根据上述指南进行设置的过程中(你必须从那里开始，我不会在这里重复)，你会注意到我们正在使用`yarn`，以及它的`workspaces`特性。我们在根目录下有一个名为`packages`的文件夹，它目前包含 3 个子文件夹，每个子文件夹都有自己的`package.json`文件。但重要的是，所有的软件包都将安装在根目录下的`node_modules`文件夹中。不在子`node_modules`文件夹中。

**让我们逐一查看这些包/文件夹:**

**1- `Components` :** 这个文件夹将保存通用组件或共享代码，也就是说，在这个组件中，我们将拥有我们想要共享的一切。如果你喜欢，你可以在命名上更有创意。

**2- `Mobile` :** 顾名思义，这个文件夹会保存我们手机的特定代码。当工作/建造移动设备时，我们将呆在这个文件夹中。这个文件夹中还有我们在任何 react-native 应用程序中看到的常见文件夹，如`ios` `android` `src`文件夹等。你应该已经知道这些是什么，以及我们如何使用它们。

**3- `Web` :** 这是真正的网络奇迹发生的地方。这应该是本文的重点。

### 明白了！

就像没有免费的午餐一样，我也遇到过一些问题。我直接从布鲁诺的原始文章粘贴前 3 个。

1.  `react-native-web`支持大部分的`react-native` API，但是少了一些，比如`Alert`、`Modal`、`RefreshControl`和`WebView`
2.  如果您遇到一个不适合 monorepo 结构的依赖项，您可以将它添加到[no huit](https://github.com/devhubapp/devhub/blob/2f64088b84611f7458d3dd7933baf25ffb5196d9/package.json#L16-L18)列表中；
3.  没有`nohoist`,`react-native link`可能无法很好地处理 monorepo 项目；要解决这个问题，可以在`**/react-native`上使用`nohoist`，或者不使用`yarn workspace mobile add xxx`来安装依赖项，而是将它们也安装在根目录:`yarn add xxx -W`。现在你可以链接它，然后再从根目录`package.json`中删除它。*(到目前为止我一直在做的就是从这个出发的第二个选择。也就是将依赖项复制到根目录`packakeg.json`中，然后从那里删除它们。)*
4.  寻找库版本当你从现有应用程序中复制依赖项时，如果你没有在 `package.json`中**正确锁定它们，它们*将*改变并可能在你不注意的情况下跳转到最新版本，所以我建议你在开始移植之前，花一些时间正确锁定它们，并确保所有东西在旧应用程序中都能工作。**
5.  做好准备，至少在短期内，你的手机应用程序的一些功能细节会受到影响。听起来很奇怪，但这是我面对的现实，一些 RN 组件甚至在 RN-web 中不受支持，其中之一是`Alert`，我们碰巧经常使用它，所以我们将需要一些补丁(如果我们能找到的话)来使它工作，或者我们将不得不使用其他东西来实现相同的功能。
6.  除非你决定限制你的网络应用的宽度。以及应用程序的某些组件/屏幕。甚至感觉像是重写视图层。可以想象响应能力的巨大变化。
7.  我还没有设置一些合适的`versioning script`，就像我们在旧设置中使用的那样，但这对我们来说可能会有点问题。我们可能需要一些定制的东西，除非我们可以没有它，每次发布新版本的时候都浪费时间。
8.  最后，从第 4、5 和 6 点来看，你可能已经猜到了，RN 和 RN-web 组合将最适用于旨在 PWA 或混合结果的新 react 应用程序。这并不意味着当你时间紧迫，想从现有的 RN 代码库中获取一些东西时，它不值得一试。

## 将现有应用程序导入新设置

### 先说依赖关系

我决定首先移动、安装依赖项，并正确更新`yarn.lock`文件，然后复制实际代码。所以我这么做了。我复制了旧`package.json`中`dependencies`和`devDependencies`部分下的所有内容(除了 react 和 react-native，任何其他重复)，并分别将其移动到`packages/components/package.json`部分。*(请注意，我对上面提到的第 4 个问题并不小心，这给我们后来带来了许多问题，所以请留意)。*在我安装好所有东西并且移动应用程序仍然运行良好之后(当然，因为我们还没有从这些新的依赖项中导入任何东西)，我进行了一次新的提交。

### 现在导入实际代码

这一特定步骤听起来似乎很容易，但实际上并不容易。相信我，我花了至少一天半的时间来让它在这个新的架构中为移动设备而构建！是啊…

为了导入旧代码，我将解释我采取了什么策略，您可以决定什么最适合您。我决定将整个现有代码复制到应用程序的`components`包中，这是为了共享代码。我们的想法是首先在这个架构中成功地构建移动设备，然后在 web 上进行尝试，并将需要移动的内容移动到移动设备或 web 特定的子包中。

所以我只是将所有东西从我的旧 src 文件夹复制到`packages/components/src`中，在我们的旧设置中，我们将`App.js`放在了`src`之外，我也将它移到了`src`之内，并且必须更新`App.js`中的一些导入路径，但这很好。现在 app 应该能用了吧？不，不要忘记您的依赖项可能需要的链接和其他`xcode`特定设置。

### 为我们的应用程序链接库和`xcode`修复

可能有比我根据自己的经验描述的更好的方法。

正如在 gotchas 部分的第 3 点中提到的，链接有点棘手。在链接之前，如果你使用`cocoa pods`，**，请设置它们。即使你没有，这也是使用它们的好时机。安装可可豆荚**。当它看起来不错的时候，选择你想要如何链接库，正如上面提到的部分所描述的，现在，我正在做的是将所有的依赖项从`packages/components/package.json`复制到主`package.json`，从主目录安装并运行`react-native link`。然后从 main package.json 和 yarn install 中删除这些依赖项。显然，最后一步是在`ios`目录下运行`pod install`。

**我有一些并发症**，结果是由其他原因引起的，我一会儿会解释，但在这个舞台上，我所做的是*完全复制我的旧`podfile`*。我的意思是，如果你有一个`podfile`，拷贝一个旧的并修改对回购主目录中的`node_modules`文件夹的引用并没有坏处。

**在这一步，您可以尝试运行项目的`.scworkspace`文件，清理构建，让`metro bundler`在后台运行**(如 monorepo 安装指南中所述，我链接的命令类似于:`yarn workspace mobile start`)，然后**让我们尝试构建应用**。如果你足够幸运，你的应用会运行，我的不会。它确实构建成功，但从 metro bundler 加载文件时失败。

#### 奇怪的`path`和`fs`错误

首先我出现了 **`bundling failed: Error: Unable to resolve module` path `...`** 错误，我盲目地添加了一个名为 path 的东西的包(你可以谷歌一下，我不记得了，可能它的名字就叫 path)。然后开始看`bundling failed: Error: While trying to resolve module` fs `from file...`，`node_modules/fs/package.json`被成功找到。但是，此包本身指定了一个`main`模块字段，该字段无法解析为移动包中的新字段`thats when i thought maybe, **it is something wrong with my config** , not my dependencies, because i have all my dev and other dependencies installed same as in previous app. So it is not the dependencies i need but something else. What is turned out to be? _I missed to copy babel presets from old` babel.config.js`。这解决了这两个问题。 _

那是我浪费了很多时间的部分。在这之后，我重试了建设，低，看！另一个错误——当然是 [![😀](img/193be02fea5ea08f1d6a70ca78222a2d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--qgur0s-_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://s0.wp.com/wp-content/mu-plugins/wpcom-smileys/twemoji/2/72x72/1f600.png)

#### `react-native native module cannot be null`错误

这个错误是从地铁捆扎机加载后出现的，原来，**是抱怨缺少正确的链接**。因为有些库不仅仅要求你运行`react-native link`，它们有时也包括手动步骤，比如`react-native-permissions`包。所以在这个阶段我有两个选择:

1.  仔细阅读我的所有依赖项自述文件，并确保我们根据需要设置了所有内容。
2.  匹配旧的`.xcworkspace`文件库和链接部分，并确保一切都匹配。

由于时间不够，我选择了路线 2，我不推荐它，特别是如果你有 android 版本的话。这花了我一些时间来确保我们有我们需要的一切。在出现类似错误的几轮失败之后，我能够运行这个应用程序了。

**注:** *这是我几乎可以肯定的部分，这将有一些比上面提到的两个更好的方法，肯定比我采取的方法更好。如果你能提出任何建议，请发表评论，我会将其添加到读者列表中。*

### 轰！移动应用程序正在 monorepo 设置中运行！

根据你的设置，可能会有一些其他的位，但对我来说就是这样了，我运行了我的移动构建！终于！

**PS:** 正如我提到的，我没有正确锁定我的依赖项，我在这一点上的应用程序有些问题，但它是工作的，我们唯一的目标是尽快准备好网页版本。所以我们只是注意到这些问题，然后继续前进。

[![download](img/36965c27471ad4fbe048ecc18d66752f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--P8D22pFE--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://ziatechblog.files.wordpress.com/2019/07/download.jpeg)

至少第一部分是这样。我们的移动应用运行在这个架构中。我们可以在下一部分从这里开始，实际上开始移植 web 应用程序。

请分享你的经历和你想在文章中添加/修复的任何东西。

下次见，TC。

第二部分可以在这里找到。