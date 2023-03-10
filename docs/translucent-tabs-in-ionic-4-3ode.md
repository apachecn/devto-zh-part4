# Ionic 4 半透明标签

> 原文：<https://dev.to/teamhive/translucent-tabs-in-ionic-4-3ode>

# 什么是半透明标签页？

半透明选项卡是对原生选项卡布局的视觉增强，允许视图的内容以模糊的效果渗透到选项卡栏中。当您的应用程序具有丰富的视觉效果和颜色，可以通过标签栏直观地描绘出来时，这是最好的利用方式。

## 离子芯支架

如果你好奇为什么 Ionic 不支持开箱即用，答案是他们有点支持。选项卡栏上的半透明标志将选项卡栏的样式更新为半透明。不幸的是，选项卡的设计方式使得选项卡栏成为内容的兄弟；这阻止了内容在标签栏下自然滚动。

以下是一些有待解决的问题，以跟踪 Ionic 何时正式支持半透明标签:

*   [https://github.com/ionic-team/ionic/issues/17676](https://github.com/ionic-team/ionic/issues/17676)
*   [https://github.com/ionic-team/ionic/issues/17201](https://github.com/ionic-team/ionic/issues/17201)

## 变通办法

让我们从为应用程序添加半透明效果的解决方法开始。请注意，这种方法需要对使用底部固定定位(即 ion-fab)的页面进行一些额外的样式配置。

#### 半透明效果

将`ion-tab-bar`上的`translucent`属性绑定添加为`true`，以启用 Ionic 核心团队设置的样式预设。

```
<ion-tabs>
    <ion-tab-bar [translucent]="true" slot="bottom">
       <!--- Your Tabs --->
    </ion-tab-bar>
</ion-tabs> 
```

Enter fullscreen mode Exit fullscreen mode

这将允许内容的颜色穿过标签栏。不幸的是，内容不会在选项卡下滚动，所以接下来让我们调整选项卡栏的位置。

#### 绝对定位

在包含`ion-tabs`组件的组件中，应用以下样式。

```
ion-tab-bar {
    // Translucent positioning effect
    position: absolute;
    bottom: 0;
    left: 0;
    width: 100%;
} 
```

Enter fullscreen mode Exit fullscreen mode

这将使标签栏“弹出”视图，并始终停留在应用程序容器的底部。太好了！现在，内容将以半透明效果显示在选项卡栏下。您将面临的下一个问题是，因为您已经从视窗尺寸中移除了选项卡栏，所以您的内容的底部将总是隐藏在选项卡栏下，并阻止用户与它们进行交互。

#### 偏移内容容器

现在我保证有更好的方法来处理这种操作，但是我需要一种快速的方法来穿透加载缓慢的路由器出口并修改页面的内容，而不需要手动将一个类应用到每个页面或者将相同的样式覆盖应用到每个页面。

在包含`ion-tabs`的组件中，实现`AfterViewInit`生命周期挂钩并应用以下标记。

```
@ViewChild(IonTabs, { static: true }) ionTabs: IonTabs;

ngAfterViewInit() {
    this.overrideTabContainer();
}

private overrideTabContainer() {
    setTimeout(() => {
        const routerOutlet = (this.ionTabs.outlet as any).nativeEl as HTMLElement;
        const container = routerOutlet.querySelector('ion-content');
        if (container) {
             container.style.setProperty('--padding-bottom', '90px');
        }
    });
} 
```

Enter fullscreen mode Exit fullscreen mode

这段代码将在惰性加载的标签中找到`ion-content`,并将填充的底部覆盖为大于标签栏高度的尺寸。您可以在这里手动拉动标签栏的`clientHeight`,而不是提供一个静态值。

#### 明白了

由于`ion-tab-bar`现在位于视图的底部，通常位置固定的元素将显示在标签栏上和/或标签栏下。这方面的一个例子是`ion-fab`和`ion-footer`。为了解决这个问题，您需要创建一个全局样式，将我们用于填充底部的相同偏移量应用于元素的定位。

即:

```
// Used for the translucent tabs
ion-fab, ion-footer {
    bottom: 90px;
} 
```

Enter fullscreen mode Exit fullscreen mode

## 结果

[![Translucent Tabs Image](img/51a7e42ca63b71202ec9c69451ec1f41.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--haj4RH5N--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://i.gyazo.com/89b8cdf826349b45483d008673c62675.gif)