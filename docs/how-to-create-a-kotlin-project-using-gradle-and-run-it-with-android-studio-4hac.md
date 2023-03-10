# 如何使用 Gradle 创建一个 Kotlin 项目并在 Android Studio 中运行

> 原文：<https://dev.to/cutiko/how-to-create-a-kotlin-project-using-gradle-and-run-it-with-android-studio-4hac>

在 Android 上工作有很多方面，其中之一是`gradle`，配置它有时会很难，但这是非常值得的，在大多数严肃的公司里都有专门从事 gradle 构建的人。所以我想学习更多关于 gradle 的知识，并挑战自己使用 Android Studio 创建一个简单的 Kotlin 项目，然后在其中运行，以利用 IDE 的强大功能。

## 1。安装 Kotlin

使用您选择的安装程序，[参见手册](https://kotlinlang.org/docs/tutorials/command-line.html) (brew、sdk、ports)

使用 brew 将会是

```
brew install kotlin 
```

如果您唯一想做的事情是创建一个 hello world，那么您可以按照上面的文档使用终端来完成并运行它。

## 2。安装梯度

这不应该影响你的 Android Studio 项目，因为 Android 项目使用一个动态的在线梯度，这就是为什么你可以在 Android Studio 上离线配置。如果离线的 Android Studio 激发了你的兴趣[这篇文章](https://hackernoon.com/speed-up-gradle-build-in-android-studio-80a5f74ac9ed)应该会对你有所帮助(我有过使用离线的混合体验，不推荐也不气馁，找到你的味道)。

再次使用您选择的安装程序安装 gradle，[参见手册](https://gradle.org/install/)

有了啤酒，它就会

```
brew install gradle 
```

在此之前，你需要有 Java，如果你来自 Android Studio，这应该不成问题。

### 3。创建一个 Gradle 项目

我们现在将使用 cli 创建一个 gradle 项目，有一些关于 Gradle 和官方 Kotlin 的信息，但我很困惑，所以我宁愿给你我自己的配方。

1.  创建项目

```
mkdir devDotTo
cd devDotTo
gradle init 
```

这将触发交互式 CLI，从字面上阅读它说什么，并遵循它，但由于我一无所知，我得到了吨的错误，所以让我饶了你的尝试和错误。

1.  选择库(数字 3 并输入)
2.  选择 Kotlin(数字 4 并回车)
3.  这最后一个选择是用于 gradle 文件的语言，所以选择一个你更舒服的。我习惯了 Android，所以我喜欢 groovy，在我深刻理解我在做什么之前，我不会换到 Kotlin 扩展。
4.  接下来的两个选项是命名，因此您可以键入适合您和您的标准的任何内容，或者只需按两次 enter。

这创建了一个带有一个默认类和一个默认测试的模板项目，我们将不得不在 Android Studio 上稍微修改一下。

### 4。使用 Android Studio 运行它

1.  打开 Android Studio 并打开项目，点击目录并点击打开

[![open on android studio](img/98b5f83ceb5442ebd9b3c0eaaf46d859.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--zqwVv-dW--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/18umeey0f07b8b4q1bzs.png)

1.  让 Android Studio 来做这件事...如果你需要冷却你的电脑，吹一些空气，说真的，如果你习惯于 Android 项目，你可以在窗格上选择“Android”视觉，也就是文件的左边标签

2.  打开文件`Library.kt`将是那里唯一的文件。我们将添加**主方法**，这样我们就可以配置我们的 IDE 来运行它。有一个默认的方法，如果你删除它，确保在测试文件夹中也删除它。您的文件应该如下所示:

```
class Library {

    companion object {

        @JvmStatic
        fun main(args: Array<String>) {
            println("DEV DOT TO")
        }
    }

    fun someLibraryMethod(): Boolean {
        return true
    }
} 
```

如你所见，我们必须将它添加到`companion object`中。

1.  现在我们将配置 Android Studio 来运行它。点击顶栏上的“添加配置”

[![add configuration button](img/9765df4faebc0e766d341da42bb095f3.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Rwo00brj--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/9456kg7in7hgzybzaocv.png)

在显示的对话框中，不要点击模板，点击加号按钮(您不希望编辑模板，您希望使用模板来创建配置)。选择 Kotlin 模板:

[![add configuration kotlin](img/a104c84adc2f41920a7e244a49c63a43.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ditE_np3--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/7jmw0x20oo3f7y5tgac0.png)

现在你必须选择主类，如果你按下 3 个点，你会马上看到它，因为它有`main method`

[![3 dots](img/a649901b48fa178c052e9d6145b9fed4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--kia4bInq--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/gps2rv8w1ooz8ejvug2n.png)

[![main class](img/3a8da448a5eadd0ea16354a08546a789.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--yeWYahkD--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/9fwtwpkeskiqdylrg5mx.png)

现在你唯一要做的就是**保存配置**。默认的名字是“未命名”，我喜欢把它命名为“运行”。

你可以点击运行按钮，或者在 Mac 上点击`ctrl + r`，在 Windows 上点击`shift+F10`

[![run](img/40c38e73376310025240491ddbe0ac44.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--sgO-pM36--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/o7rjz7iqbwovqmnn7pbf.png)

万岁！控制台里有我们的日志

[![DEV DOT TO](img/7bc43dd644bcd9fb2dc28c6b2ea87aac.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Q-7XcBpp--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/zkmzwrvyp4rqhxgavhel.png)