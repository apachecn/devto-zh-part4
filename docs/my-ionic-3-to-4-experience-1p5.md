# 我的离子 3 到 4 经验

> 原文：<https://dev.to/walkingriver/my-ionic-3-to-4-experience-1p5>

我有一款中庸的手机游戏，叫 *Bravo！*它是使用 Ionic Framework 版本 3 和 Apache Cordova 用 TypeScript 编写的。目前仅[可用于 iPhone](https://bit.ly/ios-bravo) 。几周前，我开始收到来自谷歌的电子邮件，警告我不符合他们的广告政策，最终决定从 Play Store 中删除它。事实证明，这是一个简单的配置问题，但为了恢复，我必须提交一个新的 APK。我决定借此机会将 app 转换成带电容的 Ionic 4。这篇文章描述了我在这个过程中的经历

# 新建项目

据我所知，最简单的方法是创建一个全新的 Ionic 项目，这是我用`ionic start`做的。我确保告诉它不要添加科尔多瓦。

从那以后，用 Ionic CLI 重新创建每个页面就很简单了。使用最新的 CLI 版本，您可以使用`ionic`或`ng`命令(是的，我也在使用 Angular)。

```
ng g page game
ng g page instructions
ng g page game-over
ng g page new-game 
```

Enter fullscreen mode Exit fullscreen mode

Ionic CLI 提供了 Angular CLI 的 Ionic 专用示意图。这允许我创建 Ionic 页面，而不是 Angular 组件，它遵循 Ionic 关于页面初始化的建议。我最初的游戏只有两页，但是我决定在这里增加一些额外的内容:游戏结束和新游戏。默认情况下，CLI 设置路由和延迟加载。这是自动为我提供的路线。

```
const routes: Routes = [
  {
    path: '',
    redirectTo: 'home',
    pathMatch: 'full'
  },
  {
    path: 'home',
    loadChildren: './home/home.module#HomePageModule',
    canActivate: [InstructionGuard],
  },
  {
    path: 'game',
    canActivate: [InstructionGuard, InProgressGuard],
    loadChildren: './game/game.module#GamePageModule'
  },
  {
    path: 'instructions',
    loadChildren: './instructions/instructions.module#InstructionsPageModule'
  },
  { path: 'game-over', loadChildren: './game-over/game-over.module#GameOverPageModule' },
  { path: 'new-game', loadChildren: './new-game/new-game.module#NewGamePageModule', canActivate: [NewGameGuard] },
]; 
```

Enter fullscreen mode Exit fullscreen mode

成员是我决定作为一个新特性添加的，稍后我会详细介绍。其他一切都是由离子脚手架自动提供的。

## 离子 3 vs 4 标记

我想做的下一件事是调整我的游戏页面的 HTML 标记。这是我需要做出最大改变的地方，但我相信你会同意这些改变并不是那么剧烈。

我的游戏显示了一系列随机的卡片，一次一张，带有一个单词或主题，以及一条规则。规则是这样的，“唱一首包含这个词的歌....狗...歌词里。”在 v3 版本中，卡片标记如下所示。

```
 <ion-card (swipe)="onSwipe($event)">
    <ion-card-header [ngClass]="card.class+'-bg'">
      <h1>{{card.title}}</h1>
    </ion-card-header>
    <ion-card-content>
      <div class="item-text-wrap item text-center">
        <h2>&nbsp;</h2>
        <h1>{{rule}}</h1>
        <h2>&nbsp;</h2>
        <h1>{{card.text}}</h1>
      </div>
    </ion-card-content>
    <ion-row>
      <ion-col col-4></ion-col>
      <ion-col col-4></ion-col>
      <ion-col col-4>
        <button ion-button block clear (click)="getNextCard()">Draw Card</button>
      </ion-col>
    </ion-row>
  </ion-card> 
```

Enter fullscreen mode Exit fullscreen mode

这是新的 v4 版本。

```
<ion-card>
  <div [ngClass]="card.class" class="bg"></div>
  <ion-card-header>
    <ion-card-subtitle>
      {{card.title}}
    </ion-card-subtitle>
    <ion-card-title>
      {{card.text}}
    </ion-card-title>
  </ion-card-header>
  <ion-card-content>
    <div class="item-text-wrap item text-center">
      <p>{{card.rule}} "{{card.text}}"</p>
    </div>
  </ion-card-content>
</ion-card> 
```

Enter fullscreen mode Exit fullscreen mode

我趁机做了一些风格上的改变，但除此之外卡片格式都差不多。游戏页面上最大的不同是记分的标记，它由 6 个不同颜色的按钮组成，每个按钮显示特定颜色的分数。

这是分数的 v3 标记。

```
<ion-footer>
  <ion-toolbar color="dark">
    <ion-row>
      <ion-col col-2>
        <button ion-button block clear class="blue-bg score" (click)="addScore(0)">{{score[0]}}</button>
      </ion-col>
      <ion-col col-2>
        <button ion-button block clear class="red-bg score" (click)="addScore(1)">{{score[1]}}</button>
      </ion-col>
      <ion-col col-2>
        <button ion-button block clear class="green-bg score" (click)="addScore(2)">{{score[2]}}</button>
      </ion-col>
      <ion-col col-2>
        <button ion-button block clear class="yellow-bg score" (click)="addScore(3)">{{score[3]}}</button>
      </ion-col>
      <ion-col col-2>
        <button ion-button block clear class="pink-bg score" (click)="addScore(4)">{{score[4]}}</button>
      </ion-col>
      <ion-col col-2>
        <button ion-button block clear class="brown-bg score" (click)="addScore(5)">{{score[5]}}</button>
      </ion-col>
    </ion-row>
  </ion-toolbar>
</ion-footer> 
```

Enter fullscreen mode Exit fullscreen mode

这是调整后的 v4 标记。

```
<ion-footer>
  <ion-toolbar color="dark">
    <ion-grid>
      <ion-row>
        <ion-col size="2">
          <ion-button expand="block" fill="clear" class="blue-bg score" (click)="addScore(0)">{{score[0]}}</ion-button>
        </ion-col>
        <ion-col size="2">
          <ion-button expand="block" fill="clear" class="red-bg score" (click)="addScore(1)">{{score[1]}}</ion-button>
        </ion-col>
        <ion-col size="2">
          <ion-button expand="block" fill="clear" class="green-bg score" (click)="addScore(2)">{{score[2]}}</ion-button>
        </ion-col>
        <ion-col size="2">
          <ion-button expand="block" fill="clear" class="yellow-bg score" (click)="addScore(3)">{{score[3]}}
          </ion-button>
        </ion-col>
        <ion-col size="2">
          <ion-button expand="block" fill="clear" class="pink-bg score" (click)="addScore(4)">{{score[4]}}</ion-button>
        </ion-col>
        <ion-col size="2">
          <ion-button expand="block" fill="clear" class="brown-bg score" (click)="addScore(5)">{{score[5]}}</ion-button>
        </ion-col>
      </ion-row>
    </ion-grid>
  </ion-toolbar>
</ion-footer> 
```

Enter fullscreen mode Exit fullscreen mode

这里的主要变化是按钮的标记。Ionic 在 v3 和 v4 之间做了一个突破性的改变。最初的按钮是具有`ion-button`、`block`和`clear`属性的`<button>`元素。新的 v4 语法用一个`<ion-button>`元素代替了它，具有更标准的 HTML 属性`expand="block"`和`fill="clear"`。

新语法不仅更加 HTML 标准，以这种方式使用属性使得开发人员将这些值绑定到组件类上的变量变得简单得多。

# 组件代码

坦率地说，这里没有什么可报告的。组件代码在升级过程中没有改变。我确实做了一些游戏性的改变，但这些都是离子 4 附带的。

# 页面模块

在 Ionic 4 中，每个页面都有自己的模块，这有助于简化延迟加载。我的 v3 版游戏除了 app.module.ts 文件之外没有任何模块。

# 科尔多瓦 vs 电容

这是发生最大变化的地方。该游戏不使用任何设备特定的功能。Cordova 主要是在设备上加载和运行它的工具。

事实上，它作为一个渐进式网络应用程序(PWA)运行得很好。当它完成的时候，我正在认真考虑发行一个 PWA 版本作为主要的发行方式。使用 Capacitor 创建 iOS 和 Android 版本主要是一种练习。

### 添加电容器

到目前为止，我还没有做任何与电容器。当然，我看过，读过，等等。这是我第一次尝试在实际项目中使用它。我首先确保我拥有所有必需的依赖项，[，如这里所解释的](https://capacitor.ionicframework.com/docs/getting-started/dependencies)。

这意味着安装了:

*   可可普斯
*   Xcode 命令行工具
*   安卓工作室
*   JDK 8

接下来，我用
将电容器添加到我现有的 Ionic 项目中

```
ionic integrations enable capacitor 
```

Enter fullscreen mode Exit fullscreen mode

Ionic 文档在这一点上与上述命令末尾显示的说明不同。医生说运行

```
npx cap init [appName] [appId] 
```

Enter fullscreen mode Exit fullscreen mode

但是，命令似乎已经完成了。我还是运行了它，它更新了`capacitor.config.json`中的值。所以我想经营它不会有什么坏处。

### 构建 App

接下来，文档警告说，在添加任何平台之前，您需要构建一次应用程序。

```
ionic build 
```

Enter fullscreen mode Exit fullscreen mode

这建立了我的`www`文件夹，将被电容使用。

### 创建 iOS 应用

我的主要开发机器是一台 Mac Mini，因此开发 iOS 应用程序似乎是自然而然的下一步。Capacitor 是作为 Ionic 项目的开发依赖项安装的，这意味着它不是全球可用的。要执行任何电容器命令，必须以`npx`为前缀。

```
npx cap add ios 
```

Enter fullscreen mode Exit fullscreen mode

正如它警告的那样，这一步花了几分钟:准确地说是 424 秒。它创建了一个 Xcode 项目，并通过 CocoaPods 安装了一堆依赖项。它还为我提供了几十张图片，用作图标和闪屏。我已经为 Bravo 准备了一堆，所以它们需要被替换。

在 Xcode 中打开 iOS 项目是一个简单的命令。

```
npx cap open ios 
```

Enter fullscreen mode Exit fullscreen mode

> 起初并不顺利。Xcode 没有我的开发者账户；显然，自从买了新的苹果电脑后，我就没用过它了。为了下载我的开发者证书，我首先必须在线登录我的帐户并接受最新的开发者协议。这样一来，我就可以继续。

Xcode 可以正确识别所有内容。在我的 iPhone 7 上构建和部署应用程序只需点击一下鼠标。应用程序启动了，我可以立即开始玩游戏。

总而言之，这是一次非常顺利的经历。

### 创建安卓应用

接下来是安卓应用。我有一个三星 Galaxy S7 坐在这里收集灰尘，所以这是我的目标。

类似于 iOS 的 app，添加 Android 作为电容目标是一个单一的命令。

```
npx cap add android 
```

Enter fullscreen mode Exit fullscreen mode

这个命令完成的速度比 iOS 版本快了许多数量级。添加 Android 只花了 13 秒。

类似地，在 Android Studio 中打开项目是另一个简单的命令。

```
npx cap open android 
```

Enter fullscreen mode Exit fullscreen mode

Android Studio 毫无怨言地打开了项目。选择`Run | Run 'app'`菜单，它识别出我连接的 Galaxy S7。在那里，我选择了设备并点击了`Run`按钮。几秒钟后，Bravo 出现在手机上。

# 结论

坦白地说，这比我想象的要顺利。在 Android 上运行比在 iPhone 上稍微容易一点，但两种体验都不痛苦。鉴于我以前没有这样做过，并且一次对一个项目做了多个变更，我对整体的体验很满意。

我可以诚实地向任何有 Ionic v3 应用程序并考虑升级的人推荐这个。当然，我的游戏很简单，只有几页，所以如果你的程序比较大，你可能不会像我在这里一样在一个下午就完成。

# 下一步

还有几件事留给我去做。

## 图标和闪屏

正如我上面提到的，添加电容器为我提供了一个不同大小的图像文件夹，用于图标和闪屏。我需要确保我现有的布拉沃！图标和闪屏可以在进入下一步之前放入。

## 公元变化

我的 v3 代码使用了一个 Cordova 插件来显示 AdMob 广告。我用我的新 v4 代码删除了它，我还没有决定如何或者是否要在这个应用程序中显示它们。鉴于我最近使用免费广告支持应用的经验，我可能会让它成为一个付费应用，让事情顺其自然。

## 部署

当然，我仍然需要签署和部署应用程序到各自的商店。根据这个过程的复杂性，我可能会在事后再写一篇文章。在这一点上，我为自己在短短几个小时内取得的进步感到高兴。

# 参考文献

*   [离子框架](https://ionicframework.com)
*   [电容器](https://capacitor.ionicframework.com/)
*   [行走的布拉沃河！](https://bit.ly/ios-bravo)

# 反馈赞赏

你有什么意见或问题吗？我在这篇文章中犯了什么错误吗？在推特上告诉我。我是 [@walkingriver](https://twitter.com/walkingriver) 。