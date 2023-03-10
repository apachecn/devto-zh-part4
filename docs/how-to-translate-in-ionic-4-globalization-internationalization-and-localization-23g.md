# 如何翻译 Ionic 4 -全球化、国际化和本地化

> 原文：<https://dev.to/enappd/how-to-translate-in-ionic-4-globalization-internationalization-and-localization-23g>

* * *

> 本博客的完整代码可在[https://github.com/enappd/ionic4-i18n](https://github.com/enappd/ionic4-i18n)获得

多语言翻译，或国际化-本地化是每个想要瞄准国际客户的应用程序日益增长的需求。到目前为止，大多数应用程序都是用英文开发的，这不足为奇！在这篇博文中，我们将学习如何在 Ionic 4 应用中实现翻译，或者从技术上来说——国际化+本地化。

首先，我们需要了解流程中涉及的步骤。多亏了谷歌，翻译看起来非常容易，但在应用程序和网站的情况下，它们有点复杂，在我们的情况下是 Ionic 4 应用程序。在应用程序的翻译过程中有 3 个主要步骤—

1.  **翻译语言** —检测您想要翻译成的语言。这可以通过检测手机或浏览器的语言来自动完成，也可以通过用户操作来完成(比如使用下拉菜单)。对于我们的用例，我们将使用 [Cordova 全球化插件](https://github.com/apache/cordova-plugin-globalization)来检测设备的语言。
2.  **准备语言文本** —你需要有一个预翻译的字典(或 JSON 文件)，其中存储了你的应用程序中所有翻译语言文本的翻译。有几种方法可以做到这一点，我们将在下面的步骤中看到。对于较小的应用程序来说，这主要是一个手动过程，但你可以使用在线工具，如 [this](https://www.logisticinfotech.com/translate-language-files-online-json/) 进行快速翻译，或像[Poe editor](https://poeditor.com/)进行更标准化的工作流程。
3.  **翻译** —经过以上两步，你实际上是把你的 app 的文本翻译成了翻译语言文本。我们将使用 [ngx-translate](https://github.com/ngx-translate/core) 库来翻译我们的文本。

### 开发先决条件

以下是我的开发环境信息，以防你想匹配。其中有 **Ionic 4.x** 是必不可少的。 **Node v10.x** 也不错。

```
Ionic:
```

```
ionic (Ionic CLI)             : 4.11.0 
Ionic Framework               : @ionic/angular 4.1.1
@angular-devkit/build-angular : 0.13.5
@angular-devkit/schematics    : 7.2.4
@angular/cli                  : 7.3.5
@ionic/angular-toolkit        : 1.4.0
```

```
Cordova:
```

```
cordova (Cordova CLI) : 8.0.0
Cordova Platforms     : ios 4.5.5
```

```
System:
```

```
ios-deploy : 1.9.2
ios-sim    : 5.0.13
NodeJS     : v10.0.0 (/usr/local/bin/node)
npm        : 5.6.0
OS         : macOS Mojave
Xcode      : Xcode 10.1 Build version 10B61
```

此外，要在 iOS 设备上运行应用程序，您需要有正确版本的 Xcode。我现在的版本是 **Xcode 10.1** ，iOS 的时候是 v12.x，对于 android 设备，你需要安装正确的 android SDK 和库，以及一个设备(或者模拟器)。

### 概述

所以这个博客的发展纲要将是

1.  创建一个入门的 Ionic 4 tab 应用程序(我稍后会告诉你我为什么选择 tab 应用程序)
2.  准备多语言 JSON 文件
3.  实现 [ngx-translate](https://github.com/ngx-translate/core) 库进行检测和翻译
4.  实现一个基本的页面布局，在浏览器中显示翻译的文本
5.  实现 [Cordova 全球化插件](https://github.com/apache/cordova-plugin-globalization)来检测设备语言。
6.  在 iOS 模拟器上运行应用程序，通过手机的默认语言进行翻译

我们将翻译另外两种语言的文本——法语**和西班牙语**

听起来很简单，对吧？好吧，让我们开始吧。

那不…翻译得很好😛

### 步骤 1:创建 Ionic 4 应用程序

这一部分主要是为初学者准备的。一旦你在你的系统中安装了 Ionic 4，你就可以通过运行

```
ionic start yourAppName tabs
```

```
// Change to app folder
cd yourAppName
```

```
// Start the app in browser
ionic serve
```

这将在浏览器中给你一个漂亮的应用视图。很酷，对吧？

浏览器上的离子服务

还要熟悉文件夹结构。`src/app/tab1`和`src/app/tab2`是我们进一步开发的兴趣。这些分别是选项卡 1 和选项卡 2 的文件夹。

### 步骤 2:准备语言 JSON 文件

我们将在`src/assets/i18n`文件夹中创建这些 JSON 文件。即使在生产构建之后,`assets`文件夹仍然在根目录中，因此路径不会中断。我们将创建三个 JSON 文件`en.json`(英语)、`fr.json`(法语)和`es.json`(西班牙语)。

i18n 文件的文件夹结构

`**en.json**`

```
{
"TITLE": "Hello sir",
```

```
"TITLE_2": "Hello {{value}}",
```

```
"description": "Ooohh .... did you just translate this text ?",
```

```
"data": {"name": "My name is {{name_value}}"}
}
```

`**fr.json**`

```
{
"TITLE"         : "Bonjour Monsieur",
```

```
"TITLE_2"       : "Bonjour {{value}}",
```

```
"description"   : "Ooohh .... vous venez de traduire ce texte?",
```

```
"data"          :{"name": "je m'appelle {{name_value}}"}
```

```
}
```

`**es.json**`

```
{
```

```
"TITLE": "Hola señor",
```

```
"TITLE_2": "Hola {{value}}",
```

```
"description": "Ooohh .... acabas de traducir este texto?",
```

```
"data": {"name": "Me llamo {{name_value}}"}
```

```
}
```

``{{value}}`` 和``{{name_value}}`` 是我们可以从组件中传递的变量/常量。这可以用来在翻译中保持单词不变，或者给出库不支持的翻译。

### 步骤 3:实现 ngx-翻译库

ngx-translate 是 Angular 的国际化(i18n)库。由于 Ionic 4 在默认情况下有 Angular，我们可以将这个库用于 app 以及渐进式 web 应用程序。

#### 安装库

```
// Install core library
npm install --save @ngx-translate/core
```

```
// Install http loader
npm install @ngx-translate/http-loader --save
```

**http-loader** 用于通过 Angular 的 HttpClient 模块加载翻译文件(在我们的例子中是 JSONs)。

#### 设置库和 http-loader

我们需要定义一个函数，使用 http-loader 将外部 JSON 文件加载到应用程序中。这个函数看起来像这样

```
export function HttpLoaderFactory(http: HttpClient) {
return new TranslateHttpLoader(http, "./assets/i18n/", ".json");
}
```

其中我们已经为函数提供了 JSON 文件的外部路径。

我们需要在我们的根模块(app.module.ts)中添加**翻译**和 **http-loader** 模块。这是安装后文件的外观。

注意`TranslateModule.forRoot()`这是在选项卡式应用程序或一般非惰性加载模块的情况下使用的。然而，对于一个标签子节点，我们将不得不使用`TranslateModule.forChild()`。我们将在后面看到这将如何影响我们的功能。

#### 在子组件中设置翻译库

比方说，我们想在 **Tab1 中实现翻译。**如前所述，`src/app/tab1`文件夹包含了本页的所有文件。我们将使用以下命令导入`tab1.page.ts`文件中的`translationService`

```
import { TranslateService } from '@ngx-translate/core';
```

完成的`tab1.page.ts`文件将如下所示

让我们分解代码，以便更好地理解

*   页面加载时运行。然后，我们使用`this._translate.getBrowserLang()`(针对浏览器)获取默认的浏览器语言，如果没有浏览器，则设置默认语言
*   然后我们使用`this._translate.use(this.language)`告诉翻译服务要翻译成哪种语言(在我们的例子中是英语)
*   **重要提示:**使用`translateService`中的`this._translate.get('TITLE').subscribe()`函数异步获取翻译。这里，- `get()`是获取翻译的函数。- `TITLE`是在 JSON 文件中搜索的键。如果数据在一个嵌套的 JSON 中，那么我们使用点符号来获取，例如，`data.name` - `subscribe`用于异步获取(不需要使用超时)
*   `changeLanguage`是从用户动作中调用的。这个函数将使用前面的两个步骤来翻译成目标语言。

### 步骤 4:显示翻译文本的基本页面布局

我们将创建一个非常基本的布局，使语言选择和显示应用程序中的翻译文本。
语言选择可以通过一个简单的`ion-select`来完成，它的作用就像一个选择列表。我们将在`ion-card`布局中显示不同类型的翻译文本。最终布局将如下所示

文本翻译成所有三种语言。选择页面顶部的选项

布局文件`tab1.page.html`将具有以下布局

在您的`ionic serve`上运行所有这些更改，瞧！你得到了一些翻译🎉 🎉 🎉 🎉

浏览器上的文本翻译

#### 指令抓到你了😎

如果您完全按照上述步骤操作，您可能不会得到如上图所示的准确结果。

你会意识到**翻译在你曾经使用过的地方起作用**

```
this._translate.get('TITLE').subscribe((res: string) => {           
this.title = res;
});
this._translate.get('description').subscribe((res: string) => {           
this.description = res;
});
```

来获得翻译，并在 HTML 中使用本地变量显示出来，如下所示

```
<h1>{{ title }}</h1>
```

```
<p>{{ description }}</p>
```

但是，**翻译在你使用了如下`directive`的地方**就不起作用了

```
<h1 translate>TITLE</h1>
```

```
<p [translate]="'description'"></p>
```

这是因为在我们的 Ionic 4 标签应用程序中，标签页面是惰性加载的。在延迟加载的模块中，您还需要在子模块中导入翻译模块，这样一切才能正常工作。

让我们转到我们的`tab1.module.ts`文件，导入类似于根模块的翻译和 http-loader 模块。但是这一次，我们会用`TranslateModule.forChild`。完整的模块文件如下所示

现在，如果你再次运行该应用程序，`directive`翻译也将正常工作。😎 😎 😎

在标签应用程序中翻译—像老板一样

> 对于拥有大量代码的大型应用程序，最好使用此方法，因为此方法会产生较小的代码大小，并且不需要局部变量。

### 步骤 5:实现 Cordova 全球化插件

要使用设备的默认语言执行翻译，我们需要首先检测设备的默认语言。这可以使用全球化插件来完成。使用以下命令安装插件

```
ionic cordova plugin add cordova-plugin-globalization
```

```
npm install @ionic-native/globalization
```

使用以下命令在组件中导入插件

```
import { Globalization } from '@ionic-native/globalization/ngx';
```

```
constructor(private globalization: Globalization) { }
```

现在，您可以在页面 start 上调用一个简单的函数，它将检测设备的默认语言

```
getDeviceLanguage() {
this.globalization.getPreferredLanguage().then(res => {
// Run other functions after getting device default lang
this._initTranslate()
})
.catch(e => console.log(e));
}
```

### 步骤 6:在模拟器上运行应用程序

要在模拟器上运行应用程序，首先要添加 iOS 平台

```
ionic cordova platform add ios
```

然后为 iOS build 准备应用程序

```
ionic cordova prepare ios
```

之后，您可以直接使用 Xcode 构建应用程序，或者使用 Ionic CLI 在默认模拟器中运行应用程序。我更喜欢 Xcode，因为它可以更好地处理所有与供应相关的错误。要直接从 Ionic CLI 运行，请使用

```
ionic cordova run ios --target="iPhone-6s"
```

如果您在设备或模拟器上运行，这将是您在 Safari 控制台中看到的内容。我把模拟器上的默认语言改成了`french`

获取设备默认语言— iOS 模拟器

**有趣的事实:**既然 Ionic apps 是基于浏览器本身的，检测浏览器的默认语言就足够了。如上例所示，
-设备默认语言为`fr-US`
-浏览器默认语言为`fr`

因此，对于我们的情况，检测浏览器语言本身就足够了。但是，如果设备的默认语言不同于浏览器的语言，那么能够检测设备的默认语言是很好的。

### 结论

在这篇文章中，我们学习了如何检测设备/浏览器语言，创建多种语言文件，并使用不同的方法实现翻译。

使用 **ngx-translate** 库的唯一限制是，你需要事先在你的应用程序的每一页上定义你的语言文本。这些将以国家代码命名的 JSON 文件(即 en.json、jp.json、it.json 等)存储在 **src/assets/i18n/** 目录中。你不能使用这个库动态地生成语言翻译，所以如果你需要这种类型的功能，你需要使用谷歌翻译 API 或类似的东西。

*本博客最初发表于*[***Enappd***](https://enappd.com/blog/)*。*

* * *

👉**你可能喜欢的其他基于 Ionic 的文章:**

[如何将 Firebase 与 Ionic 4 配合使用——初学者完全指南](https://enappd.com/blog/firebase-with-ionic-4-overview/8/)

[如何在 Ionic 4 中使用地理定位、地理围栏和信标插件](https://enappd.com/blog/how-to-use-geolocation-geofencing-and-beacon-plugins-in-ionic-4/10/)

[Ionic 4 中的二维码、扫描和光学字符识别(OCR)](https://enappd.com/blog/qr-code-scanning-and-optical-character-recognition-ocr-in-ionic-4/9/)

[Ionic 4 中的支付网关解决方案——Paypal、Apple Pay、Stripe 和其他](https://enappd.com/blog/payment-gateway-solutions-in-ionic-4-paypal-apple-pay-stripe-and-others/6/)

* * *

**需要游离离子 4 号启动器？**

*   【Ionic 4 的免费聊天主题启动器
*   [游离离子 4 —燃烧基启动器](https://store.enappd.com/product/ionic4-firebase-starter/)

你也可以在我们的网站[enappd.com](https://store.enappd.com)找到免费的离子 4 启动器

* * *

🐦*follow enappd on*[**Twitter**](https://twitter.com/EnappdStore)**，*[**Facebook**](https://www.facebook.com/EnappdStore/)*，*[**LinkedIn**](https://in.linkedin.com/company/enappdstore)🕸*

 *#### 很高兴有你在这里，请点击👏按钮和共享👭这篇文章可以帮助别人找到它！欢迎在下方留言评论。*