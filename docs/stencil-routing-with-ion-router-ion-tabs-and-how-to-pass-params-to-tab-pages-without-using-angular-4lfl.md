# 模板:使用离子路由器、离子标签进行路由，以及如何将参数传递到标签页(不使用角度)

> 原文：<https://dev.to/cm/stencil-routing-with-ion-router-ion-tabs-and-how-to-pass-params-to-tab-pages-without-using-angular-4lfl>

在一个只有模板的项目中设置`<ion-router>`和`<ion-tabs>`(没有 Angular)可能会非常棘手。本文涵盖:

*   如何将`<ion-router>`与`<ion-tabs>`一起使用
*   如何用`<ion-tabs>`分配一个`root`路线，特别是如果没有标签有 URL `/`
*   如何在不使用 Angular 的情况下向标签页传递参数

## 先决条件:安装 ionic-pwa

从`ionic-pwa`开始模板:

```
npm init stencil
→ select ionic-pwa 
```

Enter fullscreen mode Exit fullscreen mode

## 如何用`ion-router`搭配`ion-tabs`

首先，这是包含所有特性的完整工作示例。当在选项卡之间切换时，当通过按钮调用路线时，以及当用不同的 URL 重新加载浏览器时，它都可以工作。稍后，我会告诉你什么不太管用。

修改`app-root.tsx` :

```
<ion-app>
  <ion-router useHash={false}>
    <ion-route-redirect from="/" to="/home" />
    <ion-route component="app-tabs">
      <ion-route url="/home" component="tab-home">
        <ion-route component="app-home" />
      </ion-route>
      <ion-route url="/profile" component="tab-profile">
        <ion-route url="/:name" component="app-profile" />
      </ion-route>
    </ion-route>
  </ion-router>
  <ion-nav />
</ion-app> 
```

Enter fullscreen mode Exit fullscreen mode

添加新文件`app-tabs.tsx` :

```
import { Component, h } from '@stencil/core';

@Component({
  tag: 'app-tabs'
})
export class AppTabs {
  render() {
    return [
      <ion-tabs>
        <ion-tab tab="tab-home">
          <ion-nav />
        </ion-tab>

        <ion-tab tab="tab-profile">
          <ion-nav />
        </ion-tab>

        <ion-tab-bar slot="bottom">
          <ion-tab-button tab="tab-home">
            <ion-icon name="home" />
            <ion-label>home</ion-label>
          </ion-tab-button>
          <ion-tab-button tab="tab-profile" href="/profile/notangular">
            <ion-icon name="person" />
            <ion-label>Profile</ion-label>
          </ion-tab-button>
        </ion-tab-bar>
      </ion-tabs>
    ];
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

该设置执行以下操作:

*   当你的应用在`localhost:3333`下启动时，它会通过`<ion-route-redirect>`重定向到`localhost:3333/home`
*   `name`作为 URL 参数传递给`app-profile`，并作为`Prop()`接收
*   点击该选项卡还会将一个参数传递给`app-profile`

## 什么不起作用

### `root`——属性在`ion-router`而不是`ion-route-redirect`

```
<ion-app>
  <ion-router useHash={false} root="/home">
    <ion-route component="app-tabs">
      <ion-route url="/home" component="tab-home">
        <ion-route component="app-home" />
      </ion-route>
      <ion-route url="/profile" component="tab-profile">
        <ion-route url="/:name" component="app-profile" />
      </ion-route>
    </ion-route>
  </ion-router>
  <ion-nav />
</ion-app> 
```

Enter fullscreen mode Exit fullscreen mode

这将导致以下错误:

```
[ion-router] URL is not part of the routing set 
```

Enter fullscreen mode Exit fullscreen mode

为什么？大概是因为`<ion-router>`只有一条直达子路由，没有`url`属性。设置路由器时，根是未知的，因为它嵌套在 tabs route 中的某个地方(注意:这是我的假设，我没有检查代码)。

### 全包子路由从不接收查询参数

```
<ion-app>
  <ion-router useHash={false}>
    <ion-route-redirect from="/" to="/home" />
    <ion-route component="app-tabs">
      <ion-route url="/home" component="tab-home">
        <ion-route component="app-home" />
      </ion-route>
      <ion-route url="/profile/:name" component="tab-profile">
        <ion-route component="app-profile" />
      </ion-route>
    </ion-route>
  </ion-router>
  <ion-nav />
