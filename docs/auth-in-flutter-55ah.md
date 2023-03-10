# 颤振中的认证

> 原文：<https://dev.to/andrious/auth-in-flutter-55ah>

*使用授权库包登录*

<figure>[![](img/28016e279c97f0c7f34bd2127511bc30.png)](https://pub.dev/packages/auth) 

<figcaption>[auth 库包](https://pub.dev/packages/auth)</figcaption>

</figure>

认证是移动应用程序的一项常见任务，这使得它成为类库的理想候选对象。一个类库将以一种正式和标准化的方式执行任务——使认证变得轻而易举。我把这样的类库以 Dart 包、**的形式用于自己的 app。我将使用示例代码来演示这个包。这个示例代码可以在 Dart 包的[示例选项卡](https://pub.dev/packages/auth#-example-tab-)中查看。然后，我将继续用传统的“简单易读”的截屏“浏览”类库。**

 **讽刺的是，这篇关于移动开发的文章，在电脑上看比在手机上看更好。此外，我们大多在电脑上编程；我们的手机上没有。暂时如此。

我们开始吧。

<figure>[![](img/428b5f66e56c73069fcfa4268b974751.png)](https://medium.com/@greg.perry) 

<figcaption>[格雷格佩里其他故事](https://medium.com/@greg.perry)</figcaption>

</figure>

### 做任何事之前

在你做任何事之前，你必须建立一个燃烧基地。我不打算一步一步地指导您，因为这超出了本文的范围。你可以自己做。但是，我会给出一些截图，提示您需要做什么，以及如果您试图超越自己并运行这个库包，您可能会在屏幕上看到什么。

### 没什么可说的！

注意，大多数截图的标题中都有相应的 gist 或 Github 条目。例如，您可以通过这些条目进行简单的复制和粘贴。我个人觉得 gists 很难看，我不喜欢在我的文章中使用它们。但是，如果一定要看那些丑陋的东西。你可以通过点击截图的标题或者截图本身来选择。

这里我应该提到的另一点。事实上，我是机器人

我最初是一名 Android 开发人员，而不是 iOS 开发人员，因此下一节更适用于 Android 开发人员。我向你们 iOS 的人道歉。拜托，为了你，我们跳过这一部分，直接看文章的其余部分怎么样？

现在，至于其他人，也许可以打开这个网站， [**将 Firebase 添加到你的 Android 项目**](https://firebase.google.com/docs/android/setup) 中，作为帮助你设置 Firebase 的补充，因为我将简要描述这个东西。在**目录下， *android*** ，将以下两个依赖项添加到 *build.gradle* 文件中。

<figure>[![](img/3cfb2f481c6bf1251034e141c32bb345.png)](https://gist.github.com/Andrious/1b8c343bc208379e7025568556c558b2#file-build-gradle)

<figcaption></figcaption>

</figure>

现在重要的是将以下内容添加到第二个 build.gradle 文件的底部。该文件位于目录下: ***android/app***

<figure>[![](img/32996fa9a7f160cac9a62f60ea56e9ba.png)](https://gist.github.com/Andrious/8615dd07d7584c7aad30a3c72231d697)

<figcaption></figcaption>

</figure>

### 格拉德里有什么

如果您随后尝试类库，可能会遇到以下错误。看起来有一个最低版本要求，你需要在“wrapper”目录下更新你的 g _ radle-wrapper . properties _ file。比如把文件条目，***gradle-4.4-all . zip***改成***gradle-_ 5 . 1 . 1*-all . zip _**。

[![](img/1bdd45c6697758583dcc900e162feea5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--PUH964g3--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2ATDDGRZzmwyY4Q-qW9mdszQ.png)

[![](img/f34a36f2e2499bab91341bf63ef97b85.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Tl9HB7Rj--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AXa9Gvv1VglqwbebLK984Eg.png)

### 在这里获得你的 JSON！

您可能遇到的下一个错误涉及 json 文件。下面列出了一个错误的例子，它抱怨 json 文件丢失了。你需要去 Firebase(记住你的示例应用程序的包名)做一个***Google-services . JSON***配置文件:[你的 Firebase 项目](https://console.firebase.google.com/?pli=1)

[![](img/d3667d750addeab37fafc3733d991223.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--q2OkRCeF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/0%2AC6C_oASPvLibSlC6.png)

### 你会需要 SHA1

除了配置文件，您可能还需要获得应用程序的 SHA1 号码。请访问该网站，[认证您的客户](https://developers.google.com/android/guides/client-auth)。所以，在经历了这一切之后。您已经准备好运行示例应用程序并登录 Firebase。

### 跑吧！

设置好 Firebase 后，让我们继续运行示例应用程序，看看它能做什么。作为一个规则，我使用模拟器从 Android Studio 运行这些东西。

[![](img/aa645f32d5da86385d22d3a7dadbc262.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--feSXSTi6--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/308/1%2A5cZUF8NBxySc2zPIVrYzow.jpeg)

你在这里。当一切就绪，你运行应用程序，你会看到“登录”按钮。现在，与我的大多数库包不同，这个包使用两个插件而不是一个: [**firebase_auth**](https://pub.dev/packages/firebase_auth) 和 [**google_sign_in**](https://pub.dev/packages/google_sign_in) 。因此，如果你有一个谷歌 Gmail 账户，你就可以用这个账户登录应用程序。例如，点击上面显示的“Google 登录”按钮，应用程序将提示您使用您的 Google 帐户登录。

以下是该过程的截图:

[![](img/c3b255820b470e70445bc8cd1d4d433a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--HzkYU5ui--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2A9-h7MGPFHa7XvkcXyPmW1w.png)

[![](img/537dce8c1b2ba0c2bb4f4f94a9792cea.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--tJ6SHA3s--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AyFHsQOV6rGYKjMjw7MqSjA.png)

上面的最后一个截图很有趣，因为应用程序(使用类库的“scopes”参数)请求访问用户的联系人。尽管点击了*允许*按钮，我自己还是得到了 **403** 的响应(权限被拒绝)，所以我的联系人都没有被访问。我怀疑，一个人必须在他们的[开发者控制台](https://console.developers.google.com/apis/api/people.googleapis.com/)下启用这样的权限，但是坦白地说，我不打算这么做。目前来说，知道它有效就足够了。下面的屏幕截图描述了类库使用它的“scopes”参数请求访问用户的联系信息以及电子邮件信息。

<figure>[![](img/209e30447a9c9c76c25cf26405d2ae03.png)](https://github.com/AndriousSolutions/auth/blob/fab7e97246581fa4b6ad91fa95a9d10124347f2c/example/main.dart#L37) 

<figcaption>[最大类](https://github.com/AndriousSolutions/auth/blob/fab7e97246581fa4b6ad91fa95a9d10124347f2c/example/main.dart#L37)</figcaption>

</figure>

成功登录后，您可以点击“结果”选项卡来查看您通过 Auth 对象拥有的许多属性中的一些。这些特性将在稍后介绍。

[![](img/6a8bd7093afa9f13af674b52b253eaa5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--wAhP0oaE--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AczD1ggjqEaStIBIoqAYH3w.png)

### 工作原理

它在示例应用程序中尝试“登录”的函数 **initState** ()中。这个类库中有八个不同的函数用于登录。例如，在下面的屏幕截图中，函数**signingly**()，用于允许用户通过他们的 Google 帐户自动登录，而无需用户参与，如果他们过去已经这样做了。如果是这样，该应用程序将在当时和当时静默登录。当使用 Google 成功登录时，命名参数 *listen* 将被触发。

同样，在 **init** ()函数中，我请求访问用户的联系人。但是，请注意第二个命名参数，*监听器*，提供了一个匿名函数，当用户使用 instead Firebase 而不是 Google 成功登录时，将触发该函数。因此，对于两个不同的插件，涉及到两个不同的事件处理程序。一个名叫，*听着*；另一个*听者*。

<figure>[![](img/b1ef7cf083c4d8a95cad25f81d28f698.png)](https://github.com/AndriousSolutions/auth/blob/fab7e97246581fa4b6ad91fa95a9d10124347f2c/example/main.dart#L20) 

<figcaption>[符号类](https://github.com/AndriousSolutions/auth/blob/fab7e97246581fa4b6ad91fa95a9d10124347f2c/example/main.dart#L20)</figcaption>

</figure>

最后，在上面的截图中，像任何好的类库一样，当应用程序终止时，它通过调用状态对象自己的 **dispose** ()函数中的函数 **dispose** ()，来清理自己。

### 签到时

当用户使用 Google 成功登录时，分配给命名参数 *listen* 的匿名函数再次被触发。在这里，变量 *loggedIn* ，根据变量 *account* 是否为空，被赋予一个布尔值。见上文。

顺便说一下，变量 *account* 的类型是*googlesigniaccount，*并且在成功登录时包含用户的 Google 帐户信息。_ 注意上面的函数， **setState** ()，是 next 调用的。这将导致状态对象的 **build** ()被调用，从而执行函数 **_buildBody** ()。正如您在下面的代码中看到的，如果变量 *loggedIn* 被设置为 true，那么就会在屏幕上显示用户名和电子邮件地址。

<figure>[![](img/bc529b9a4ffe27aa77f6cf216f593f02.png)](https://github.com/AndriousSolutions/auth/blob/fab7e97246581fa4b6ad91fa95a9d10124347f2c/example/main.dart#L92) 

<figcaption>[最大类](https://github.com/AndriousSolutions/auth/blob/fab7e97246581fa4b6ad91fa95a9d10124347f2c/example/main.dart#L92)</figcaption>

</figure>

### 类库有多种方式

下面，你会看到我已经改变了一些东西。对 **init** ()函数进行了修改，现在可以提供两个“事件处理程序”参数，而无需任何参数即可调用“登录”函数。这演示了类库如何允许一个人在其他地方(开发者想要的任何地方)显式地调用*()而让 **init** ()函数在 **initState** ()中初始化。*

 *<figure>[![](img/dd938a27f27b67453c7e33ed46f7894c.png)](https://github.com/AndriousSolutions/auth/blob/fab7e97246581fa4b6ad91fa95a9d10124347f2c/example/main.dart#L31) 

<figcaption>[最大类](https://github.com/AndriousSolutions/auth/blob/fab7e97246581fa4b6ad91fa95a9d10124347f2c/example/main.dart#L31)</figcaption>

</figure>

### 听着！有两个。

再次查看两个指定的参数:*监听器*和*监听*。它们之间的区别在于它们的参数值。函数 listen 用于登录 Google，并被传递了类类型的参数 GoogleSignInAccount。函数 listener 用于登录 Firebase，并被传递类类型的参数 FirebaseUser。您可以在下面的 Auth 类及其命名构造函数 **Auth.init** ()的截图中看到这一点。

<figure>[![](img/b8df61d9c2349d30ac4960175cd6bde0.png)](https://github.com/AndriousSolutions/auth/blob/eec0c203b9cba7cf4d2e2f2795ae312eecfa034b/lib/auth.dart#L51) 

<figcaption>[Auth 类](https://github.com/AndriousSolutions/auth/blob/eec0c203b9cba7cf4d2e2f2795ae312eecfa034b/lib/auth.dart#L51)</figcaption>

</figure>

### 准备，集合！

为了简洁起见，演示这个类库的代码都在一个地方呈现——在 **initState** ()函数中——但是当然不一定都在 **initState** ()函数中完成。例如，假设您有一个应用程序，它在一个地方设置身份验证范围，在另一个地方设置“事件处理程序”选项，或者(您将在后面看到)在代码的许多不同地方多次设置身份验证侦听器。您可以选择使用类库的 setters 来实现这一点。

### 最初，有 Init

让我们开始浏览类库本身。看下面的参数表。你看到了什么？你会看到每个相同的“命名参数”,如果你单独使用插件的话……只是在这里它们被组合在一起。因为它们是命名参数，所以它们都是可选的，但是一旦这个函数被调用，你知道一件事:这些插件的两个实例都已经被创建了。它们被创建并存储在下面突出显示的相应静态变量中。注意，类库本身也存储在静态变量 *_this，*中，因为这个类使用了工厂构造函数。

<figure>[![](img/a5c2c4612b99f32eb0ee8aa907d4e2b2.png)](https://github.com/AndriousSolutions/auth/blob/eec0c203b9cba7cf4d2e2f2795ae312eecfa034b/lib/auth.dart#L51) 

<figcaption>[Auth 类](https://github.com/AndriousSolutions/auth/blob/eec0c203b9cba7cf4d2e2f2795ae312eecfa034b/lib/auth.dart#L51)</figcaption>

</figure>

### 规定

下面列出了 **dispose** ()函数，该函数将在状态对象对应的 **dispose** ()函数中被调用。换句话说，它是在你的应用程序终止时被调用的。这个函数通过注销并从内存中清除大量占用大量宝贵内存的“重”变量来进行清理。

<figure>[![](img/26e8532e2bf826783f1189575d564d51.png)](https://github.com/AndriousSolutions/auth/blob/eec0c203b9cba7cf4d2e2f2795ae312eecfa034b/lib/auth.dart#L77) 

<figcaption>[Auth 类](https://github.com/AndriousSolutions/auth/blob/eec0c203b9cba7cf4d2e2f2795ae312eecfa034b/lib/auth.dart#L77)</figcaption>

</figure>

接下来是实现的两个流订阅，以“捕捉”例如登录或注销时发生的任何和所有身份验证事件。然后是库私有函数， **_init** ()，一个命名的构造函数，它被工厂构造函数调用来真正初始化静态变量的两个插件， *_fireBaseAuth* 和 *_googleSignIn* 。你可以看到“Google”插件就在函数中初始化，而“Firebase”插件实际上是在函数 **_initFirebase** )中进一步初始化的。

<figure>[![](img/2846fcf339da9125cfbcbfe674d14557.png)](https://github.com/AndriousSolutions/auth/blob/eec0c203b9cba7cf4d2e2f2795ae312eecfa034b/lib/auth.dart#L91) 

<figcaption>[Auth 类](https://github.com/AndriousSolutions/auth/blob/eec0c203b9cba7cf4d2e2f2795ae312eecfa034b/lib/auth.dart#L91)</figcaption>

</figure>

事实上，函数 **_initFireBase** ()，是类库中列出的下一段代码。在那里，Firebase 插件被初始化，并在变量 *_fireBaseAuth* 中被引用。注意，对插件的引用也被分配给一个实例变量， *_fbAuth* ，使得它可以通过 _ getter 公开使用。_

<figure>[![](img/8daa027a2c64a6a0cf75be46dd4e6f2d.png)](https://github.com/AndriousSolutions/auth/blob/eec0c203b9cba7cf4d2e2f2795ae312eecfa034b/lib/auth.dart#L121) 

<figcaption>[Auth 类](https://github.com/AndriousSolutions/auth/blob/eec0c203b9cba7cf4d2e2f2795ae312eecfa034b/lib/auth.dart#L121)</figcaption>

</figure>

### 生火

当插件的实例 *FirebaseAuth* 被创建时，它被分配一个监听器函数，_*listfirebaselistents*。列表变量 __ fireBaseListeners_，然后用于存储一个或多个函数，如果发生“身份验证更改”,可以触发这些函数。例如，当类型为*的用户登录和/或注销时。有函数 *fireBaseListener* 和设置器 *listener* ，提供了两种方法将任意数量的“监听器”分配给类库。*

<figure>[![](img/29a58712fd1d1c58d78501c3f7f3dff0.png)](https://github.com/AndriousSolutions/auth/blob/eec0c203b9cba7cf4d2e2f2795ae312eecfa034b/lib/auth.dart#L150) 

<figcaption>[Auth 类](https://github.com/AndriousSolutions/auth/blob/eec0c203b9cba7cf4d2e2f2795ae312eecfa034b/lib/auth.dart#L150)</figcaption>

</figure>

### 听着！

接下来是函数 _ **initListen** ()，正如您在上面看到的，它在 _ **init** ()函数中被调用。四个参数被传入该函数，然后被传入 GoogleSignIn 的“监听器”

注意，第一个参数 *listen* 被间接传递给监听器。它首先被添加到列表变量，_ *googleListeners* ，稍后在函数 __ listGoogleListeners 中进行处理。_ 就是这个函数，\ _ *listGoogleListeners，_ 它实际上是作为第一个参数传递给侦听器的。同样，这种安排允许您为插件分配任意数量的侦听器，因为函数 _* listGoogleListeners_，然后使用 **for 循环**遍历所有这些侦听器。

<figure>[![](img/2cbc67ecd2dab042097e626764060c76.png)](https://github.com/AndriousSolutions/auth/blob/eec0c203b9cba7cf4d2e2f2795ae312eecfa034b/lib/auth.dart#L169) 

<figcaption>[Auth 类](https://github.com/AndriousSolutions/auth/blob/eec0c203b9cba7cf4d2e2f2795ae312eecfa034b/lib/auth.dart#L169)</figcaption>

</figure>

### 听好了 Google！

接下来，就是上面第一个参数的函数，\ \ _*listGoogleListeners*。你可以看到，它确实处理了列表变量 __ googlListeners_，揭示了这个类库的一个深刻的特性——同样，你可以给这个类库分配“任意数量”的侦听器。有一个循环的**在那里依次调用每一个。**

例如，当“认证改变”时，另一个程序员在你的应用程序的另一部分工作可能也需要触发一个事件。这个类库允许为这个特定事件依次触发多个函数。在下面，您会看到在被调用的 setter 中使用了 **add** ()函数， *listen* 。它向列表变量添加了一个类型为 *GoogleListener* 的函数，稍后将在此事件中被调用。最后，有一个名为 **removeListen** ()的函数，用于在必要时删除特定的函数。

<figure>[![](img/0f7d17a6e0dc615c4c8cc0efc99a361f.png)](https://github.com/AndriousSolutions/auth/blob/eec0c203b9cba7cf4d2e2f2795ae312eecfa034b/lib/auth.dart#L193) 

<figcaption>[Auth 类](https://github.com/AndriousSolutions/auth/blob/eec0c203b9cba7cf4d2e2f2795ae312eecfa034b/lib/auth.dart#L193)</figcaption>

</figure>

### 已经存在

函数**已经登录**()，用于减少重要对象的不必要的重新创建。如果用户已经登录，则无需再次登录。它被用于类库中几乎所有的认证函数。它也是公开的，所以开发者也可以使用它。

<figure>[![](img/32e8aa2e66ce338bfd5d02f24ad89d1c.png)](https://github.com/AndriousSolutions/auth/blob/eec0c203b9cba7cf4d2e2f2795ae312eecfa034b/lib/auth.dart#L225) 

<figcaption>[Auth 类](https://github.com/AndriousSolutions/auth/blob/eec0c203b9cba7cf4d2e2f2795ae312eecfa034b/lib/auth.dart#L225)</figcaption>

</figure>

### 匿名

下一个功能允许您匿名登录 Firebase。这样做时，它允许您提供一个“监听器”功能，以便在您成功登录后触发。注意，这个功能和几乎所有其他“登录”功能都有一个 ***尝试..*** 语句来‘捕捉’任何可能发生的错误。然后，使用函数 **_setError** ()，将异常存储在“库-私有”变量 *_ex* 中。

<figure>[![](img/5519c5cbad0da27730e66d7447b9f8c8.png)](https://github.com/AndriousSolutions/auth/blob/eec0c203b9cba7cf4d2e2f2795ae312eecfa034b/lib/auth.dart#L235) 

<figcaption>[Auth 类](https://github.com/AndriousSolutions/auth/blob/eec0c203b9cba7cf4d2e2f2795ae312eecfa034b/lib/auth.dart#L235)</figcaption>

</figure>

为了演示，下面的三个截图显示了当你点击“匿名登录”按钮时会发生什么。

[![](img/64990efe0decb999c604f329e0adf400.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--es4trdhN--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/682/1%2AaARtsZ1R0e_M75ZW8yfzDQ.png)

[![](img/5e52676d09fc6ed4b38e11cd1732c645.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--cOtwnY9S--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/684/1%2AIuBllGIxA8KOZl1Ijoc25Q.png)

[![](img/d7d45b828178b2ddfbc246e3d7802247.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--0kxb53ms--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/680/1%2An9OhpMiCI7J_AXRCcNguzA.png)

### 通过电子邮件

下一个功能将允许用户使用他们的电子邮件和密码登录 Firebase。这里涉及到一些准备工作。你必须进入你的 [Firebase 控制台](https://console.firebase.google.com/u/0/?source=post_page---------------------------)并允许使用密码进行身份验证。这在 Firebase 网站上有更详细的介绍:

[在](https://firebase.google.com/docs/auth/android/password-auth)上使用基于密码的帐户通过 Firebase 进行身份验证

[Android 在 iOS 上使用基于密码的帐户通过 Firebase 进行身份验证](https://firebase.google.com/docs/auth/ios/password-auth)

作为一个题外话，请注意我使用了与插件完全相同的函数名。我想让这个类库能被开发者识别。如果他们已经知道如何使用插件，他们就会知道如何使用这个类库。

<figure>[![](img/cee2fe8155d5dad121cc9bb26b74edf9.png)](https://github.com/AndriousSolutions/auth/blob/eec0c203b9cba7cf4d2e2f2795ae312eecfa034b/lib/auth.dart#L257) 

<figcaption>[Auth 类](https://github.com/AndriousSolutions/auth/blob/eec0c203b9cba7cf4d2e2f2795ae312eecfa034b/lib/auth.dart#L257)</figcaption>

</figure>

### 取

如果你的应用程序要求你提供你的用户已经登录的“提供商”列表，有一个函数可以做到这一点: **fetchProvidersForEmail** ()。为什么？我怎么会知道？！这是你的应用程序。插件提供了这样的功能，类库也是如此。接下来是一种“重置”密码的方法。也就是说，如果你用电子邮件和密码登录 Firebase，**用电子邮件和密码登录**()

<figure>[![](img/01151c12db8d093a2fbca21820886211.png)](https://github.com/AndriousSolutions/auth/blob/eec0c203b9cba7cf4d2e2f2795ae312eecfa034b/lib/auth.dart#L284) 

<figcaption>[Auth 类](https://github.com/AndriousSolutions/auth/blob/eec0c203b9cba7cf4d2e2f2795ae312eecfa034b/lib/auth.dart#L284)</figcaption>

</figure>

<figure>[![](img/8649d82776c9636ba0efbdf212fbb48b.png)](https://github.com/AndriousSolutions/auth/blob/eec0c203b9cba7cf4d2e2f2795ae312eecfa034b/lib/auth.dart#L312) 

<figcaption>[Auth 类](https://github.com/AndriousSolutions/auth/blob/eec0c203b9cba7cf4d2e2f2795ae312eecfa034b/lib/auth.dart#L312)</figcaption>

</figure>

下面是一个尝试使用电子邮件和密码登录的演示。显然，在这种情况下，我还没有执行准备步骤。

[![](img/b9057a06a41decd80be082b8d0984981.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--YFJQjdfO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/682/1%2Aolt3mbCNfgi4cIPKK0ySjw.png)

[![](img/ad24777573c487b726efafb69f5925ac.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Aa2RqHmM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/680/1%2A1u1pS3yq7sspQOYMz1JBug.png)

[![](img/d3b62801997e6db65b100cdee2f74e71.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--_ZP08hm7--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/676/1%2A4Oc-XxOMmCGU5wX43ni7NA.png)

下面显示的示例代码突出显示了显示错误消息所涉及的代码。它演示了如何使用类库执行异步操作。

<figure>[![](img/574547e5f0118b4c8d53f9ddf93757f5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--gVwOWmSm--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AHIeKRKv9HgCmmIwDOP52nA.png)

<figcaption>[SignInDemoState 类](https://github.com/AndriousSolutions/auth/blob/fab7e97246581fa4b6ad91fa95a9d10124347f2c/example/main.dart#L92)和 [_buildBody()](https://github.com/AndriousSolutions/auth/blob/fab7e97246581fa4b6ad91fa95a9d10124347f2c/example/main.dart#L138)</figcaption>

</figure>

### 你的凭证

一些系统(比如 Google)使用类类型 AuthCredential 来进一步从登录用户那里获取身份验证信息。事实上，在这个类库的后面，它被用来在使用脸书登录时获取这样的信息。如果你愿意，你也可以使用下面的功能。

<figure>[![](img/3f6dc2f2a4a6ba26fe007dd858dd32e4.png)](https://github.com/AndriousSolutions/auth/blob/eec0c203b9cba7cf4d2e2f2795ae312eecfa034b/lib/auth.dart#L343) 

<figcaption>[Auth 类](https://github.com/AndriousSolutions/auth/blob/eec0c203b9cba7cf4d2e2f2795ae312eecfa034b/lib/auth.dart#L343)</figcaption>

</figure>

### 自定义标志

当您必须“刷新会话中的日志”时，将使用此功能登陆 Firebase 只能持续一定的时间(据说是一个小时)。这篇文章将提供一些见解，[firebase sign in withcustomtoken broken](https://stackoverflow.com/questions/45094359/firebase-signinwithcustomtoken-broken?source=post_page---------------------------#answer-50350714)

<figure>[![](img/bac9dd52cce9f936d973d31d8ddf097c.png)](https://github.com/AndriousSolutions/auth/blob/eec0c203b9cba7cf4d2e2f2795ae312eecfa034b/lib/auth.dart#L372) 

<figcaption>[Auth 类](https://github.com/AndriousSolutions/auth/blob/eec0c203b9cba7cf4d2e2f2795ae312eecfa034b/lib/auth.dart#L372)</figcaption>

</figure>

就像下面官方文档陈述的下一个功能，“*为可以国际化的 auth 操作设置面向用户的语言代码，比如【sendEmailVerification】。这种语言代码应该遵循 IETF 在 BCP47* 中定义的约定

<figure>[![](img/25c12099fc63a5228342ab00af34725a.png)](https://github.com/AndriousSolutions/auth/blob/eec0c203b9cba7cf4d2e2f2795ae312eecfa034b/lib/auth.dart#L402) 

<figcaption>[Auth 类](https://github.com/AndriousSolutions/auth/blob/eec0c203b9cba7cf4d2e2f2795ae312eecfa034b/lib/auth.dart#L402)</figcaption>

</figure>

### 来自火基

以下函数 *_setUserFromFirebase* ，每次只登录 Firebase 而不登录 Google 时都会被调用。这样，该参数的类型为 *FirebaseUser* 。用户配置文件信息被分配到许多静态变量中，然后您可以使用类库的相应 getters 来轻松访问这些变量。

<figure>[![](img/ab0756cf37a2fc352fc5d6b1f4ad6ce4.png)](https://github.com/AndriousSolutions/auth/blob/eec0c203b9cba7cf4d2e2f2795ae312eecfa034b/lib/auth.dart#L412) 

<figcaption>[Auth 类](https://github.com/AndriousSolutions/auth/blob/eec0c203b9cba7cf4d2e2f2795ae312eecfa034b/lib/auth.dart#L412)</figcaption>

</figure>

### 用谷歌登录 Firebase

下一个函数让您使用 Google 登录 Firebase。您登录 Google，然后使用提供的 Google 凭据登录 Firebase。

这就是函数***_ setFireBaseWithGoogle***()的作用。它会在代码的后面找到。它从“Google”登录中获取信息，并使用这些信息登录 Firebase。

<figure>[![](img/b603443dbc5e7f4fbee66261455783d1.png)](https://github.com/AndriousSolutions/auth/blob/eec0c203b9cba7cf4d2e2f2795ae312eecfa034b/lib/auth.dart#L441) 

<figcaption>[Auth 类](https://github.com/AndriousSolutions/auth/blob/eec0c203b9cba7cf4d2e2f2795ae312eecfa034b/lib/auth.dart#L441)</figcaption>

</figure>

### 默默地

如果用户以前已经登录过，此功能将为他们登录。也就是说，让他们登录谷歌。然后，它使用谷歌凭证登录 Firebase。

### 内部错误

在撰写本文时，我注意到插件在“第一次”登录谷歌时出现了“内部错误”。我只是让类库再次尝试(在 catch 语句中)，它成功地登录了。我在这里错过了一些东西…也许有人可以贡献和帮助。

<figure>[![](img/2e354ef78ad8c3225e9a7d21b281d017.png)](https://github.com/AndriousSolutions/auth/blob/eec0c203b9cba7cf4d2e2f2795ae312eecfa034b/lib/auth.dart#L455) 

<figcaption>[Auth 类](https://github.com/AndriousSolutions/auth/blob/eec0c203b9cba7cf4d2e2f2795ae312eecfa034b/lib/auth.dart#L455)</figcaption>

</figure>

### 签到

下一个函数用于让用户手动登录他们的 Google 帐户。我经常使用这个函数，在本文开头的例子中，当你点击“登录”按钮时，就是这个函数。

<figure>[![](img/a3aa437e835503f09f7686e07325ec68.png)](https://github.com/AndriousSolutions/auth/blob/eec0c203b9cba7cf4d2e2f2795ae312eecfa034b/lib/auth.dart#L490) 

<figcaption>认证类</figcaption>

</figure>

### 来自谷歌的用户

下一个函数以类对象的形式将成功登录到 Google 的帐户信息, *GoogleSignInAccount* 。然后传递给这个函数，并提供两个令牌值来登录 Firebase。类库的许多静态变量又被赋予了“用户配置文件信息”,它们很容易被相应的设置器访问。

<figure>[![](img/ae1f29e4452247732a43dc9abc6bc634.png)](https://github.com/AndriousSolutions/auth/blob/eec0c203b9cba7cf4d2e2f2795ae312eecfa034b/lib/auth.dart#L516) 

<figcaption>[Auth 类](https://github.com/AndriousSolutions/auth/blob/eec0c203b9cba7cf4d2e2f2795ae312eecfa034b/lib/auth.dart#L516)</figcaption>

</figure>

### 这是真实的信息

同样，一旦用户登录，你就可以知道用户的一些信息。类库通过使用大量的*getter*来提供这种访问，这些 getter 在示例应用程序本身的“Results”选项卡下进行了演示。

[![](img/d51ceda2349039c95648a032effde9b1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--QGYKgIaU--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/682/1%2AGWPZlouZ45d9yivlNIOwxA.png)

[![](img/b6fb06d2885c62730c303587c1dade15.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--hGpaG_Wf--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/692/1%2AJWopeC3PwJM_SZJk7An1ZQ.png)

[![](img/b2b27a6821d92a6cab8b05ebb022a6b5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--fuPkQVDq--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/692/1%2Amyeh1YG3DVQ7L2Kg7kIZJw.png)

<figure>[![](img/c08bd0bd8550224fadeedda130e4025c.png)](https://github.com/AndriousSolutions/auth/blob/fab7e97246581fa4b6ad91fa95a9d10124347f2c/example/main.dart#L151) 

<figcaption>[_authResults Widget](https://github.com/AndriousSolutions/auth/blob/fab7e97246581fa4b6ad91fa95a9d10124347f2c/example/main.dart#L151)</figcaption>

</figure>

### 脸书

您可以看到下面列出了一小段使用**flutteroath**()函数的代码。然后它调用前面遇到的函数 **signInWithCredential** ()。

<figure>[![](img/8ff8d3c7580764afc84e2ee9d8edb877.png)](https://github.com/AndriousSolutions/auth/blob/eec0c203b9cba7cf4d2e2f2795ae312eecfa034b/lib/auth.dart#L551) 

<figcaption>[Auth 类](https://github.com/AndriousSolutions/auth/blob/eec0c203b9cba7cf4d2e2f2795ae312eecfa034b/lib/auth.dart#L551)</figcaption>

</figure>

### 碎碎念

此时，这个类库不使用 Twitter 登录。有这样一个插件:[*flutter _ Twitter _ log in*](https://pub.dartlang.org/packages/flutter_twitter_login?source=post_page---------------------------)。不过现在，我会邀请你阅读，[**Flutter+Firebase——通过 Etimbuk**](https://medium.com/@euedofia/flutter-firebase-authenticate-with-twitter-d73c0602bb0b) 向 Twitter 认证。他介绍了这个插件，正是这个插件提供了两个令牌，您可以依次传递给下面的函数。

<figure>[![](img/cd83e0be593c479fc529787ab957fe9b.png)](https://github.com/AndriousSolutions/auth/blob/eec0c203b9cba7cf4d2e2f2795ae312eecfa034b/lib/auth.dart#L572) 

<figcaption>[Auth 类](https://github.com/AndriousSolutions/auth/blob/eec0c203b9cba7cf4d2e2f2795ae312eecfa034b/lib/auth.dart#L572)</figcaption>

</figure>

### 断开

退出就是退出 Google 后端和 Firebase 后端。回到应用程序，“静默”签名是可能的，无需用户再次手动登录。但是，如果您注销然后断开连接，用户将不得不再次手动登录。

<figure>[![](img/dbf64a7d397e29c654987723335b6b26.png)](https://github.com/AndriousSolutions/auth/blob/eec0c203b9cba7cf4d2e2f2795ae312eecfa034b/lib/auth.dart#L599) 

<figcaption>[Auth 类](https://github.com/AndriousSolutions/auth/blob/eec0c203b9cba7cf4d2e2f2795ae312eecfa034b/lib/auth.dart#L599)</figcaption>

</figure>

### 蹲下

当应用程序终止时，明确退出是一个好习惯。 **dispose** ()函数调用下面列出的 **signOut** ()函数。你可以看到它只是分别调用插件的**签出**()函数。请注意，您没有断开连接，但是后面的函数会断开连接。

<figure>[![](img/9c22e2b22fc83488b3f70b99fde1e3f9.png)](https://github.com/AndriousSolutions/auth/blob/eec0c203b9cba7cf4d2e2f2795ae312eecfa034b/lib/auth.dart#L605) 

<figcaption>[Auth 类](https://github.com/AndriousSolutions/auth/blob/eec0c203b9cba7cf4d2e2f2795ae312eecfa034b/lib/auth.dart#L605)</figcaption>

</figure>

### 在或不在

接下来的两个函数测试你是否已经“登录”在最后两个函数中再次调用了 **init** ()函数，以确保在这种情况下，变量 _ _ Google sign in _ 不为空。

<figure>[![](img/10c8e5eca89cf6d134fd601cfac335a0.png)](https://github.com/AndriousSolutions/auth/blob/eec0c203b9cba7cf4d2e2f2795ae312eecfa034b/lib/auth.dart#L613) 

<figcaption>[Auth 类](https://github.com/AndriousSolutions/auth/blob/eec0c203b9cba7cf4d2e2f2795ae312eecfa034b/lib/auth.dart#L613)</figcaption>

</figure>

接下来，是记录类库可能遇到的任何错误的内部函数。有一个函数， **getError** ()，用于检索这样的错误，

<figure>[![](img/a7838b3fc2b84c64e9e0dc7b033fa801.png)](https://github.com/AndriousSolutions/auth/blob/eec0c203b9cba7cf4d2e2f2795ae312eecfa034b/lib/auth.dart#L638) 

<figcaption>[Auth 类](https://github.com/AndriousSolutions/auth/blob/eec0c203b9cba7cf4d2e2f2795ae312eecfa034b/lib/auth.dart#L638)</figcaption>

</figure>

<figure>[![](img/ded0c2c231b43e8f2d4a03460a4f08c1.png)](https://github.com/AndriousSolutions/auth/blob/eec0c203b9cba7cf4d2e2f2795ae312eecfa034b/lib/auth.dart#L646) 

<figcaption>[Auth 类](https://github.com/AndriousSolutions/auth/blob/eec0c203b9cba7cf4d2e2f2795ae312eecfa034b/lib/auth.dart#L646)</figcaption>

</figure>

### 设定者信息

类库再次以几个*getter*传递用户的配置文件信息结束。当然，它们会在函数 **_setUserFromFirebase** ()或**_ setfirebaseuserfromggoogle**)中更新。

<figure>[![](img/e2e50bb3494265bd3032c26e9cb7e8f8.png)](https://github.com/AndriousSolutions/auth/blob/eec0c203b9cba7cf4d2e2f2795ae312eecfa034b/lib/auth.dart#L673) 

<figcaption>[Auth 类](https://github.com/AndriousSolutions/auth/blob/eec0c203b9cba7cf4d2e2f2795ae312eecfa034b/lib/auth.dart#L673)</figcaption>

</figure>

### 结论

这个类库中有很多冗余。这就是它成为类库的原因。内心丑陋，但外表光滑。只需几个电话，你就完成了应用程序的用户认证。这个类库只是工具箱中的另一个工具，让在 Flutter——移动开发的最佳平台——中开发移动应用变得更加简单。

[→格雷格·佩里的其他故事](https://medium.com/@greg.perry?source=post_page---------------------------)

[![](img/e5c9c94e4b10026aadd6f5f5a18f2f5a.png)](https://dev.to/andrious/sqlite-in-flutter-3na5-temp-slug-5894831)

<figure>[![](img/1983230ddfabd4733ca91a716206d851.png)](https://medium.com/@greg.perry/store-and-read-your-apps-preferences-4139e836cfe9) 

<figcaption>其他类库文章</figcaption>

</figure>***