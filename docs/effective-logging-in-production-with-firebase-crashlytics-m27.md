# 利用 Firebase Crashlytics 在生产中进行有效测井

> 原文：<https://dev.to/funkyidol/effective-logging-in-production-with-firebase-crashlytics-m27>

日志记录，尤其是崩溃日志记录，是开发者机器之外可用的任何应用的一个非常重要的方面。它只是获取应用程序崩溃时生成的内部应用程序细节的行为。无论是提供给测试人员还是提供给公众使用，捕捉崩溃和识别错误对于开发人员提供高质量的产品是至关重要的。在移动应用程序开发中，Firebase Crashlytics 是使用最广泛的崩溃日志工具之一。Crashlytics 由 Fabric(归 Twitter 所有)构建，后来被 Google 收购并集成到 Firebase 中。

在 Envision，我们非常依赖 Crashlytics 让我们深入了解我们的应用质量以及用户在使用我们的应用时可能面临的任何问题。有了 Crashlytics，我们可以了解用户面临的崩溃，以及我们的应用程序可能遇到的任何内部问题，这些问题对最终用户来说不一定是可见的。

### 目标

我们目前对 Crashlytics 的目标是:

1.  崩溃日志
2.  问题识别和诊断
3.  应用行为洞察

### 实现

Crashlytics 中有许多通常不为人知的特性。我们想强调我们使用 Crashlytics 实现上述目标的一些方法。

附:以下细节是 Android 特有的，但可以很容易地翻译成 iOS。下面的所有链接也包含了在 iOS 中实现 Crashlytics 的步骤和细节

*   自动崩溃日志记录:作为核心特性集的一部分，自动崩溃日志记录是您在项目中集成 Crashlytics 时所得到的。你可以在这里找到整合步骤[。如果您想在活动开发期间排除调试版本污染您的崩溃日志，您可以遵循这里的步骤](https://firebase.google.com/docs/crashlytics/get-started?platform=android)。
*   非致命日志记录:
    非致命日志记录是我们开始使用 Crashlytics 的高级功能的地方，它有助于诊断和修复应用程序使用时记录的崩溃以外的问题。当在 Firebase 中手工记录异常时，它们被记录为非致命的。在应用程序中使用非致命性的最佳方式是记录在 try/catch 块的 catch 部分生成的所有异常。此外，当我们的应用程序处于不良状态，需要检查原因并确定其对应用程序的影响时，我们还会创建异常并记录它们。

    ```
    try {
          methodThatThrows()
      } catch (e: Exception) {
          Crashlytics.logException(e)
          // handle your exception here
      } 
    ```

*   Debug Logging:
    我们使用 Timber 来管理 Android 上的日志，这使得我们在 debug &生产版本中管理日志变得非常容易。以下配置在调试版本中将日志打印到控制台，但在生产版本中，它将调试日志和异常一起发送到 Firebase。

    ```
    override fun onCreate() {
          super.onCreate()
             if (!BuildConfig.DEBUG) {
                 Fabric.with(this, Crashlytics())
                 Timber.plant(CrashReportingTree())
             } else {
                 Timber.plant(Timber.DebugTree())
             }
       }

       private inner class CrashReportingTree : Timber.Tree() {
          override fun log(priority: Int, tag: String?, message: String, throwable: Throwable?) {
             if (priority == Log.ERROR || priority == Log.DEBUG) {
                Crashlytics.log(priority, tag, message)
                if (throwable != null) {
                   Crashlytics.logException(throwable)
                }
             } else return
          }
      } 
    ```

*   用户 Id/电子邮件标记:
    Firebase 能够将从特定用户收集的日志附加到他们唯一的用户 Id 上。这使我们能够诊断和修复用户向我们报告的问题，尤其是在 beta 测试阶段。在 Firebase 控制台中，我们能够过滤特定用户的崩溃和非致命日志。
    `Crashlytics.setUserIdentifier(userId)`

*   在 Envision，我们试图遵循测试版和发布版的正确版本，以便 Firebase 中收集的所有崩溃和日志可以针对特定版本快速过滤，并在下一个版本中修复。内部测试构建也遵循相同的方案，以便额外的数据可以专门记录到测试版本中。

### 瓶颈

即使有了所有这些先进的功能，Firebase 在某些场合仍然有所欠缺。

1.  有限的日志收集存储在 firebase 中，每个用户的调试日志存储限制为 64KB，旧日志会随着新日志的到来而被删除。这可能是问题诊断的一大障碍，因为很多时候，当我们发现问题并开始诊断时，日志已经不见了。
2.  有限的过滤选项 Firebase 中的过滤选项数量受限于应用程序版本、崩溃与非致命性以及用户 Id。所有这些对于基本的崩溃日志使用都很好，但是对于更复杂和罕见的问题，这些过滤选项是有限制的。无法根据特定的 OEM、设备类别或设备型号过滤日志。这对于 Android 应用程序尤其有用，因为在这些应用程序中，可用设备和 OEM 厂商的数量巨大，并且设备特定的问题很常见。
3.  没有搜索功能 Firebase crashlytics 中没有搜索特定崩溃或调试日志的搜索功能，这在处理特定问题或诊断罕见的崩溃时再次变得非常重要。
4.  使用 Firebase 控制台的缓慢界面整体感觉非常缓慢，页面加载和渲染时间非常长。那是在你使用 Chrome 浏览器的时候。我在使用 Firefox 时速度变得很慢。
5.  没有自定义警报在问题或趋势警报方面，Firebase 非常有限。他们只针对新问题、回归问题和趋势问题发送电子邮件提醒。无法创建自定义警报，例如针对特定问题或特定设备的趋势警报。
6.  目前无法直接查看调试日志，查看调试日志的唯一方法是在崩溃日志或非致命崩溃中找到它们。我们知道调试日志的收集和关联是基于每一个崩溃/非致命的，但我们也应该有办法遍历每个用户 Id 或设备型号的调试日志。
7.  有限的仪表板和图表统计在 Firebase Crashlytics 中只有一个可用的图表，即每天、每周或每月的总崩溃次数。我们也应该有更多的方法来可视化崩溃统计数据。Fabric 的仪表板上有非常有用的图表，这些图表提供了很多价值，而无需我们去挖掘这些信息。

### 往前走

随着我们的应用程序和用例的成熟，Crashlytics 已经到了这样一个地步，在实现我们快速识别和解决问题的目标方面，我们经常会受到限制。我们现在正在寻找一些其他第三方和更先进的崩溃日志解决方案，如 [BugSnag](//notion://www.notion.so/letsenvision/bugsnag.com) ，这可能是付费的，但在达到更高质量方面增加了我们产品的价值。

*最初发布于[https://medium . com/letsen vision/effective-logging-in-production-with firebase-crashlytics-9 FB 3 af 9 f 242d](https://medium.com/letsenvision/effective-logging-in-production-with-firebase-crashlytics-9fb3af9f242d)T3】*