# 用 React Native 和 Firebase 在 Android 上实现脸书登录

> 原文：<https://dev.to/kris/implementing-facebook-login-on-android-with-react-native-and-firebase-1bj2>

<figure>[![](img/d7945233c83ae19e8f22596a1e343ae3.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--miuJHavC--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2A1Xh7ODuMl6sErOGqpMIWAw.jpeg) 

<figcaption>[图片来源](https://unsplash.com/photos/AGqzy-Uj3s4)</figcaption>

</figure>

本教程是关于如何在使用 [React Native](https://www.instamobile.io/app-templates/) 开发时将脸书登录与 Firebase 集成的快速指南。通过使用 Firebase 和脸书登录，您将能够理解如何正确地使用和集成 Firebase，以及在登录的同时实现脸书身份验证。这个过程也将帮助开发人员了解很多关于 Firebase 的知识，Firebase 可以作为轻量级且易于配置的后端使用。

如今，脸书登录在许多应用程序中变得非常重要，因为它将为用户提供比手动输入凭据更简单的登录方式。这是提高应用程序 UX 的一个简单而有效的方法。

首先，您将了解如何使用 [GitHub 存储库](https://github.com/invertase/react-native-firebase-starter)集成 React Native Firebase starter 样板文件。

> **react-native-firebase-starter**是一款预集成了 react-native-fire base 的 react native 应用程序，因此您可以快速上手。

然后，您将把 **react-native-fbsdk** 集成到您的 React Native 项目中，以启用脸书登录。这个插件是一个 React 原生包装器，围绕着 Android 和 iOS 的脸书 SDK，提供对脸书登录、共享、图形请求、应用事件等的访问。所有需要的配置将在下面详细解释。在本教程结束时，您将能够创建自己的 React Native Firebase 项目，其中包括一个脸书登录。

### 设置 Firebase 样板文件

为了快速方便地为我们的项目设置，我们将克隆一个**react-native-fire base-starter**包。这是您的项目的样板文件，将包含 Firebase 设置所需的所有配置。我们只需将 GitHub repo 克隆到我们的本地驱动器中。克隆回购协议的命令如下:

```
git init
git clone https://github.com/invertase/react-native-firebase-starter 
```

因此，您将在驱动器中看到一个 React 本地项目文件夹，其中包含所有需要的配置。现在我们需要安装这些配置。

为此，我们运行 yarn，它将安装 React Native Firebase 项目所需的所有插件和软件包。这就完成了 starter 项目的设置，我们将在项目文件中看到 google-service.json，它是从 Firebase 控制台生成的。

应用程序创建完成后，我们需要选择 Android 应用程序，并在 IDE 中打开 MainApplication.java。在那里，我们将看到一个名为 com . invertase . rnfirebase started _ 的导入包。_ 我们需要复制 com . invertase . rnfirebasestarter 并粘贴到 Firebase 控制台中，如下图截图所示:

[![](img/590bdd64c1a5f2fb595a5a52b4ae5cc7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--6toWdTGL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2ALFxks0EFM0kt1d57zDMPNg.png)

遵循截图中的指导点，将 google-services.json 文件下载到我们的项目文件夹中:

[![](img/3eb2bc1bf3d794e836a7a8614dbbb4b6.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--L2AOfuAA--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/844/1%2A1iEQAApXSu09hVZwTYGBiw.png)

将 google-services.json 文件下载到您的项目文件夹后，我们需要将该文件从**主项目文件夹**移动到**项目的 app 文件夹中。**

[![](img/e82cb3031ecbfb39f66360ccddb2bbb6.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--nBOhU70t--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/298/1%2A-xN0dbQkbl-I2A10F1NoXg.png)

然后，我们使用下面的命令启动应用程序/项目:

react-原生运行-android

然后，我们将在 Android 模拟器中看到以下结果:

[![](img/80276277e701cd264dcb8b92b776ca5f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--mxY1QylK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/442/1%2AgFJTsg9xXQiABAxGf_JxgA.png)

最后，我们可以看到我们的 Firebase 集成工作正常。现在，我们可以继续将脸书登录添加到我们的应用程序。

> 移动开发的下一次革命？机器学习。不要错过这个新兴交叉路口的最新消息。[注册](https://www.fritz.ai/newsletter?utm_campaign=heartbeat-newsletter-fomo2&utm_source=heartbeat)获取我们的移动开发人员和机器学习者的每周更新。

### 设置脸书 SDK 插件

因为我们已经使用官方文档集成了 Firebase 插件。为了集成脸书登录，[文档](https://rnfirebase.io/docs/v5.x.x/auth/social-auth#Facebook)建议开发者使用 [react-native-fbsdk](https://github.com/facebook/react-native-fbsdk) 包。因此，让我们从安装插件开始，如下所示:

> *使用纱线:*

```
yarn add react-native-fbsdk 
```

> *或者，使用 NPM(节点包管理器):*

```
npm install react-native-fbsdk 
```

安装脸书 SDK 包后，我们必须链接 SDK 来配置 iOS 和 Android 项目。我们可以通过在项目控制台中使用下面这段代码来做到这一点:

```
react-native link react-native-fbsdk 
```

现在，转到 app/src/main/java/com//下的 MainApplication.java 和 MainActivity.java 来完成设置。

在 MainApplication.java，我们需要添加一个 CallbackManager 类型的实例变量及其 getter。您可以使用下面的代码片段来做到这一点:

```
import com.facebook.CallbackManager;
import com.facebook.FacebookSdk;
import com.facebook.reactnative.androidsdk.FBSDKPackage;
import com.facebook.appevents.AppEventsLogger;
...

public class MainApplication extends Application implements ReactApplication {

  private static CallbackManager mCallbackManager = CallbackManager.Factory.create();

  protected static CallbackManager getCallbackManager() {
    return mCallbackManager;
  }
    //... 
```

app 项目中使用的上述代码示例如下所示:

[![](img/cf1ad152696081f86ad28a8399cfbb5d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--8iO-kKyT--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AS3QyOV79wO8G87nFztFVdg.png)

现在，我们需要使用 getPackages()方法注册 SDK 包。按照下面的代码片段来做:

```
private final ReactNativeHost mReactNativeHost = new ReactNativeHost(this) {
    @Override
    public boolean getUseDeveloperSupport() {
      return BuildConfig.DEBUG;
    }

    @Override
    protected List<ReactPackage> getPackages() {
      return Arrays.<ReactPackage>asList(
          new MainReactPackage(),
          new FBSDKPackage(mCallbackManager)
      );
    }
}; 
```

上面的代码在 app 项目中的集成如下面的截图所示:

[![](img/84416ea30a0f43b6db495fb444cdc4ca.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--268UodIV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AzPG8MIOqKyyBZNNpDRSXaw.png)

现在，在 MainActivity.java 文件中，我们需要覆盖方法 onActivityResult():

```
import android.content.Intent;

public class MainActivity extends ReactActivity {

    @Override
    public void onActivityResult(int requestCode, int resultCode, Intent data) {
        super.onActivityResult(requestCode, resultCode, data);
        MainApplication.getCallbackManager().onActivityResult(requestCode, resultCode, data);
    }
    //... 
```

同样，我们项目环境中的上述代码:

[![](img/c095d54164d1f5933ed142e312a207e3.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--48h9QvTu--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2Ak45moVaP1Ux0IuI8h_KS-Q.png)

完成后，通过运行我们的应用程序，在 IDE Android 移动仿真器中测试结果。结果可以在下面的截图中看到:

[![](img/bf136f1c6122f7c7456af348413ef93d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ju0545XJ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/442/1%2AIGlReF3Q87oKA-vwykOf-Q.png)

上面的截图显示了错误的发生。此错误是由于未建立 APP_ID 造成的。你可以在这里找到这个错误[的解决方法。](https://stackoverflow.com/a/51651988/733676)

现在，打开 Android/app/src/main/androidmanifest . XML 文件，查找标签，确认这个元数据标签存在:

```
<meta-data android:name="com.facebook.sdk.ApplicationId"
          android:value="@string/facebook\_app\_id"/> 
```

然后，我们打开 Android/app/src/main/RES/values/strings . XML 文件，并确认有一个 facebook_app_id 字符串标记，其值为我们的应用 id:

```
<string name="facebook\_app\_id">YOUR\_APP\_ID\_HERE</string> 
```

最后，我们再次运行 react-native run-android，在 IDE android 模拟器中得到以下结果:

[![](img/249c4bc08c290c5a1f8da3d5bd61b5f7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--RsB1R0oO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/442/1%2ADSsBaD7x-WfeSn1J2QW03w.png)

最后，我们的 react-native-fbsdk 的配置完成了。

但是我们需要记住，要激活 Firebase 控制台上的脸书登录，我们必须从我们的 [facebook 开发者帐户](https://developers.facebook.com/)添加一份 APP_ID 和 APP_SECRET。

[![](img/1345c55f351e5c89e910345c1f31cd53.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--WPTKHDyo--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AWwPDXLkDcHr2s2fLmq2XeA.png)

### 为设置脸书登录的编码阶段

在这里，我们按照 rnfirebase.io 提供的[指南](https://rnfirebase.io/docs/v5.x.x/auth/social-auth#Facebook)来设置脸书登录时所需的社交认证。这里，我们将尝试证明指南中给出的正常流程是可行的。

在 App.js 中，从 react-native-fbsdk 包中导入 AccessToken 和 LoginManager 模块:

```
import { AccessToken, LoginManager } from "react-native-fbsdk"; 
```

之后，我们需要创建一个函数来处理脸书登录逻辑。这里，我们创建了一个名为 facebookLogin()的异步函数来处理所有的登录、权限、身份验证和错误抛出(如果发生的话)的逻辑。您可以在下面的代码片段中看到其中的逻辑: