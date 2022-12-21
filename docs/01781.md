# [更新] #XfEffects: Xamarin。用于更改 ImageButton 图像的淡色的窗体效果-(新系列)

> 原文：<https://dev.to/msicc/updated-xfeffects-xamarin-forms-effect-to-change-the-tintcolor-of-imagebutton-s-image-new-series-fl8>

文档建议当我们只想改变底层本地控件的属性时使用效果。我开始喜欢特效，因为它们让我的代码更易读。对于效果，我总是知道有一个特定于平台的实现，而这在使用自定义渲染器时并不明显。现在，我总是试图在渲染器之前实现一个效果。

* * *

更新:我更新了示例项目，也考虑到了`ImageButton`源代码的变化。我最近遇到了一种情况，通过我的 ViewModel 根据它的状态改变了`Source`(播放/暂停)，并意识到在这种情况下需要重新应用效果。请注意。

* * *

#### 基础知识

效果的工作方式与渲染器类似。您在`Xamarin.Forms`项目中实现定义，将它附加到需要更改的控件上。需要导出`PlatformEffect`实现以编译到应用程序中。与渲染器一样，平台实现也支持属性更改。在这个新系列 [#XfEffects](https://msicc.net/tag/XfEffects/) 中，我将向你展示一些对我有用的`Effects`。

#### 效果声明

让我们翻到本帖的`Effect`。我们会根据自己的需要改变`ImageButton`的`TintColor`。让我们从为我们的`Effect` :
创建类开始

```
 public class ImageButtonTintEffect : RoutingEffect
    {
        public ImageButtonTintEffect() : base($"XfEffects.{nameof(ImageButtonTintEffect)}")
        {
        }
    } 
```

所有的`Xamarin.Forms` `Effect`实现都需要从`RoutingEffect`类派生，并将`Effect`的名字传递给基类的构造函数。这几乎是我们为`Effect`类本身所要做的一切。

#### 用于传递参数的效果扩展

对我们来说，将我们想要的`TintColor`应用到平台实现的最简单的方法是附加的`BindableProperty`。为了能够附加`BindableProperty`，我们需要一个静态类来为附加的属性提供容器:

```
public static class ImageButtonTintEffectExtensions
{
    public static readonly BindableProperty TintColorProperty = BindableProperty.CreateAttached("TintColor", typeof(Color), typeof(ImageButtonTintEffectExtensions), default, propertyChanged: OnTintColorPropertyChanged);
    public static Color GetTintColor(BindableObject bindable)
    {
        return (Color)bindable.GetValue(TintColorProperty);
    }
    public static void SetTintColor(BindableObject bindable, Color value)
    {
        bindable.SetValue(TintColorProperty, value);
    }
    private static void OnTintColorPropertyChanged(BindableObject bindable, object oldValue, object newValue)
    {
    }
} 
```

当然，我们希望将`TintColor`属性设置为`Xamarin.Forms.Color`,因为这样可以很容易地从`Style`甚至是视图模型中控制颜色。

我们希望我们的效果只在我们改变默认的`TintColor`值时被调用。这确保了我们只运行我们的应用程序所必需的代码:

```
private static void OnTintColorPropertyChanged(BindableObject bindable, object oldValue, object newValue)
{
    if (bindable is ImageButton current)
    {
        if ((Color)newValue != default)
        {
            if (!current.Effects.Any(e => e is ImageButtonTintEffect))
                current.Effects.Add(Effect.Resolve(nameof(ImageButtonTintEffect)));
        }
        else
        {
            if (current.Effects.Any(e => e is ImageButtonTintEffect))
            {
                var existingEffect = current.Effects.FirstOrDefault(e => e is ImageButtonTintEffect);
                current.Effects.Remove(existingEffect);
            }
        }
    }
} 
```

最后但同样重要的是，在我们的`Xamarin.Forms`应用程序中，我们希望使用新的`Effect`。这很简单:

```
<!--import the namespace-->
xmlns:effects="clr-namespace:XfEffects.Effects"
<!--then use it like this-->
<ImageButton Margin="12,0,12,12"
    effects:ImageButtonTintEffectExtensions.TintColor="Red"
    BackgroundColor="Transparent"
    HeightRequest="48"
    HorizontalOptions="CenterAndExpand"
    Source="ic_refresh_white_24dp.png"
    WidthRequest="48">
    <ImageButton.Effects>
        <effects:ImageButtonTintEffect />
    </ImageButton.Effects>
</ImageButton> 
```

我们使用上面创建的附加属性将`TintColor`提供给`ImageButtonTintEffect`，我们需要将它添加到`ImageButton`的`Effects`列表中。

#### 安卓实现

我们来看看 Android 特有的实现。首先，让我们添加 platform 类并用适当的属性来修饰它，以导出我们的效果:

```
using Android.Content.Res;
using Android.Graphics;
using System;
using System.ComponentModel;
using Xamarin.Forms;
using Xamarin.Forms.Platform.Android;
using AWImageButton = Android.Support.V7.Widget.AppCompatImageButton;
[assembly: ResolutionGroupName("XfEffects")]
[assembly: ExportEffect(typeof(XfEffects.Droid.Effects.ImageButtonTintEffect), nameof(XfEffects.Effects.ImageButtonTintEffect))]
namespace XfEffects.Droid.Effects
{
    public class ImageButtonTintEffect : PlatformEffect
    {
        protected override void OnAttached()
        {

        }
        protected override void OnDetached()
        {
        }
        protected override void OnElementPropertyChanged(PropertyChangedEventArgs args)
        {
        }
    }
} 
```

记住:每个应用程序只需要定义一次`ResolutionGroupName`，并且不应该改变。类似于自定义的`Renderer`，我们还需要导出平台实现和`Forms`实现的定义，以使我们的`Effect`工作。

像按钮这样的 Android 控件有不同的状态。Android 控件上的属性，比如颜色，可以基于它们的`State`属性来设置。`Xamarin.Android`在`Android.Resource.Attribute` [类](https://docs.microsoft.com/en-us/dotnet/api/android.resource.attribute?f1url=https%3A%2F%2Fmsdn.microsoft.com%2Fquery%2Fdev16.query%3FappId%3DDev16IDEF1%26l%3DEN-US%26k%3Dk(Android.Resource.Attribute);k(TargetFrameworkMoniker-MonoAndroid,Version%3Dv9.0);k(DevLang-csharp)%26rd%3Dtrue&view=xamarin-android-sdk-9)中实现这些状态。我们这样定义我们的`ImageButton`状态:

```
static readonly int[][] _colorStates =
{
    new[] { global::Android.Resource.Attribute.StateEnabled },
    new[] { -global::Android.Resource.Attribute.StateEnabled }, //disabled state
    new[] { global::Android.Resource.Attribute.StatePressed } //pressed state
}; 
```

注意:某些状态，如“*禁用*”，只需在[操作系统状态列表](https://developer.android.com/reference/android/R%24attr)中定义的匹配状态前添加一个“-”即可创建(否定它)。我们需要这个声明来创建我们自己的`ColorStateList`，它将覆盖`ImageButton`图像的颜色。将这个方法添加到上面创建的类中:

```
private void UpdateTintColor()
{
    try
    {
        if (this.Control is AWImageButton imageButton)
        {
            var androidColor = XfEffects.Effects.ImageButtonTintEffectExtensions.GetTintColor(this.Element).ToAndroid();
            var disabledColor = androidColor;
            disabledColor.A = 0x1C; //140
            var pressedColor = androidColor;
            pressedColor.A = 0x24; //180
            imageButton.ImageTintList = new ColorStateList(_colorStates, new[] { androidColor.ToArgb(), disabledColor.ToArgb(), pressedColor.ToArgb() });
            imageButton.ImageTintMode = PorterDuff.Mode.SrcIn;
        }
    }
    catch (Exception ex)
    {
        System.Diagnostics.Debug.WriteLine(
            $"An error occurred when setting the {typeof(XfEffects.Effects.ImageButtonTintEffect)} effect: {ex.Message}\n{ex.StackTrace}");
    }
} 
```

这段代码在 Android SDK 23 之上工作，因为只有在那时才添加了修改已定义颜色的 A 通道的能力。Android 上的`Xamarin.Forms` `ImageButton`翻译成了`AppCompatImageButton`。`AppCompatImageButton`拥有`ImageTintList`属性。这个属性的类型是[colorstatelist](https://docs.microsoft.com/en-us/dotnet/api/android.content.res.colorstatelist.-ctor?f1url=https%3A%2F%2Fmsdn.microsoft.com%2Fquery%2Fdev16.query%3FappId%3DDev16IDEF1%26l%3DEN-US%26k%3Dk(Android.Content.Res.ColorStateList.%2523ctor);k(TargetFrameworkMoniker-MonoAndroid,Version%3Dv9.0);k(DevLang-csharp)%26rd%3Dtrue&view=xamarin-android-sdk-9)，它使用我们之前定义的状态以及这些状态的匹配颜色。

最后，我们需要设置构图模式。如果你想对此有更深的理解，一个很好的起点是[这个堆栈溢出问题](https://stackoverflow.com/questions/8280027/what-does-porterduff-mode-mean-in-android-graphics-what-does-it-do)。为了使事情不太复杂，我们将颜色注入图像中。最后一步是调用`OnAttached`覆盖和`OnElementPropertyChanged`覆盖中的方法。

基于我创建的示例的结果如下所示:

[![](img/9e457f01852536fe7db06e6154fbd5d7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--46Ioq9Ub--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i0.wp.com/msicc.net/wp-content/uploads/2019/09/tint-android.jpg%3Fw%3D1440%26ssl%3D1)

#### iOS 实现

当然，同样在 iOS 上，我们也要对类进行属性化，类似于 Android 版:

```
using System;
using System.ComponentModel;
using UIKit;
using Xamarin.Forms;
using Xamarin.Forms.Platform.iOS;
[assembly: ResolutionGroupName("XfEffects")]
[assembly: ExportEffect(typeof(XfEffects.iOS.Effects.ImageButtonTintEffect), nameof(XfEffects.Effects.ImageButtonTintEffect))]
namespace XfEffects.iOS.Effects
{
    public class ImageButtonTintEffect : PlatformEffect
    {
        protected override void OnAttached()
        {
        }
        protected override void OnDetached()
        {
        }
        protected override void OnElementPropertyChanged(PropertyChangedEventArgs args)
        {
        }
    }
} 
```

`Xamarin.Forms` `ImageButton`的底层控件是 iOS 上默认的`UIButton`。`UIButton`控件有一个`UIImageView`，可以用`SetImage`方法改变。基于这些知识，我们将实现`UpdateTintColor`方法:

```
private void UpdateTintColor()
{
    try
    {
        if (this.Control is UIButton imagebutton)
        {
            if (imagebutton.ImageView?.Image != null)
            {
                var templatedImg = imagebutton.CurrentImage.ImageWithRenderingMode(UIImageRenderingMode.AlwaysTemplate);
                //clear the image on the button
                imagebutton.SetImage(null, UIControlState.Normal);
                imagebutton.ImageView.TintColor = XfEffects.Effects.ImageButtonTintEffectExtensions.GetTintColor(this.Element).ToUIColor();
                imagebutton.TintColor = XfEffects.Effects.ImageButtonTintEffectExtensions.GetTintColor(this.Element).ToUIColor();
                imagebutton.SetImage(templatedImg, UIControlState.Normal);
            }
        }
    }
    catch (Exception ex)
    {
        System.Diagnostics.Debug.WriteLine($"An error occurred when setting the {typeof(ImageButtonTintEffect)} effect: {ex.Message}\n{ex.StackTrace}");
    }
} 
```

让我们回顾一下这些代码行。第一步是从`UIButton`的`UIImageView`中提取已经附加的图像作为模板图像。第二步是从按钮中清除图像，使用`SetImage`方法并传递`null`作为`UIImage`参数。我试图省去这一步，但如果你这样做，它就不起作用。

下一步改变`UIButton`的`UIImageView`以及按钮本身的`TintColor`。我只能在改变了两个`TintColor`属性后才能改变`TintColor`。

最后一步是再次使用`SetImage`方法，将第一步中提取的图像重新附加到`UIImageButton`的`UIImageView`中——但这一次，我们将提取的图像作为`UIImage`参数传递。

当然，这里我们也需要在`OnAttached`覆盖和`OnElementPropertyChanged`中调用这个方法。

结果应该类似于下图:

[![](img/517e519447410c974a95c2be7273ccc2.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--eAQpKlrS--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i0.wp.com/msicc.net/wp-content/uploads/2019/09/tint-ios.jpg%3Fw%3D1440%26ssl%3D1)

#### 结论

用 Xamarin 实现扩展功能非常容易。表单效果。该过程类似于创建自定义渲染器的过程。通过使用附加的属性，您可以微调该代码的用法，还可以将属性值传递给平台实现。

请确保跟随我将作为这个新系列的一部分发布的其他效果。我还创建了一个示例应用程序来演示这些效果([在 Github 上找到](https://github.com/MSiccDev/XfEffects))。一如既往，我希望这篇文章对你们中的一些人有所帮助。

##### 直到下一个帖子，大家编码快乐！