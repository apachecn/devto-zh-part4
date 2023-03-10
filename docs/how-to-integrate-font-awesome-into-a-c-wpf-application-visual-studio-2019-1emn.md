# 如何将 Font Awesome 集成到 C# WPF 应用程序中(Visual Studio 2019)

> 原文：<https://dev.to/sucrose/how-to-integrate-font-awesome-into-a-c-wpf-application-visual-studio-2019-1emn>

如果你是一名前端 web 开发人员，你肯定听说过[字体牛逼](https://fontawesome.com/)。简单地总结一下，这是一个字体系列，主要在网络上作为一个快速简单的图标解决方案使用。为了方便起见，它以多种文件格式发布:SVG、OTF、TTF、EOT 和 WOFF/WOFF2。

对于本教程，我们将使用从[免费桌面软件包](https://fontawesome.com/how-to-use/on-the-desktop/setup/getting-started)获得的 OpenType (OTF)格式。(OpenType 有几个独有的功能，如支持 Windows 和 Macintosh 系统以及扩展的字符集。[了解更多关于 OpenType 和 TrueType 的区别。)](https://www.fonts.com/support/faq/font-formats)

1.  [下载](https://fontawesome.com/download)免费官方字体牛逼桌面包。(将 ZIP 存档文件解压缩到一个方便的位置。)【[截图](https://imgur.com/PyEiu1o)
2.  在 Visual Studio 中创建一个新项目[ [截图](https://imgur.com/Uf55Km0)
3.  选择 *WPF App(。NET Framework)* ，点击*下一步*，命名你的项目，然后点击*创建*[截图](https://imgur.com/meIFFPT)
4.  在*解决方案浏览器*窗口内，右击项目名称，点击*添加→新建文件夹*(将文件夹命名为“字体”)[ [截图](https://imgur.com/Wta6iHb) ]
5.  右键点击你创建的*字体*文件夹，点击*添加→现有项目...*，浏览到解压 ZIP 存档内容的字体 Awesome 目录，从...\ font awesome-free-5 . 8 . 1-desktop \ otfs \ directory。您可能需要将文件对话框过滤器更改为*所有文件(*)。* )* 查看文件。【[截图](https://imgur.com/Dd7Aua7)
6.  右键点击 *App.xaml* ，点击*打开*，然后在 *XAML* 窗口的 *<应用内添加下面一行代码。资源>* 标签:`<FontFamily x:Key="FontAwesome">/Fonts/Font Awesome 5 Free-Regular-400.otf#Font Awesome 5 Free</FontFamily>`[截图](https://imgur.com/FP6Gm7n)
7.  切换到 *MainWindow.xaml* 标签，然后在 *XAML* 窗口的 *<网格>* 标签内添加下面一行代码:`<TextBlock x:Name="TextBlock1" FontFamily="{StaticResource FontAwesome}" Text="&#xf004;" HorizontalAlignment="Center" Margin="0" TextWrapping="Wrap" VerticalAlignment="Center" FontSize="100"/>`[截图](https://imgur.com/I6jinU5)
8.  此时，在*设计*窗口中，你会注意到在*主窗口* GUI 的中间有一个心形图标。

**解释**
对于我们使用的 *TextBlock* 的 *FontFamily* 属性，我们链接到我们在步骤 6 中添加的静态资源键。( *x:Key* 属性值可以改变，如果你喜欢不同的东西。)您必须在*文本*属性中使用图标的 Unicode 值，以便正确显示。(字体 Awesome Unicode 值可以在[图标库](https://fontawesome.com/icons)中找到。)

**资源项目范围**
您可以在应用程序的任何地方引用字体 Awesome 资源，因为我们使用了 *<应用程序。第六步>* 资源。如果出于某种原因，应用程序范围是不受欢迎的，您可以将代码片段插入到一个 *<窗口中。任何窗口的 XAML 内的 Resources >* 标记将范围限制在该窗口内。【[截图](https://imgur.com/Y6iV9nQ)

**<窗口>的 FontFamily**
另一个不太流行的方法是将 *<窗口>的 FontFamily* 属性值设置为两部分的字体资源引用:基本统一资源标识符(URI)；和字体位置参考。例如:`FontFamily="/FontAwesome-Example;component/Fonts/#Font Awesome 5 Free Regular"`(这个方法的缺点是窗口中的每个组件都会继承 *FontFamily* 。)[ [截图](https://imgur.com/V3vo48U) ]

**从代码**中引用字体资源
你也可以在你的 C#代码中设置组件的字体族。例如:`TextBlock1.FontFamily = new FontFamily(new Uri("pack://application:,,,/"), "./Fonts/#Font Awesome 5 Free");`这在动态创建组件时很有用。【[截图](https://imgur.com/zQm7ANY)