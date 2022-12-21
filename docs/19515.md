# C#中扩展方法的创造性使用

> 原文：<https://dev.to/tyrrrz/creative-use-of-extension-methods-in-c-1fe5>

我相信每个至少有一些 C#背景的人都知道扩展方法——这是一个很好的特性，允许开发人员用新方法扩展现有的类。

这在您想要向您无法控制的类型添加功能的情况下非常方便——我想每个人都曾为基类库编写过扩展，只是为了让某些东西更容易访问。

但是除了更明显的用例之外，还有一些有趣的模式直接依赖于扩展方法，展示了如何以稍微不太传统的方式使用它们。

## 向枚举添加方法

枚举只是一组具有唯一指定名称的常量数值。尽管 C#中的所有枚举都继承自`Enum`抽象类，但它们并不真正被视为类。这个限制，除了别的以外，阻止了它们拥有方法。

在某些情况下，在枚举上包含逻辑可能会有所帮助。其中一种情况是，一个枚举的值可以有多种不同的表示形式，并且您希望能够轻松地转换为其中的一种。

例如，在一个可以保存各种格式文件的通用应用程序中，想象以下类型:

```
public enum FileFormat
{
    PlainText,
    OfficeWord,
    Markdown
} 
```

Enter fullscreen mode Exit fullscreen mode

该枚举定义了应用程序支持的格式列表，并可能在代码中的不同位置使用，以根据其值调用分支逻辑。

由于每种文件格式也可以用它的文件扩展名来表示，如果`FileFormat`包含一个获取它的方法就好了。这就是我们可以使用扩展方法的地方，就像这样:

```
public static class FileFormatExtensions
{
    public static string GetFileExtension(this FileFormat fileFormat)
    {
        if (fileFormat == FileFormat.PlainText)
            return "txt";

        if (fileFormat == FileFormat.OfficeWord)
            return "docx";

        if (fileFormat == FileFormat.Markdown)
            return "md";

        // This will be thrown if we add a new file format but forget to add corresponding file extension
        throw new ArgumentOutOfRangeException(nameof(fileFormat));
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

这使得可以进行以下操作:

```
var format = FileFormat.Markdown;
var fileExt = format.GetFileExtension(); // "md"
var fileName = $"output.{fileExt}"; // "output.md" 
```

Enter fullscreen mode Exit fullscreen mode

## 重构模型类

有些情况下，你可能不希望将一个方法直接添加到一个类中，例如当它是一个模型时。

模型通常表示一组公共的只获取不可变属性，因此向模型类添加方法可能会使它看起来不纯，或者可能会让人怀疑这些方法正在访问一些私有状态。扩展方法没有这个问题，因为它们不能访问模型的私有成员，并且本质上不是模型本身的一部分。

所以考虑这个两个模型的例子——一个代表一个视频的隐藏字幕，另一个代表一个单独的字幕:

```
public class ClosedCaption
{
    // Text that gets displayed
    public string Text { get; }

    // When it gets displayed relative to beginning of the track
    public TimeSpan Offset { get; }

    // For how long does it get displayed
    public TimeSpan Duration { get; }

    public ClosedCaption(string text, TimeSpan offset, TimeSpan duration)
    {
        Text = text;
        Offset = offset;
        Duration = duration;
    }
}

public class ClosedCaptionTrack
{
    // Language of the closed captions inside
    public string Language { get; }

    // Collection of closed captions
    public IReadOnlyList<ClosedCaption> Captions { get; }

    public ClosedCaptionTrack(string language, IReadOnlyList<ClosedCaption> captions)
    {
        Language = language;
        Captions = captions;
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

在当前状态下，如果有人想要在特定时间点显示隐藏字幕，他们必须运行这样的 LINQ:

```
var time = TimeSpan.FromSeconds(67); // 1:07
var caption = track.Captions.FirstOrDefault(cc => cc.Offset <= time && cc.Offset + cc.Duration >= time); 
```

Enter fullscreen mode Exit fullscreen mode

这很容易出错，并且确实需要某种帮助方法——它可以作为成员方法或扩展方法来实现，但我个人更喜欢后者。

```
public static class ClosedCaptionTrackExtensions
{
    public static ClosedCaption GetByTime(this ClosedCaptionTrack track, TimeSpan time)
        => track.Captions.FirstOrDefault(cc => cc.Offset <= time && cc.Offset + cc.Duration >= time);
} 
```

Enter fullscreen mode Exit fullscreen mode

这里的扩展方法实现了与普通方法相同的功能，但是它提供了一些微妙的好处:

1.  很明显，这个方法只对类的公共成员有效，不会以某种模糊的方式改变它的私有状态。
2.  很明显，这个方法只是提供了一个快捷方式，它的存在只是为了方便。
3.  该方法是一个完全独立的类(甚至是程序集)的一部分，有助于保持代码的整洁。

总的来说，在这里使用扩展方法有助于在什么是*必需的*和什么是*有用的*之间画一条线。

## 让界面更加通用

当设计一个接口时，你总是希望保持契约最小化，以便更容易实现。当您的接口公开最通用类型的功能，以便其他人(或您)可以在它的基础上构建并涵盖更多特定的情况时，它会非常有帮助。

如果这没有任何意义，这里有一个将一些模型保存到文件中的典型界面的例子:

```
public interface IExportService
{
    FileInfo SaveToFile(Model model, string filePath);
} 
```

Enter fullscreen mode Exit fullscreen mode

它工作得很好，但是几个星期后你带着一个新的需求回来了——实现`IExportService`的类，除了导出到一个文件之外，现在还应该能够写入内存。

为了满足这个需求，您需要向契约中添加一个新的方法:

```
public interface IExportService
{
    FileInfo SaveToFile(Model model, string filePath);

    byte[] SaveToMemory(Model model);
} 
```

Enter fullscreen mode Exit fullscreen mode

这一改变使得所有现有的`IExportService`实现无效，现在所有的实现都必须更新以支持写入内存。

如果不这样做，我们可以设计一个稍微不同的初始版本的界面:

```
public interface IExportService
{
    void Save(Model model, Stream output);
} 
```

Enter fullscreen mode Exit fullscreen mode

通过这种方式，接口强制写入最普通的目的地- `Stream`。现在，我们不再局限于文件输出，可以有效地写入几乎任何内容。

这种方法唯一的缺点是更基本的操作不像以前那样简单了——现在您需要设置一个`Stream`的具体实例，将其包装在一个`using`语句中，并将其作为参数传递。

幸运的是，使用扩展方法可以完全消除这个缺点:

```
public static class ExportServiceExtensions
{
    public static FileInfo SaveToFile(this IExportService exportService, Model model, string filePath)
    {
        using (var output = File.Create(filePath))
        {
            exportService.Save(model, output);
            return new FileInfo(filePath);
        }        
    }

    public static byte[] SaveToMemory(this IExportService exportService, Model model)
    {
        using (var output = new MemoryStream())
        {
            exportService.Save(model, output);
            return output.ToArray();
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

通过重构初始接口，我们使它更加通用和可维护，并且，由于扩展方法，我们不必以任何方式牺牲可用性。

我相信这是艾伦·凯的名言“简单的事情应该简单，复杂的事情应该可能”的一个很好的例子。