</ion-app> 
```

Enter fullscreen mode Exit fullscreen mode

在这种情况下，`name`被传递给了`tab-profile`组件，但没有传递给`app-profile`。

### 两个子路由使参数可选

当使用`<ion-router>` :
时，我尝试了这种设置来使查询参数可选

```
<ion-app>
  <ion-router useHash={false}>
    <ion-route-redirect from="/" to="/home" />
    <ion-route component="app-tabs">
      <ion-route url="/home" component="tab-home">
        <ion-route component="app-home" />
      </ion-route>
      <ion-route url="/profile" component="tab-profile">
        <ion-route url="/" component="app-profile" />
        <ion-route url="/:name" component="app-profile" />
      </ion-route>
    </ion-route>
  </ion-router>
  <ion-nav />
</ion-app> 
```

Enter fullscreen mode Exit fullscreen mode

起初，这似乎行得通。你可以调用`/profile`和`/profile/ionic`，两者都能很好的渲染。然而，当我将一串`console.log`语句附加到`constructor()`、`componentDidLoad()`和`componentDidUnload()`时，它显示`app-profile`页面被创建了两次。虽然没有明显的视觉差异，但在某些时候也是卸载的。这种行为通常可以被描述为古怪。

我不知道如何使查询参数真正可选，但是您可以通过设置`ion-tab-button` :
的`href`属性来简单地传递一些通用值

```
<ion-tab-button tab="tab-profile" href="/profile/not-a-person"> 
```

Enter fullscreen mode Exit fullscreen mode

然后，在`app-profile`中，解析道具`name`，如果它等于`not-a-person`就做别的事情。

### 将`url`放在子路线中

如果像这样将`url`属性移动到子路由:

```
<ion-app>
  <ion-router useHash={false}>
    <ion-route component="app-tabs">
      <ion-route url="/" component="tab-home">
        <ion-route component="app-home" />
      </ion-route>
      <ion-route component="tab-profile">
        <ion-route url="/profile/:name" component="app-profile" />
      </ion-route>
    </ion-route>
  </ion-router>
  <ion-nav />
</ion-app> 
```

Enter fullscreen mode Exit fullscreen mode

你会再次期待古怪的行为。这种设置在某些情况下是可行的。如果你点击个人资料选项卡，它不会显示，但如果你通过按下`app-home`上的按钮打开`app-profile`页面，它似乎可以工作并通过道具。然后，如果您再次点击选项卡，它会显示页面，尽管浏览器 URL 没有改变，并且从组件生命周期的角度来看，页面实际上应该已经卸载了。所以，这真的行不通。

## 须知:关于`ion-router`的观察

### 不同的参数=不同的分量

考虑这条路线:

```
<ion-route url="/profile/:name" component="app-profile" /> 
```

Enter fullscreen mode Exit fullscreen mode

如果你通过一个按钮打开`/profile/user1`,然后通过另一个按钮打开`/profile/user2`(假设你使用标签或侧菜单来导航离开你刚刚打开的屏幕)，Stencil 将创建一个新的`app-profile`组件(并销毁旧的组件)。它不**而**更新`app-profile`的`Prop() name`。

### 带路线参数的散列不起作用

这种情况也是如此:

```
/profile/user1#a
/profile/user1#b 
```

Enter fullscreen mode Exit fullscreen mode

参数`name`的值为`user1#a`或`user1#b`。这与你可能从 web 应用程序中的传统服务器端路由所期待的不同。

### 同名(无参数)的标签被重用

如果你有一个带路线`/profile`的标签`app-profile`，无论你是从按钮还是标签打开`/profile`都没关系。将显示完全相同的组件，并且**不会**被重新创建。因此，即使您通过不同的选项卡离开该页面，它也会保持其状态。

### 航线用`componentProps`创建新组件

考虑这条路线，它经过`componentProps` :

```
<ion-route url="/profile" component="app-profile" componentProps={{ name: this.name }} /> 
```

Enter fullscreen mode Exit fullscreen mode

如果`this.name`发生变化，**的`app-profile`组件**将被销毁，并创建一个具有新名称的新组件。它**不**更新现有`app-profile`的道具。

## 有没有办法让一个*全局* `app-profile`组件在参数或道具改变时得到重用？

为什么会有人需要这个？当渲染一个页面的开销很大时，您需要这样做。假设您有一个映射库。渲染一个城市的地图需要几秒钟。出于性能原因，您希望保持`page-citymap`不变，但是您也希望在 URL 中反映当前选择的城市，如下所示:`/map/berlin`或`/map/nyc`。据我所见，这个和现在的`ion-router`是不行的。如果您导航到一个不同的 URL，`page-citymap`将被重新创建，并且昂贵的地图渲染将为新组件重新开始。

如果我错了，如果你找到了解决方法，请纠正我。

还有一点需要注意:看看 [Ionic Stencil 会议应用](https://github.com/ionic-team/ionic-stencil-conference-app)，它展示了如何在主从场景中使用路线参数。