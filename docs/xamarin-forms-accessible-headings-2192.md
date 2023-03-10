# Xamarin 形成可访问的标题

> 原文：<https://dev.to/stuartblang/xamarin-forms-accessible-headings-2192>

如果你正在开发一个移动应用程序，在 iOS 上启用 VoiceOver 并从依赖这些工具访问你的应用程序的人的角度考虑可用性是值得的。

开箱即用，Xamarin Forms 附带了一些很好的辅助功能，可以控制 VoiceOver 的体验，特别是[自动化属性](https://docs.microsoft.com/en-us/xamarin/xamarin-forms/app-fundamentals/accessibility/automation-properties)允许您用元数据注释您的 XAML 元素，例如当 VoiceOver 选择时要读出什么文本。

在这篇文章中，我不会把重点放在这些功能上，而是看一下标题。

### 标题

考虑下面的屏幕:

[![](img/52d480b2b6988f66bcdab7051dd732b1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--wHamBRwg--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://stu.dev/conteimg/2019/08/image-5.png)

从视觉上看，这些标题向用户传达了屏幕的结构。但是，现在让我们尝试只使用 VoiceOver 来导航页面。在此动画中，当我们向左/向右滑动时，会选择上一个/下一个元素，并大声朗读其描述。

[![](img/41eadef5e40c8c11fc528c12db986451.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ny0-2p3O--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://stu.dev/conteimg/2019/09/before.gif)

虽然所有的内容都可以访问，但如果 VoiceOver 是您唯一的用户界面，标题就会淡出常规内容。

[![](img/1ffff5d774f6e4d3a87b54d32e553a0a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--x91U9XW2--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://stu.dev/conteimg/2019/08/image-9.png) 

<figcaption>转子控制</figcaption>

让我们尝试使用转子导航控制来选择`Headings`(用双指扭转手势)。现在，我们可以通过上下滑动手势在标题间跳转来浏览屏幕，但目前 VoiceOver 只是说:“没有找到标题”，这不是很有帮助。

在 iOS 上，元素可以被赋予一个`header` [可访问性特征](https://developer.apple.com/documentation/uikit/accessibility/uiaccessibility/accessibility_traits)。使用此功能，VoiceOver 会将这些元素视为标题，向上/向下滑动手势现在将起作用，并将所选内容跳转到下一个/上一个标题。

[![](img/65b672eccd4cc26b21c5ae65099b0b5e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--jHFXOKfw--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://stu.dev/conteimg/2019/09/after.gif)

现在这个功能更好了，因为我们可以在标题之间快速跳转，并且仍然可以导航到它们周围的项目。现在让我们来建造它。

### 我们是怎么做到的？

事实证明，我们不需要太多的代码来实现这一点。我们将创建一个附加属性，该属性将自动为它所添加到的元素添加效果。

我们首先需要定义我们的附加属性，并创建仅在 iOS 上实现的效果:

```
using System;
using System.Linq;
using Xamarin.Forms;

namespace XamAppWithHeaders
{
    public static class Accessibility
    {
        [Flags]
        public enum AccessibilityTrait
        {
            None,
            Header
        }

        public static readonly BindableProperty AccessibilityTraitsProperty =
            BindableProperty.CreateAttached("AccessibilityTraits", typeof(AccessibilityTrait), typeof(Accessibility), AccessibilityTrait.None, propertyChanged: OnAccessibilityTraitsChanged);

        public static AccessibilityTrait GetAccessibilityTraits(BindableObject view)
        {
            return (AccessibilityTrait)view.GetValue(AccessibilityTraitsProperty);
        }

        public static void SetAccessibilityTraits(BindableObject view, AccessibilityTrait value)
        {
            view.SetValue(AccessibilityTraitsProperty, value);
        }

        static void OnAccessibilityTraitsChanged(BindableObject bindable, object oldValue, object newValue)
        {
            if (!(bindable is View view))
            {
                return;
            }

            var newTraits = (AccessibilityTrait)newValue;
            var hasTraits = newTraits != AccessibilityTrait.None;

            if (hasTraits)
            {
                if (!view.Effects.OfType<AccessibilityTraitEffect>().Any())
                {
                    view.Effects.Add(new AccessibilityTraitEffect());
                }
            }
            else
            {
                var accessibilityTrait = view.Effects.OfType<AccessibilityTraitEffect>().FirstOrDefault();
                if (accessibilityTrait != null)
                {
                    view.Effects.Remove(accessibilityTrait);
                }
            }
        }

        public class AccessibilityTraitEffect : RoutingEffect
        {
            public AccessibilityTraitEffect() : base("XamAppWithHeaders.AccessibilityTraitEffect")
            {
            }
        }
    }
} 
```

确保在`RoutingEffect`基本调用中更改`XamAppWithHeaders`以匹配您自己的名称空间。

现在，在我们的 iOS 项目中，我们将实现效果，并实际设置可访问性特征:

```
using UIKit;
using Xamarin.Forms;
using Xamarin.Forms.Platform.iOS;
using static XamAppWithHeaders.Accessibility;

[assembly: ResolutionGroupName("XamAppWithHeaders")]
[assembly: ExportEffect(typeof(XamAppWithHeaders.iOS.AccessibilityTraitEffect), "AccessibilityTraitEffect")]

namespace XamAppWithHeaders.iOS
{
    public class AccessibilityTraitEffect : PlatformEffect
    {
        protected override void OnAttached()
        {
            AddAccessibilityTraits();
        }

        protected override void OnDetached()
        {
        }

        protected override void OnElementPropertyChanged(System.ComponentModel.PropertyChangedEventArgs args)
        {
            if (args.PropertyName == AccessibilityTraitsProperty.PropertyName)
            {
                AddAccessibilityTraits();
            }
            else
            {
                base.OnElementPropertyChanged(args);
            }
        }

        void AddAccessibilityTraits()
        {
            var traits = Control.AccessibilityTraits;

            var newTraits = GetAccessibilityTraits(Element);

            if ((newTraits & AccessibilityTrait.Header) > 0) traits |= UIAccessibilityTrait.Header;

            Control.AccessibilityTraits = traits;
        }
    }
} 
```

...就这样，我们现在可以将名称空间引入我们的 xaml 视图，并将其添加到任何我们想要指示为标题的元素中。

```
<Label local:Accessibility.AccessibilityTraits="Header" Text="First Header" Style="{StaticResource headerStyle}" /> 
```

然而，你可以看到我们已经有了一个共享的标题样式，这是现实世界应用程序的常见情况，在本例中是`headerStyle`。原来你可以通过一个共享的样式来添加附加的属性，看起来是这样的:

```
<Style x:Key="headerStyle" TargetType="Label">
    <Setter Property="FontAttributes" Value="Bold" />
    <Setter Property="FontSize" Value="22" />
    <Setter Property="local:Accessibility.AccessibilityTraits" Value="Header" />
</Style> 
```

### 关闭思绪

只需在标题样式中添加 2 个代码文件和 1 行代码，就能让依赖 VoiceOver 的用户更容易使用您的应用程序。试一试😀

在这里可以找到一个工作样本[。](https://github.com/slang25/xamarin-forms-accessiblity-traits-demo)