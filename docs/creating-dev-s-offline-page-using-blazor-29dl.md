# 使用 Blazor 创建 DEV 的离线页面

> 原文：<https://dev.to/azure/creating-dev-s-offline-page-using-blazor-29dl>

我看到了一个来自阿里·斯皮特尔的有趣帖子，关于创建 [DEV 的离线](https://dev.to/offline)页面:

[![aspittel](img/45a8d86fe0ff981bf7cdb45b471a3c5c.png)](/aspittel) [## 如何在 DEV 的离线页面上创建绘图交互

### Ali Spittel，7 月 3 日至 19 日，4 分钟阅读

#javascript #art #showdev](/aspittel/how-to-create-the-drawing-interaction-on-dev-s-offline-page-1mbe)

鉴于我过去用 WebAssembly 做过一些[实验，我决定尝试一下自己在 WebAssembly 中的实现，特别是用](https://dev.to/aaronpowell/learning-golang-through-webassembly---go-wasm-typescript-and-react-klm) [Blazor](https://dotnet.microsoft.com/apps/aspnet/web-apps/client?WT_mc.id=devto-blog-aapowell) 。

## 入门

*警告:Blazor 是一个使用。NET 堆栈，特别是 C#语言。它是高度实验性的，所以有可能在编写时会发生变化(我用的是 build `3.0.0-preview6.19307.2`)。*

首先，你需要遵循 Blazor 的[设置指南，一旦完成，在你最喜欢的编辑器中创建一个新项目(我用的是 VS 代码)。](https://docs.microsoft.com/en-gb/aspnet/core/blazor/get-started?view=aspnetcore-3.0&tabs=visual-studio-code&WT_mc.id=devto-blog-aapowell)

然后我从`Pages`和`Shared`文件夹中删除了所有的样板代码(除了任何`_Imports.razor`文件)，从`css`文件夹和`sample-data`中删除了引导程序。现在我们有一个完全空的 Blazor 项目。

## 创建我们的布局

我们需要做的第一件事是创建[布局文件](https://docs.microsoft.com/en-gb/aspnet/core/blazor/layouts?view=aspnetcore-3.0&WT_mc.id=devto-blog-aapowell)。和 ASP.NET MVC 一样，Blazor 使用一个布局文件作为所有页面的基础模板(所有使用这个布局的页面，你可以有多个布局)。因此，在`Shared`中创建一个名为`MainLayout.razor`的新文件，我们将定义它。鉴于我们希望它是全屏的，这将是*非常简单* :

```
@inherits LayoutComponentBase

@Body 
```

Enter fullscreen mode Exit fullscreen mode

这个文件继承了 Blazor 为布局提供的基类，`LayoutComponentBase`,它让我们可以访问`@Body`属性，这个属性允许我们将页面内容放在任何我们想要的 HTML 中。我们不需要在它周围放置任何东西，所以我们只需要在页面中放置`@Body`。

## 创建我们的离线页面

制作离线页面的时间到了，我们首先在`Pages`文件夹中创建一个新文件，姑且称之为`Offline.html` :

```
@page "/"

<h3>Offline</h3> 
```

Enter fullscreen mode Exit fullscreen mode

这是我们的起点，首先我们有`@page`指令，它告诉 Blazor 这是一个我们可以导航到的页面，它将响应的 URL 是`"/"`。我们有一些占位符 HTML 在那里，我们将取代下一步。

### 启动画布

离线页面本质上是一个我们可以在上面绘图的大画布，我们需要创建它，让我们用画布元素
更新`Offline.razor`

```
@page "/"

<canvas></canvas> 
```

Enter fullscreen mode Exit fullscreen mode

### 设置画布大小

我们需要设置画布的大小为全屏，现在是`0x0`，不理想。理想情况下，我们希望获得浏览器的`innerWidth`和`innerHeight`，为此我们需要使用 Blazor 的 [JavaScript interop](https://docs.microsoft.com/en-gb/aspnet/core/blazor/javascript-interop?view=aspnetcore-3.0&WT_mc.id=devto-blog-aapowell) 。

我们将快速创建一个新的 JavaScript 文件进行互操作(将其命名为`helper.js`并将其放入`wwwroot`，同时更新`wwwroot`中的`index.html`以引用它):

```
window.getWindowSize = () => {
    return { height: window.innerHeight, width: window.innerWidth };
}; 
```

Enter fullscreen mode Exit fullscreen mode

接下来我们将创建一个 C# `struct`来表示这些数据(我在项目根目录中添加了一个名为`WindowSize.cs`的文件):

```
namespace Blazor.DevToOffline
{
    public struct WindowSize
    {
        public long Height { get; set; }
        public long Width { get; set; }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

最后，我们需要在 Blazor 组件中使用它:

```
@page "/"
@inject IJSRuntime JsRuntime

<canvas height="@windowSize.Height" width="@windowSize.Width"></canvas>

@code {
    WindowSize windowSize;

    protected override async Task OnInitAsync()
    {
        windowSize = await JsRuntime.InvokeAsync<WindowSize>("getWindowSize");
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

这是添加的一点代码，让我们来分解它。

```
@inject IJSRuntime JsRuntime 
```

Enter fullscreen mode Exit fullscreen mode

这里我们使用[依赖注入](https://docs.microsoft.com/en-gb/aspnet/core/blazor/dependency-injection?view=aspnetcore-3.0&WT_mc.id=devto-blog-aapowell)将`IJSRuntime`作为一个名为`JsRuntime`的属性注入到我们的组件中。

```
<canvas height="@windowSize.Height" width="@windowSize.Width"></canvas> 
```

Enter fullscreen mode Exit fullscreen mode

接下来，我们将把`<canvas>`元素的`height`和`width`属性设置为我们的`struct` 实例*字段的值，该实例名为`windowSize`。注意`@`前缀，这告诉编译器这是指 C#变量，而不是静态字符串。* 

```
@code {
    WindowSize windowSize;

    protected override async Task OnInitAsync()
    {
        windowSize = await JsRuntime.InvokeAsync<WindowSize>("getWindowSize");
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

现在，我们已经在组件中添加了一个代码块。它包含变量`windowSize`(该变量未初始化，但它是一个结构，因此它有一个默认值)，然后我们覆盖一个[生命周期方法](https://docs.microsoft.com/en-gb/aspnet/core/blazor/components?view=aspnetcore-3.0&WT_mc.id=devto-blog-aapowell#lifecycle-methods)，`OnInitAsync`，在该方法中，我们调用 JavaScript 来获取窗口大小并将其赋给我们的局部变量。

恭喜你，你现在有一个全屏画布！🎉

## 接线起事件

我们可能已经显示了画布，但是它还没有做任何事情，所以让我们通过添加一些事件处理程序来解决这个问题:

```
@page "/"
@inject IJSRuntime JsRuntime

<canvas height="@windowSize.Height"
        width="@windowSize.Width"
        @onmousedown="@StartPaint"
        @onmousemove="@Paint"
        @onmouseup="@StopPaint"
        @onmouseout="@StopPaint" />

@code {
    WindowSize windowSize;

    protected override async Task OnInitAsync()
    {
        windowSize = await JsRuntime.InvokeAsync<WindowSize>("getWindowSize");
    }

    private void StartPaint(UIMouseEventArgs e)
    {
    }

    private async Task Paint(UIMouseEventArgs e)
    {
    }

    private void StopPaint(UIMouseEventArgs e)
    {
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

当你在 Blazor 中绑定事件时，你需要给事件名加上前缀`@`，比如`@onmousedown`，然后提供事件发生时要调用的函数名，比如`@StartPaint`。这些函数的特征是返回一个`void`或`Task`，这取决于它是否是异步的。该函数的参数需要是适当类型的事件参数，映射到 DOM 等价物(`UIMouseEventArgs`、`UIKeyboardEventArgs`等)。).

*注意:如果您将它与 JavaScript 参考实现进行比较，您会注意到我没有使用`touch`事件。这是因为，在我今天的实验中，Blazor 中有一个绑定触摸事件的 bug。记住，这是预演！*

## 获取画布上下文

*注意:我将讨论如何从 Blazor 设置与`<canvas>`的交互，但在实际应用中，你更可能想使用 [BlazorExtensions/Canvas](https://github.com/blazorExtensions/Canvas/) 而不是 roll-you-own。*

因为我们需要使用画布的 2D 上下文，所以我们需要访问它。但问题是，这是一个 JavaScript API，而我们是在 C#/WebAssembly 中，这会有点有趣。

最终，我们将不得不用 JavaScript 来实现这一点，并依赖于 Blazor 的 JavaScript 互操作特性，因此仍然无法避免编写一些 JavaScript 代码！

让我们写一个小的 JavaScript 模块来给我们一个 API:

```
((window) => {
    let canvasContextCache = {};

    let getContext = (canvas) => {
        if (!canvasContextCache[canvas]) {
            canvasContextCache[canvas] = canvas.getContext('2d');
        }
        return canvasContextCache[canvas];
    };

    window.__blazorCanvasInterop = {
        drawLine: (canvas, sX, sY, eX, eY) => {
            let context = getContext(canvas);

            context.lineJoin = 'round';
            context.lineWidth = 5;
            context.beginPath();
            context.moveTo(eX, eY);
            context.lineTo(sX, sY);
            context.closePath();
            context.stroke();
        },

        setContextPropertyValue: (canvas, propertyName, propertyValue) => {
            let context = getContext(canvas);

            context[propertyName] = propertyValue;
        }
    };
})(window); 
```

Enter fullscreen mode Exit fullscreen mode

我用一个匿名自执行函数创建的闭包作用域来实现这一点，这样我用来避免不断获取上下文的`canvasContextCache`就不会暴露出来。

该模块为我们提供了两个功能，第一个是在画布上的两点之间画一条线(我们需要它来涂鸦！)第二个更新了上下文的一个属性(我们需要它来改变颜色！).

你可能也注意到了，我从来没有调用过`document.getElementById`，我只是以某种方式“神奇地”得到了画布。这可以通过[捕获 C#中的组件引用](https://docs.microsoft.com/en-gb/aspnet/core/blazor/components?view=aspnetcore-3.0&WT_mc.id=devto-blog-aapowell#capture-references-to-components)并传递该引用来实现。

但这仍然都是 JavaScript，我们用 C#做什么？我们创建了一个 C#包装类！

```
public class Canvas2DContext
{
    private readonly IJSRuntime jsRuntime;
    private readonly ElementRef canvasRef;

    public Canvas2DContext(IJSRuntime jsRuntime, ElementRef canvasRef)
    {
        this.jsRuntime = jsRuntime;
        this.canvasRef = canvasRef;
    }

    public async Task DrawLine(long startX, long startY, long endX, long endY)
    {
        await jsRuntime.InvokeAsync<object>("__blazorCanvasInterop.drawLine", canvasRef, startX, startY, endX, endY);
    }

    public async Task SetStrokeStyleAsync(string strokeStyle)
    {
        await jsRuntime.InvokeAsync<object>("__blazorCanvasInterop.setContextPropertyValue", canvasRef, "strokeStyle", strokeStyle);
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

这是一个通用类，它接受捕获的引用和 JavaScript interop API，只是给了我们一个更好的编程接口。

## 串起我们的脉络

我们现在可以连接上下文，准备在画布上画线:

```
@page "/"
@inject IJSRuntime JsRuntime

<canvas height="@windowSize.Height"
        width="@windowSize.Width"
        @onmousedown="@StartPaint"
        @onmousemove="@Paint"
        @onmouseup="@StopPaint"
        @onmouseout="@StopPaint"
        @ref="@canvas" />

@code {
    ElementRef canvas;

    WindowSize windowSize;

    Canvas2DContext ctx;
    protected override async Task OnInitAsync()
    {
        windowSize = await JsRuntime.InvokeAsync<WindowSize>("getWindowSize");
        ctx = new Canvas2DContext(JsRuntime, canvas);
    }

    private void StartPaint(UIMouseEventArgs e)
    {
    }

    private async Task Paint(UIMouseEventArgs e)
    {
    }

    private void StopPaint(UIMouseEventArgs e)
    {
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

通过将`@ref="@canvas"`添加到我们的`<canvas>`元素中，我们创建了我们需要的引用，然后在`OnInitAsync`函数中，我们创建了我们将使用的`Canvas2DContext`。

## 在画布上画画

我们终于准备好在画布上画画了，这意味着我们需要实现这些事件处理程序:

```
 bool isPainting = false;
    long x;
    long y;
    private void StartPaint(UIMouseEventArgs e)
    {
        x = e.ClientX;
        y = e.ClientY;
        isPainting = true;
    }

    private async Task Paint(UIMouseEventArgs e)
    {
        if (isPainting)
        {
            var eX = e.ClientX;
            var eY = e.ClientY;

            await ctx.DrawLine(x, y, eX, eY);
            x = eX;
            y = eY;
        }
    }

    private void StopPaint(UIMouseEventArgs e)
    {
        isPainting = false;
    } 
```

Enter fullscreen mode Exit fullscreen mode

不可否认，这些与 JavaScript 实现没有什么不同，它们所要做的就是从鼠标事件中获取坐标，然后将它们传递给画布上下文包装器，画布上下文包装器进而调用适当的 JavaScript 函数。

## 结论

🎉我们完了！你可以看到它在这里运行，代码在 GitHub 上。

## ![GitHub logo](img/292a238c61c5611a7f4d07a21d9e8e0a.png)[aaronpowell](https://github.com/aaronpowell)/[blazor-devto-offline](https://github.com/aaronpowell/blazor-devto-offline)

### 如何使用 Blazor 创建 DEV.to 的离线页面的演示

<article class="markdown-body entry-content container-lg" itemprop="text">

这个库包含了一个如何使用 [Blazor](https://dotnet.microsoft.com/apps/aspnet/web-apps/client?WT_mc.id=blazordevtooffline-github-aapowell) 创建 [DEV.to](https://dev.to) [离线页面](https://dev.to/offline)的例子。

你可以在这里找到它的身影[https://blazordevtooffline.z23.web.core.windows.net/](https://blazordevtooffline.z23.web.core.windows.net/)。

</article>

[View on GitHub](https://github.com/aaronpowell/blazor-devto-offline)

这是对 Blazor 的一个快速浏览，但更重要的是，我们如何在一个场景中使用 Blazor，这个场景可能需要我们用 JavaScript 做更多的互操作，这是许多场景都需要的。

我希望你喜欢它，并准备好处理自己的 Blazor 实验！

## 奖金，拾色器

在上面的例子中有一件事我们没有做，那就是实现颜色选择器！

我想作为一个通用组件这样做，所以我们可以这样做:

```
<ColourPicker OnClick="@SetStrokeColour"
              Colours="@colours" /> 
```

Enter fullscreen mode Exit fullscreen mode

在一个名为`ColourPicker.razor`(文件名很重要，因为这是组件的名称)的新文件中，我们将创建组件:

```
<div class="colours">
    @foreach (var colour in Colours)
    {
        <button class="colour"
                @onclick="@OnClick(colour)"
                @key="@colour">
        </button>
    }
</div>

@code {
    [Parameter]
    public Func<string, Action<UIMouseEventArgs>> OnClick { get; set; }

    [Parameter]
    public IEnumerable<string> Colours { get; set; }
} 
```

Enter fullscreen mode Exit fullscreen mode

我们的组件将有 2 个参数，可以从父设置，颜色的集合和当你点击按钮时调用的函数。我做的事件处理程序是这样的，你传入一个返回动作的*函数，所以当`<button>`元素被创建时，它是一个“绑定”到颜色名称的单一函数。*

这意味着我们有这样的用法:

```
@page "/"
@inject IJSRuntime JsRuntime

<ColourPicker OnClick="@SetStrokeColour"
              Colours="@colours" />

// snip

@code {
    IEnumerable<string> colours = new[] { "#F4908E", "#F2F097", "#88B0DC", "#F7B5D1", "#53C4AF", "#FDE38C" };

    // snip

    private Action<UIMouseEventArgs> SetStrokeColour(string colour)
    {
        return async _ =>
        {
            await ctx.SetStrokeStyleAsync(colour);
        };
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

现在，如果你点击顶部的颜色选择器，你会得到一个不同颜色的笔。

涂鸦快乐！