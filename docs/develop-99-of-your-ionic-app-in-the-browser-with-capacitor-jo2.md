# 在浏览器中开发 99%的 Ionic 应用程序

> 原文：<https://dev.to/ionic/develop-99-of-your-ionic-app-in-the-browser-with-capacitor-jo2>

Ionic 和 Capacitor 的一个非常特别的地方是，大量的应用程序开发都可以在桌面上的浏览器中进行。这意味着完全访问您的传统桌面 web 开发工具(Chrome/Safari/Firefox 开发工具)和开发速度，而无需重新编译或部署到模拟器或设备。

Ionic 的新原生 web 应用程序容器项目 [Capacitor](http://capacitor.ionicframework.com/) 的指导设计目标之一是，在不得不摆弄模拟器或设备之前，增加您在桌面上开发应用程序的时间，该项目可以在 iOS、Android、electronic 和 web 上原生运行您的 web 应用程序，作为一个渐进的 Web 应用程序。

最重要的是，构建 web-first 意味着你的应用程序可以像一个渐进式 web 应用程序一样运行良好，只需要最少的额外工作，假设你能够通过 Web API 实现你的应用程序所需的功能。

让我们来看看它是如何工作的。

## 一个假的原生插件

有可能，你的 Ionic/Capacitor 应用会有一些它需要的原生功能，这些功能超出了 Capacitor 现成可用的功能(比如与第三方 SDK 集成)。

假设我们有这个简单的电容插件:

```
import Foundation
import Capacitor

@objc(ThirdPartyPlugin)
public class ThirdPartyPlugin: CAPPlugin {
  @objc func connect(_ call: CAPPluginCall) {
    let service = ThirdPartySDK(apiKey)
    service.connect()
    call.resolve(service.getUser())
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

这个插件连接到 ThirdPartySDK，然后返回一些用户信息。不幸的是，这个理论上的 SDK 只在 iOS 和 Android 上可用，所以当我们在浏览器中构建应用程序时，我们需要添加自定义代码来检测我们正在哪个平台上运行(不理想)，或者模拟服务，以便我们可以快速构建(更好！)

## 嘲讽网络上的插件

为了模拟这个插件，我们需要为电容器创建一个网络插件。网络插件有很大的力量，但不仅仅是为了嘲笑！例如，Capacitor 为 web 提供了完整的[文件系统实现](https://capacitor.ionicframework.com/docs/apis/filesystem)，它使用 IndexedDB，因此您可以像在 iOS、Android 和 Electron 上一样处理文件。

在这种情况下，我们的 web 插件将非常简单，只返回一些假数据，所以让我们创建它:

```
import { WebPlugin } from '@capacitor/core';

export class ThirdPartyPluginWeb extends WebPlugin {
  constructor() {
    // Register the plugin with Capacitor so it is aware of it
    super({
      name: 'ThirdPartyPlugin',
      platforms: ['web']
    });
  }

  async connect(options: any) {
    return {
      id: 123,
      name: 'Barnaby Jones',
      email: 'barnaby@aol.com'
    }
  }
}

export const ThirdPartyPlugin = new ThirdPartyPluginWeb(); 
```

Enter fullscreen mode Exit fullscreen mode

接下来，我们需要用电容器注册这个插件，这样它就知道了。这使得你的插件在运行时可用，但是只在运行在构造函数中指定的平台上时可用(在本例中是`web` ):

```
import { ThirdPartyPlugin } from './plugins';
import { registerWebPlugin } from '@capacitor/core';
registerWebPlugin(ThirdPartyPlugin); 
```

Enter fullscreen mode Exit fullscreen mode

将这些代码放在哪里很重要，以确保它在这个插件被访问之前加载，所以我们建议将它放在您的根索引 JS/TS 文件中。

## 测试插件

一旦你建立了你的插件，使用它就像正常服务你的应用程序一样简单(例如`npm start`，然后像这样调用插件:

```
import { Plugins } from '@capacitor/core';

function connectToThirdParty() {
  const { ThirdPartyPlugin } = Plugins;

  const user = await ThirdPartyPlugin.connect();
} 
```

Enter fullscreen mode Exit fullscreen mode

当在 web 上运行时，您应该看到您的模拟 web 数据返回，否则在本机运行时，会看到正常的到第三方 SDK 的完全连接！

如果你有困难，试着在`Plugins`上靠近使用它的地方访问你的插件。不幸的是，对于现代的捆绑器和模块加载器，你的代码可能会在 web 插件加载之前引用`Plugins.ThirdPartyPlugin`,所以要注意这一点。

## 结论

Web 插件非常适合构建一致的跨平台 API(例如，`Geolocation`和`Share`API 在所有平台上都有相同的代码)，但是它们也可以用来模仿只在本地平台上使用的功能。

如果你最终为 Capacitor 构建了一个网络插件，并且觉得这个指南很有帮助，请在下面告诉我们！