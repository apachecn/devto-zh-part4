# 用风格美化事物

> 原文：<https://dev.to/eatskolnikov/prettying-things-up-with-styles-1ma5>

使用样式，你可以为你的应用程序定义一个一致的用户界面，从长远来看，这是一个让你的 XAML 文件更具可读性和可维护性的好方法。

一些简洁的风格特征:

*   它们很容易定义。
*   可以被继承以最小化代码重用。
*   可以在 XAML 或 C#中定义。
*   您可以实现多个样式类，这样同一个控件无需继承。

要定义一个**样式**，你需要将它作为一个资源添加。您可以在特定页面或应用程序类中定义它们。这里有一个例子:

[![](img/4b9e8f11b791f2c34cf508df9b43155e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--1xXasnNe--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.torib.io/wp-content/uploads/2019/07/antesdespues.png) 

<figcaption>左边是风格前 app 的样子，右边是</figcaption>

后的结果

让我们关注这一部分:

*   ![](img/dc2070823b52d0142acda68a7859d58a.png)

看看我们如何在 ContentPage 的资源字典中添加样式定义，我们也可以在应用程序级别这样做。风格定义再清楚不过了，以下是它的部分内容:

*   **TargetType** 属性:这个属性是强制的，它定义了我们应用样式的控件的类型以及我们可以访问设置的属性。
*   **Setter** 标签:您必须为每个想要设置的属性添加一个 Setter 标签。它有一个**属性**属性和一个**值**属性。visual studio 中的智能感知根据**目标类型**自动完成可用的属性和值。
*   **x:Key** 属性:该属性是可选的。当你设置这个属性时，你就直接将样式定义为一个显式类型。您必须设置想要影响的控件的样式属性。

风格有六种:

*   **全局样式:**您可以在应用程序中定义自己的样式，并在整个项目中使用它们
*   **隐式样式:**这是默认行为，只要你没有给样式分配一个键，它将被隐式地应用于指定 TargetType 的所有控件
*   **显式样式:**当你定义一个样式并为其设置一个键时，你需要显式地设置你想要应用该样式的控件的样式属性。
*   **动态样式:**虽然样式在运行时不能改变，但是您可以使用动态资源作为一种变通方法来实现这种效果。
*   **设备样式:**这些目前只能应用于标签实例，在设备中总共有六个。样式类: [BodyStyle](https://docs.microsoft.com/en-us/dotnet/api/xamarin.forms.device.styles.bodystyle?view=xamarin-forms) ， [CaptionStyle](https://docs.microsoft.com/en-us/dotnet/api/xamarin.forms.device.styles.captionstyle?view=xamarin-forms) ， [ListItemDetailTextStyle](https://docs.microsoft.com/en-us/dotnet/api/xamarin.forms.device.styles.listitemdetailtextstyle?view=xamarin-forms) ， [ListItemTextStyle](https://docs.microsoft.com/en-us/dotnet/api/xamarin.forms.device.styles.listitemtextstyle?view=xamarin-forms) ， [SubtitleStyle](https://docs.microsoft.com/en-us/dotnet/api/xamarin.forms.device.styles.subtitlestyle?view=xamarin-forms) 和 [TitleStyle](https://docs.microsoft.com/en-us/dotnet/api/xamarin.forms.device.styles.titlestyle?view=xamarin-forms)

您可以使用 BasedOn 属性从样式中继承。样式可以应用于 Xamarin 表单中的每个可视元素。如果您计划从可视元素继承并为其添加样式，请确保在 TargetType 中指定类名，或者在 Style 标记上将 ApplyToDerivedTypes 设置为 True。

一如既往，我希望这对你有所帮助。直到下一个。

用风格美化事物的帖子最先出现在 [Enmanuel Toribio](https://blog.torib.io) 上。