# 如何为创建可重用的包？NET 核心使用 VS 代码，C#和 NuGet

> 原文：<https://dev.to/dotnet/how-you-can-create-reusable-packages-for-net-core-using-vs-code-c-and-nuget-3kn9>

在 [Twitter](https://twitter.com/chris_noring) 上关注我，很乐意接受您对主题或改进的建议/Chris

> 创建一个可重用的代码包是很棒的。它可以让你在下一次构建时更快地启动和运行。它也可以帮助你的同事，或者如果你把你的包上传到一个公开的存储库中，为什么不能帮助整个星球呢？

因为。净(包括。NET Core)，微软支持的代码共享机制是 NuGet，它定义了。NET 被创建、托管和使用。

所以 NuGet 是一个为微软开发平台设计的开源包管理器，于 2010 年推出。

> 亲爱的，是不是有很多包等着我用呢？

是的，他们都住在这里:

> [https://www.nuget.org/](https://www.nuget.org/)

您还可以通过终端中的一个简单命令或者使用一个 UI(如果您有一个成熟的 IDE，比如 Visual Studio)将这个包添加到您的项目中。

让我们学习如何创建这样一个包，一个所谓的 *nuget* 。NET 核心，使用 VS 代码。让我们也在`https://www.nuget.org`把它放在那里，这样其他人也可以欣赏它。

在本文中，我们希望了解以下内容:

*   **为什么**创建一个包。例如，了解我们为什么要创建一个包而不仅仅是创建一个库是很重要的。
*   **创建**一个库，这里我们将创建一个库，添加一些代码和一些测试。你应该总是测试你的代码。
*   **NuGet** 包创建，这里我们将使用终端命令创建一个 NuGet
*   在本地安装您的软件包，我们希望确保我们的软件包在本地工作。我们可以很容易地做到这一点，安装到我们现有的项目之一。
*   **发布**你的包到 NuGet 资源库，供任何人试用。我们将需要获得一些凭证以使 NuGet 能够执行这一步，但是这实际上只是一个命令。

## 资源

这里有一些重要的资源，你可能需要进一步扩展你的知识。网芯。

*   [获取概述文件](https://docs.microsoft.com/nuget/what-is-nuget?wt.mc_id=devto-blog-chnoring)

*   [创建包](https://docs.microsoft.com/nuget/create-packages/creating-a-package-dotnet-cli?wt.mc_id=devto-blog-chnoring)

*   [发布包](https://docs.microsoft.com/nuget/nuget-org/publish-a-package?wt.mc_id=devto-blog-chnoring)

*   [将 NuGet 包添加到您的项目中](https://docs.microsoft.com/dotnet/core/tools/dotnet-add-package?wt.mc_id=devto-blog-chnoring)

*   [nuget.org](https://www.nuget.org/)

*   [创建. NET 核心项目](https://docs.microsoft.com/dotnet/core/tutorials/with-visual-studio-code?wt.mc_id=devto-blog-chnoring)

*   [。NET Core CLI 概述](https://docs.microsoft.com/dotnet/core/tools/?tabs=netcore2x&wt.mc_id=devto-blog-chnoring)

*   一篇关于从……开始的文章。NET 核心和 VS 代码，显示 lib，控制台，测试项目，编译，运行，测试

## 为什么要创建包

好吧，你想要一个包裹。你知道你为什么想要一个吗？很多时候，创建可重用的东西意味着创建一个库。你可能是一个程序供应商，或者你可能是一个 OSS 开发者，或者你有另一个好的理由。使用 NuGet 分发您的代码意味着地球上的任何人都可以使用您的代码。

你是:

*   很好奇这是如何做到的？网芯？
*   你想创建一些可重用的东西，让任何人都可以访问和安装吗？

如果以上任何一个问题的答案是肯定的，那么你似乎已经准备好学习了。

> 跟着我，年轻的学徒，我们将会学到很多东西。

## 创建一个库

这个包应该是可重用的，就像一个库，我们可以在现有的项目中添加对它的引用。因为我们在使用。为此，我们将使用终端。我们将执行以下操作:

1.  **为我们的解决方案创建一个解决方案目录**
2.  **创建**解决方案文件本身
3.  **创建**一个类库类型的项目
4.  **添加**类库引用到解决方案

**创建解决方案目录**
首先，我们创建一个目录，记住，你可以随意命名。

```
mkdir example
cd example 
```

**创建一个解决方案文件**
之后，我们将使用以下命令搭建一个解决方案文件:

```
dotnet new sln 
```

它应该是这样说的:

[![](img/e978ef72da1a55d03ac69712e2156848.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--wWCv2zSi--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/x89s29y45528udhc3551.png)

**创建类库**
接下来我们将创建类库。这是我们的可重用代码将存在的地方。

```
dotnet new classlib -o library 
```

[![](img/ded7da8de383e41a211c7284ac21f768.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--67RUY9el--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/tjwvbpx2pz1mkwa8xjyh.png)

**将类库添加到解决方案中**
我们应该将这个类库添加到解决方案中。除了其他好处之外，这将使我们能够一次构建所有东西。

```
dotnet add sln add library/library.csproj 
```

这会产生以下结果:

[![](img/9f26486ddefd171205ad50c4f56ac789.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Eh6mR1x---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/vq8rru6r4ggmnh2r1q37.png)

## 添加一些代码和测试

好吧，我们有一个解决方案，一个类库，但是我们缺少了核心部分，我们希望世界使用的代码。

让我们打开我们的解决方案并添加一些代码。

将我们的`Class1.cs`文件重命名为`Start.cs`。现在让我们用下面的代码替换这里的代码:

```
 namespace library
{
  public class Start
  {
    public int Add(int a, int b) 
    {
        return a + b;
    }

    public int Sub(int a, int b)
    {
      return a - b;
    }
  }
} 
```

看，人类已知的最令人印象深刻的计算器。；)

**添加测试**
我们需要为此进行测试，因为我们是专业人士。

让我们从创建测试库
开始

```
dotnet new xunit -o library.testing 
```

接下来，我们需要添加对库项目的引用，以便测试代码。所以我们键入:

```
dotnet add library.testing/library.testing.csproj reference library/library.csproj 
```

这将为我们的测试项目添加一个引用。接下来让我们做一个测试。

将测试文件`UnitTest1.cs`重命名为`Test.cs`。你取什么名字并不重要，只要你给它取一个有意义的名字就好。

好了，对于测试，用下面的代码替换您的代码:

```
 // Test.cs

using System;
using Xunit;
using library;

namespace library.testing
{
    public class Test
    {
        [Fact]
        public void TestingAdd()
        {
            var start = new Start();
            var actual = start.Add(1, 2);
            Assert.Equal(3, actual);
        }

        [Fact]
        public void TestingSub()
        {
            var start = new Start();
            var actual = start.Sub(1, 2);
            Assert.Equal(-1, actual);
        }
    }
} 
```

在终端旁边输入:

```
dotnet test dotnet test library.testing/library.testing.csproj 
```

它应该呈现以下结果:

[![](img/78bd71b12926f050ac103405f77595ba.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ihM_OtYt--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/pjzk53gghernu9or9l71.png)

很好，现在我们已经准备好把它变成一个 NuGet 包了，我们的代码看起来已经可以工作了。

## 创建一个 NuGet 包

我们需要执行以下操作来创建一个 NuGet 包:

1.  向库项目添加一些元信息
2.  **调用**的`pack`命令，这将创建包

**添加元数据**
现在这是关于向你的项目添加元数据信息。元信息包括名字、作者、公司等等。您需要将它作为条目添加到 XML 标签`PropertyGroup`下的`library.csproj`文件中。可以是这样的:

```
<TargetFramework>netcoreapp2.2</TargetFramework>
<PackageId>math-chris</PackageId>
<Version>1.0.0</Version>
<Authors>chris_noring</Authors>
<Company>happy coder inc</Company> 
```

`PackageId` + `Version`将决定你的包的名字。接下来让我们创建我们的包。让我们导航到我们的图书馆目录并键入:

```
cd library
dotnet pack 
```

[![](img/6ed71c5fbdf7bb7a2d857796573f9271.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--5XLtjLT6--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/3s11hgm3s94nu7xbb518.png)

从上面的图片中我们可以看到，我们在`bin/Debug`文件夹中创建了一个名为`math-chris.1.0.0.nupkg`的包。

我们将在下一节学习本地安装包时使用这些信息。

## 在本地安装你的软件包

让我们从为什么开始？我们需要在本地测试这个包，然后才能推出它，nuget.org，这只是使用合理的判断，我们不想推出任何坏掉的东西。

那么我们如何做到这一点呢？我们需要一个项目来测试它。让我们搭建一个控制台应用程序

```
dotnet new console -o app 
```

让我们把它加入到解决方案中:

```
dotnet sln add app/app.csproj 
```

接下来，让我们现在安装我们的软件包。我们分两步完成:

1.  **指出**本地源代码，这是关于指示目标项目在你的计算机上哪里可以找到这个包，所以如果它在 NuGet 上找不到它，它知道下一步要查看你的计算机。

2.  **添加包**，这将在目标项目中安装包

### 指出本地来源

我们需要进入目标项目`.csproj`文件。我们想在名为`app`的控制台应用上测试我们的 NuGet 包，所以我们打开`app.csproj`，在`PropertyGroup`下添加一个名为`RestoreSources`的标签。这里我们指出了本地 NuGet 包和 NuGet 流的路径。应该是这样的:

```
<RestoreSources>$(RestoreSources);absolute-path-to-my-solution/library/bin/Debug;https://api.nuget.org/v3/index.json</RestoreSources> 
```

您需要将上面的`absolute-path-to-my-solution/library/bin/Debug`替换为您的包所在位置的绝对路径。

### 添加包

既然我们指出了 NuGet 可以在哪里找到我们的包，我们就可以安装它了。

这很简单，只需调用(从解决方案根):

```
dotnet add app package math-chris 
```

`math-chris`是我们在 meta 标签中给它的包名。

[![](img/18e67ac7095dfa3b7f55d7988267714f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Ut42ywtT--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/23fy8agsl901akce400i.png)

检查我们的`app.csproj`文件，我们可以看到我们现在得到了以下条目:

```
<PackageReference Include="math-chris" Version="1.0.1" /> 
```

现在让我们试试我们安装的软件包。将应用程序项目中的`Program.cs`更改为以下代码:

```
using System;
using library;

namespace app
{
  class Program
  {
    static void Main(string[] args)
    {
        var start = new Start();

        Console.WriteLine(string.Format("Hello World! {0}", start.Add(2, 2)));
    }
  }
} 
```

## 使用不同的版本

作为一个 NuGet 包的作者，你可能迟早要更新你的库，添加更多的功能或纠正错误。根据您所做的更新类型，您应该不同地增加版本号。让我们从总体上来看版本号，并讨论一个概念*语义版本化* :

```
1.0.0 
```

最左边的值称为主要版本。如果我们真的做了*重大*变更，包括破坏接口，删除或重命名代码，这个值应该增加。更新它意味着我们从`1.0.0`到`2.0.0`。

中间值称为次要版本。当你添加更多的功能，比如添加更多的方法时，你会用到它。更新它意味着我们从`1.0.0`到`1.1.0`。

最右边的值称为补丁版本。你应该用它来做一些小的改变。这些类型的改变通常是因为你想修复一个错误，例如*修补*代码。更新它意味着我们从`1.0.0`到`1.0.1`

### 升级

好的，那么你已经决定升级你的包裹。这意味着您需要采取以下步骤:

1.  向我们的库项目添加新功能
2.  **增量**软件包的次要版本
3.  从我们的库项目生成一个新的 NuGet 包
4.  **在我们的控制台项目上安装**新版本`app`

**添加新功能**

让我们去我们的图书馆项目。我们将添加代码来处理乘法，就像这样:

```
public int Multiply(int a, int b) 
{
  return a * b;
} 
```

`Start.cs`中的代码现在应该是这样的:

```
using System;

namespace library
{
  public class Start
  {
    public int Add(int a, int b) 
    {
        return a + b;
    }

    public int Sub(int a, int b)
    {
      return a - b;
    }

    public int Multiply(int a, int b) 
    {
        return a * b;
    }
  }
} 
```

**增加次要版本**

打开`library.csproj`，找到 XML 标签`Version`，确保它看起来像这样:

```
<Version>1.1.1</Version> 
```

**生成新的 NuGet 包**

为此，我们将导航到库目录并运行`dotnet pack`。

这将创建文件`math-chris.1.1.1.nupkg`

**安装新版本**

要安装新版本，我们需要打开我们的`app.csproj`并增加版本。我们需要找到 XML 标签`PackageReference`并将属性`Version`改为`1.1.1`，如下所示:

```
<PackageReference Include="math-chris" Version="1.1.1" /> 
```

此后，我们可以在我们的应用程序目录中运行`dotnet restore`。

你的终端应该是这样的:

```
Installing math-chris 1.1.1 
```

这意味着你得到了新的代码。让我们通过测试来确保这一点。记住，你应该可以使用`Multiply()`功能。打开`Program.cs`文件，将代码改为如下:

```
 using System;
using library;

namespace app
{
  class Program
  {
    static void Main(string[] args)
    {
        var start = new Start();
        var result = start.Multiply(2,2);

        Console.WriteLine(string.Format("Hello World! {0}", start.Add(2, 2)));
    }
  }
} 
```

### 管理版本

有时您会遇到以下情况:

*   **修复错误**，你制作了一个包，但是意识到你在你想要修复的代码中犯了一个错误，并且你想要保持相同的版本
*   **转到一个较低的版本**，你要移回一个先前版本的包

在这两种情况下，您都需要处理本地 NuGet 缓存。它目前的工作方式是，你总是可以增加一个版本，并做一个`dotnet restore`。返回意味着你需要先清空缓存，否则它会保留最新的 T2 版本。您可以通过下面的命令来做到这一点:

```
dotnet nuget locals all --clear 
```

现在你可以在`app.csproj`中将`Version`属性更改为一个较低的属性，然后转到终端和你的应用程序目录，进行`dotnet restore`。你的代码，在`Program.cs`中，现在应该对你大喊大叫，说你没有`Multiply()`方法，这是一件好事，这意味着整个过程都在正常工作。

请记住，您可以随时将`Version`改回`1.1.1`，执行`dotnet restore`，您的代码将再次正常工作:)

## 发布您的包

好的，我们花了很多时间讨论在本地测试你的包。有趣而重要的是，我们想把它发布到 NuGet 天空中伟大的 NuGet 资源库。

> 我们如何做到这一点？

您需要以下内容:

1.  获取凭据
2.  调用`publish`命令

**创建账户**

这是一件很快的事情。去 nuget.org，输入电子邮件、用户名和密码。一旦所有这些都完成了，你需要创建一个密钥。你可以点击页面右上角的用户名。然后你选择`API Keys`。

在页面上点击`+ Create`并填写`Key Name`，在`Selected Scopes`上勾选`Push`。在`Glob Pattern`场上只需输入一个`*`。最后一步是点击蓝色的`Create`按钮。向下滚动“立即管理”并找到`Manage`部分。你的新钥匙应该在这里。按下`Copy`按钮，您的密钥现在应该在您的剪贴板中。

**调用发布命令**

现在我们到了有趣的部分。我们已经完成了包装。我们已经得到了一把钥匙，只差一个命令就能把我们的包裹送到 nuget.org。

我们需要的命令如下:

```
dotnet nuget push [package name].nupkg -k [API key] -s https://api.nuget.org/v3/index.json 
```

将自己放在`library/bin/Debug`目录中，用上面的值替换，并在终端中运行命令。

您应该得到如下所示的响应:

[![](img/55eb07cd03c5741da99c107abbf7cf01.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--E0eE8TPa--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/2ehyj4gswnr10sc8dn2q.png)

现在，您应该可以在以下位置找到您的软件包:

> [https://www.nuget.org/packages/math-chris/1.1.2](https://www.nuget.org/packages/math-chris/1.1.2)

请记住。你的包裹没什么用。至少从这一点开始改进吧。如果它对你有用，对其他人也会有用:)

## 总结

我们已经到了最后一站。是时候回顾一下我们所学的内容了。我们已经学会了:

*   为什么创建一个包可能是一件好事
*   如何创建包
*   如何在本地测试包
*   如何将包裹推送到 nuget.org

希望这能激发你创建自己的包:)

去吧，去那里创造，没有尝试

[![](img/27e5ea603527a325056989ae62d48e64.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--qvhSgNLV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/emlq2cz45ce22g72myet.gif)