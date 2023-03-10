# 介绍 Xamarin。表单开关组件

> 原文：<https://dev.to/syncfusion/introducing-xamarin-forms-switch-component-3976>

## 简介

我们很高兴向您介绍 [Xamarin。形成](https://docs.microsoft.com/en-us/xamarin/xamarin-forms/)开关组件。该组件可从 Essential Studio for Xamarin 获得。表单 [2019 第二卷(测试版)](https://www.syncfusion.com/account/downloads)。如今，几乎所有的移动应用程序都包含一个用户界面，允许用户打开或关闭项目。我们 Syncfusion 了解对这一简单但重要的功能的要求，现在提供功能丰富的 Xamarin。窗体开关控件。

[Xamarin。Forms](https://docs.microsoft.com/en-us/xamarin/xamarin-forms/) 开关控制允许你打开和关闭一个项目，并提供一个可选的不确定状态。在这篇博文中，我们将总结 Xamarin 的主要特性。表单开关。

## 视觉类型

无论我们在哪里看，一个应用程序的视觉兼容性都是极致的。所以用 Xamarin。窗体开关，我们提供了三种内置的可视化类型。这些视觉类型将节省您的时间，根据设备定制开关控制或在所有三种设备(Android、iOS 和 UWP)上保持相同的外观。

**Material**—这个视觉类型将呈现 Xamarin。根据[材料指南](https://material.io/design/)形成开关控制。

Cupertino—这个视觉类型将呈现 Xamarin。基于 [iOS 指南](https://developer.apple.com/design/human-interface-guidelines/)形成开关控制。

**流畅**—这种视觉类型将呈现 Xamarin。根据 [UWP 指南](https://www.microsoft.com/design/fluent/)形成开关控制。

**默认**—默认视觉类型分别在 Android、iOS 和 UWP 设备上加载 Material、Cupertino 和 Fluent UI。

<figure>[![Xamarin.Forms Switch with three visual types](img/038a950c77b6bd27447b79127e5613f7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--BONN9B1K--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.syncfusion.com/blogs/wp-content/uploads/2019/06/SwitchWithVisualType.jpg) 

<figcaption>Xamarin。三种视觉类型的表单切换</figcaption>

</figure>

## 不定状态

我们的 Xamarin。表单开关与普通开关略有不同。除了通常的开和关状态，我们还提供了一种叫做不确定的状态。例如，在应用程序通知模块的下图中，用户可以打开或关闭已安装应用程序的通知。全局开关可以一次打开或关闭所有通知。不过，在这里，当一些应用程序通知打开，一些保持关闭时，不确定的状态会有所帮助。

<figure>[![Xamarin.Forms Switch with indeterminate state](img/2b8419504b2018249a2c6034b580987c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--PpbElIdg--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://blog.syncfusion.com/blogs/wp-content/uploads/2019/06/tristate.gif) 

<figcaption>Xamarin。具有不确定状态的表单开关</figcaption>

</figure>

## 采用可视化状态管理器

使用[可视状态管理器](https://docs.microsoft.com/en-us/xamarin/xamarin-forms/user-interface/visual-state-manager)定制 Xamarin。基于开关状态形成开关的缩略图和轨迹。它支持的可视状态有:开、关、不确定和禁用。

```
<syncfusion:SfSwitch VisualType="Custom">
  <VisualStateManager.VisualStateGroups>
    <VisualStateGroup x:Name="States">
      <VisualState x:Name="On">
        <VisualState.Setters>
          <Setter Property="SwitchSettings">
           <Setter.Value>
             <syncfusion:MaterialSwitchSettings x:TypeArguments="syncfusion:OnState"
                                            ThumbColor="Green"/>
           </Setter.Value>
         </Setter>
        </VisualState.Setters>
      </VisualState>
      <VisualState x:Name="Indeterminate">
        <VisualState.Setters>
          <Setter Property="SwitchSettings">
            <Setter.Value>
              <syncfusion:CupertinoSwitchSettings x:TypeArguments="syncfusion:IndeterminateState"
                                              ThumbColor="Orange"/>
            </Setter.Value>
          </Setter>
        </VisualState.Setters>
      </VisualState>
      <VisualState x:Name="Off">
        <VisualState.Setters>
          <Setter Property="SwitchSettings">
            <Setter.Value>
              <syncfusion:CupertinoSwitchSettings x:TypeArguments="syncfusion:OffState"
                                              ThumbColor="Red"/>
            </Setter.Value>
          </Setter>
        </VisualState.Setters>
      </VisualState>
      <VisualState x:Name="Diabled">
        <VisualState.Setters>
          <Setter Property="SwitchSettings">
            <Setter.Value>
              <syncfusion:CupertinoSwitchSettings x:TypeArguments="syncfusion:DisabledState"
                                              ThumbColor="Gray"/>
            </Setter.Value>
          </Setter>
        </VisualState.Setters>
      </VisualState>
    </VisualStateGroup>
  </VisualStateManager.VisualStateGroups>
</syncfusion:SfSwitch> 
```

## 用繁忙指示器确认

支持繁忙指示的交换机是基于服务的应用程序中最常见的场景。我们的开关可以根据验证打开或关闭项目。这里验证可以用一个繁忙指示器异步处理。

<figure>[![Xamarin.Forms Switch with busy indicator](img/c348d063ca7a5aae24cf75af923da1dc.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--i-o2FJQL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://blog.syncfusion.com/blogs/wp-content/uploads/2019/06/busy-indicator.gif) 

<figcaption>Xamarin。带繁忙指示器的表单开关</figcaption>

</figure>

## 垂直方向

Xamarin。窗体开关支持垂直方向。当您的应用程序需要设置高、低和中值时，您可以集成一个垂直开关。

<figure>[![Xamarin.Forms Switch with horizontal and vertical orientations](img/88ba1daa9dfeac8ffe014b89b2460c98.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--_9xDqfeQ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.syncfusion.com/blogs/wp-content/uploads/2019/06/SwitchWithOrientation-300x165.jpg) 

<figcaption>具有水平和垂直方向的开关</figcaption>

</figure>

## 设置渐变

高度可定制的开关控制允许你用渐变定制它的背景。 [Xamarin。窗体渐变视图](https://help.syncfusion.com/xamarin/sfgradientview/overview)与控件集成在一起。您可以对控件应用线性渐变或径向渐变外观。

<figure>[![Xamarin.Forms Switch with gradient colors](img/444c40ced9bd6bcc0061eb54cefd34db.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--RGie4oAO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.syncfusion.com/blogs/wp-content/uploads/2019/06/SwitchWithGradientColor-300x150.png) 

<figcaption>Xamarin。带渐变颜色的表格开关</figcaption>

</figure>

## 图片为自定义外观

使用图像自定义开关，以说明开关操作的使用案例。

<figure>[![Xamarin.Forms Switch with images](img/8135d4d83acf76be59c9f4ad34a88e15.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--mCekpIfM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://blog.syncfusion.com/blogs/wp-content/uploads/2019/06/image.gif) 

<figcaption>Xamarin。带图像的表格切换</figcaption>

</figure>

## 无缝 RTL 支持

开关的设计在各个方面都考虑到了[流向](https://docs.microsoft.com/en-us/dotnet/api/xamarin.forms.flowdirection?view=xamarin-forms)T2 方向。因此，您可以无缝集成 RTL 布局。

<figure>[![Xamarin.Forms Switch in RTL layout](img/8f6d7996f0b327ffb5f1121f8c6f07ee.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--rVXw9gK0--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.syncfusion.com/blogs/wp-content/uploads/2019/06/SwitchWithRTL-1024x503.jpg) 

<figcaption>Xamarin。RTL 布局中的表单切换</figcaption>

</figure>

## 结论

在这篇博客文章中，我们已经走过了 [Syncfusion Xamarin。表格](https://www.syncfusion.com/xamarin-ui-controls/)开关控制。你可以从这个 [GitHub 地点](https://github.com/SyncfusionExamples/xamarin-forms-switch-control)查看这个项目，获得开关控制的亲身体验。您可以随时[下载我们的免费评估](https://www.syncfusion.com/account/downloads)来查看所有这些控件的运行情况。探索我们在 [Google Play](https://play.google.com/store/apps/details?id=com.syncfusion.samplebrowser) 和[微软商店](https://www.microsoft.com/en-us/p/syncfusion-essential-studio-for-xamarin/9nn069tldzf4)上提供的样品。在我们的文档中了解控件的高级特性。

如果您对这些控制有任何疑问或需要澄清，请在下面的评论中告诉我们。您也可以通过我们的[支持论坛](https://www.syncfusion.com/forums)、[直接跟踪](https://www.syncfusion.com/support/directtrac/)或[反馈门户](https://www.syncfusion.com/feedback/xamarin-forms)联系我们。我们很乐意为您提供帮助！

介绍 Xamarin 的帖子。表单切换组件最早出现在 [Syncfusion 博客](https://blog.syncfusion.com)上。