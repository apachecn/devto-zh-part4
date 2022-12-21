# PowerShell 中复合类型的 JSONPath，带有额外的调试工具

> 原文：<https://dev.to/sarafian/jsonpath-for-composite-types-in-powershell-with-extra-tools-for-debugging-5df4>

虽然 JSONPath 是在 PowerShell 中实现 [JSONPath](https://goessner.net/articles/JsonPath/) 概念的独立解决方案，但它是作为一个更大问题的一部分开发的，该问题在[关于 JSONPath、SOAP 和 Amadeus 与 PowerShell](https://sarafian.github.io/amadeus/2019/06/24/powershell-jsonpath-soap-proxy-amadeus.html) 的帖子介绍中讨论。

# 简介

PowerShell 模块 [JSONPath](https://github.com/Sarafian/1ASOAP/tree/master/Source/JSONPath) 是 Stefan 的 Goessner JSONPath 概念的实现，如这里[所述](https://goessner.net/articles/JsonPath/)。该模块最适用于类型化对象。该模块的目的是允许使用基于 JSONPath 的表达式对对象进行`Find`和`Set`操作。当*发现*时，模块为空安全。当*设置*时，模块将在遇到`null`值时创建元素。`set`操作目前适用于类型化而非动态或`PSObject`类型。当使用复合非动态类型时，事情会变得复杂和困难，这也是该模块最出色的地方。

该模块的 cmdlets 有:

*   `Find-JSONPath`
*   `Set-JSONPath`
*   `Trace-JSONPath`

# 查找操作

`Find`操作允许查询对象，并且只返回那些满足给定 JSONPath 条件的对象。假设您有一个由 10 个相同或不同类型的对象组成的列表，其中包含复杂的数据，我们希望根据深入每个对象结构的条件对这些数据进行过滤。如果我们可以为 JSONPath 解析的值定义一个条件，同时操作补偿中间的`null`值或数组，而不是编写复杂的代码结构，事情会简单得多。我们可以用多层循环和条件来编码，或者我们可以使用`Find-JSONPath` cmdlet。在其操作过程中，cmdlet 确保可以为每个对象解析路径，然后应用条件。如果在路径解析过程中遇到空值，则该条件被视为`false`。如果在路径解析期间遇到数组，则 cmdlet 将遍历每个数组。

理解`Find-JSONPath` cmdlet 不会将值解析为输出，也不会显示哪个中间数组满足条件，这一点很重要。相反，它只是过滤掉所有不满足条件的对象，作为任何可能路径的一部分。

让我们假设有一个`$retrieveFacts`变量，它是一个 2 项的数组，用下面的 XML
可视化

```
<retrievalFacts>
    <retrieve>
        <type>2</type>
    </retrieve>
</retrievalFacts>
<retrievalFacts>
    <retrieve>
        <type>3</type>
    </retrieve>
</retrievalFacts> 
```

下面的 PowerShell 命令将只返回变量`$retrieveFacts`中的第二个`element`。

```
$retrieveFacts|Find-JSONPath  -Path  "retrieve.type"  -EQ  3 
```

如果对象类似于下面的 xml，它仍然会返回第二个`element`。

```
<retrievalFacts>
    <retrieve>
        <type>2</type>
    </retrieve>
</retrievalFacts>
<retrievalFacts>
    <retrieve>
        <type>3</type>
    </retrieve>
    <retrieve>
        <type>2</type>
    </retrieve>
</retrievalFacts> 
```

如果路径是`retrieve[1].type`或`invalid.type`，那么命令将返回`null`。

上面的命令比其等价的循环和条件要简单和干净得多。在更干净的代码之上，它也是可重用的。

在`Find-JSONPath`上有更多的条件，目的是将它们与`Where-Object`匹配，因为在内部，最后的过滤是通过`Where-Object` cmdlet 实现的。

# 设定操作

当使用复合类型时，此功能特别有用。在脚本语言中，我们不希望在属性为`null`时初始化每个属性，也不希望初始化数组。例如，如果属性是复合类型的数组，那么应该首先初始化数组，然后初始化数组中的每一项。这需要明确了解所涉及的类型，并检查每一步的值是否为空。这是像 C#这样的语言所期望的，但是在像 PowerShell 这样的脚本环境中，我们更愿意在像`retrievalFacts.retrieve.type=3`这样的表达式上设置值。

要创建用上述 XML 片段表示的对象，请执行以下 PowerShell 脚本。

```
$obj  |  Set-JSONPath  -Path  "retrievalFacts[1].retrieve.type"  -Value  2  $obj  |  Set-JSONPath  -Path  "retrievalFacts[0].retrieve.type"  -Value  3 
```

上面的命令比它们的循环、条件和隐含的类型知识更简单、更清晰。在更干净的代码之上，它也是可重用的，因为类型是由`Set-JSONPath` cmdlet 使用反射发现的。如果适用，cmdlet 还可以处理不同类型的对象列表，例如`@($objOfType1,$objOfType2) | | Set-JSONPath -Path "expression" -Value $value`

**注意**当 JSONPath 遍历数组属性时，需要考虑以下几点:

*   当 JSONPath 在没有明确指定索引的情况下引用数组时，将会引发一个警告，但是该数组仍然会用 1 项进行初始化。例如`retrievalFacts.retrieve.type`与`retrievalFacts[0].retrieve.type`相同。
*   当涉及数组属性时，它将在为空时初始化一次。数组的大小将与指定的索引相匹配。例如，`retrievalFacts[0]`或`retrievalFacts`将创建一个包含 1 个项目的数组，而`retrievalFacts[10]`将创建 10 个项目。下面的所有语句都需要受到最大长度的约束，否则会出现越界错误。例如`retrievalFacts[20]`将不起作用。因此，最好从最大尺寸开始，如上例所示。

# 跟踪操作

当在 PowerShell 中使用复合类型结构时，事情一点也不简单。假设一个人可以访问复合类型的 C#代码，事情会简单得多，但这有两个问题。不是每个人都喜欢。而 PowerShell 不是。尽管它是建立在. NET 之上的。此外，复合类型的源代码并不总是可用的。通常类型是前一条指令(例如`New-WebServiceProxy`)的副产品，它在会话中注入了我们需要使用的新类型。如前所述，该模块是在 SOAP 自动化环境中开发的，特别是在 Amadeus API 上的自动化，Amadeus API 提供非常复杂和嵌套的类型，在这种情况下，源代码不可用，而是在内存中生成。如果你想了解更多关于`New-WebServiceProxy`的内容，请参考【PowerShell 中改进的 SOAP 代理管理】(16)。尽管`Find-JSONPath`和`Set-JSONPath`提供了 JSON path 的简化，我们仍然需要知道如何连接类型来构建 JSON path。出于这个原因，实现了`Trace`功能来帮助加速和简化开发体验。

根据我们是要初始化一个对象还是要处理它，我们需要分别可视化类型或实例:

*   当`setting`时，我们需要所有可能的 JSONPaths。
*   当`finding`时，我们只需要看到导致设置值的 JSONPaths。

当`setting`时，`Trace-JSONPath`有两种可能的输出:

*   所有有效的 JSONPaths。我们用这个来理解结构。
*   为所有有效的 JSONPaths 设置随机值的代码。我们可以用这个直接复制粘贴代码，保留我们需要的行，最后调整我们需要的值。

作为相对复杂类型的一个例子，让我们使用在 [JSONPath](https://github.com/Sarafian/1ASOAP/tree/master/Source/JSONPath) 模块中定义的复合类型进行测试

```
using System;

namespace JSONPath.Pester
{
    public class Root
    {
        public string StringSingle{get;set;}
        public string[] StringArray{get;set;}

        public int IntSingle{get;set;}
        public int[] IntArray{get;set;}

        public Type1 Type1Single{get;set;}
        public Type1[] Type1Array{get;set;}

    }
    public class Type1
    {
        public string StringSingle{get;set;}
        public string[] StringArray{get;set;}

        public int IntSingle{get;set;}
        public int[] IntArray{get;set;}

        public Type2 Type2Single{get;set;}
        public Type2[] Type2Array{get;set;}
    }
    public class Type2
    {
        public string StringSingle{get;set;}
        public string[] StringArray{get;set;}

        public int IntSingle{get;set;}
        public int[] IntArray{get;set;}
    }    
} 
```

## 跟踪类型以帮助编写 JSONPaths

`Trace-JSONPath -Type ("JSONPath.Pester.Root" -as [type])`命令输出以下排列:

```
IntArray[0]=0
IntSingle=0
StringArray[0]="String"
StringSingle="String"
Type1Array[0].IntArray[0]=0
Type1Array[0].IntSingle=0
Type1Array[0].StringArray[0]="String"
Type1Array[0].StringSingle="String"
Type1Array[0].Type2Array[0].IntArray[0]=0
Type1Array[0].Type2Array[0].IntSingle=0
Type1Array[0].Type2Array[0].StringArray[0]="String"
Type1Array[0].Type2Array[0].StringSingle="String"
Type1Array[0].Type2Single.IntArray[0]=0
Type1Array[0].Type2Single.IntSingle=0
Type1Array[0].Type2Single.StringArray[0]="String"
Type1Array[0].Type2Single.StringSingle="String"
Type1Single.IntArray[0]=0
Type1Single.IntSingle=0
Type1Single.StringArray[0]="String"
Type1Single.StringSingle="String"
Type1Single.Type2Array[0].IntArray[0]=0
Type1Single.Type2Array[0].IntSingle=0
Type1Single.Type2Array[0].StringArray[0]="String"
Type1Single.Type2Array[0].StringSingle="String"
Type1Single.Type2Single.IntArray[0]=0
Type1Single.Type2Single.IntSingle=0
Type1Single.Type2Single.StringArray[0]="String"
Type1Single.Type2Single.StringSingle="String" 
```

相同的输出可用于呈现用`Set-JSONPath`设置随机值的代码片段。下面的`Trace-JSONPath -Type ("JSONPath.Pester.Root" -as [type]) -RenderCode`输出下面的 PowerShell 片段

```
$obj=New-Object  -TypeName  "JSONPath.Pester.Root"  $obj=$obj|Set-JSONPath  -Path  "Type1Single.Type2Single.StringSingle"  -Value  "String"  -PassThru  |  Set-JSONPath  -Path  "Type1Single.Type2Single.StringArray[0]"  -Value  "String"  -PassThru  |  Set-JSONPath  -Path  "Type1Single.Type2Single.IntSingle"  -Value  0  -PassThru  |  Set-JSONPath  -Path  "Type1Single.Type2Single.IntArray[0]"  -Value  0  -PassThru  |  Set-JSONPath  -Path  "Type1Single.Type2Array[0].StringSingle"  -Value  "String"  -PassThru  |  Set-JSONPath  -Path  "Type1Single.Type2Array[0].StringArray[0]"  -Value  "String"  -PassThru  |  Set-JSONPath  -Path  "Type1Single.Type2Array[0].IntSingle"  -Value  0  -PassThru  |  Set-JSONPath  -Path  "Type1Single.Type2Array[0].IntArray[0]"  -Value  0  -PassThru  |  Set-JSONPath  -Path  "Type1Single.StringSingle"  -Value  "String"  -PassThru  |  Set-JSONPath  -Path  "Type1Single.StringArray[0]"  -Value  "String"  -PassThru  |  Set-JSONPath  -Path  "Type1Single.IntSingle"  -Value  0  -PassThru  |  Set-JSONPath  -Path  "Type1Single.IntArray[0]"  -Value  0  -PassThru  |  Set-JSONPath  -Path  "Type1Array[0].Type2Single.StringSingle"  -Value  "String"  -PassThru  |  Set-JSONPath  -Path  "Type1Array[0].Type2Single.StringArray[0]"  -Value  "String"  -PassThru  |  Set-JSONPath  -Path  "Type1Array[0].Type2Single.IntSingle"  -Value  0  -PassThru  |  Set-JSONPath  -Path  "Type1Array[0].Type2Single.IntArray[0]"  -Value  0  -PassThru  |  Set-JSONPath  -Path  "Type1Array[0].Type2Array[0].StringSingle"  -Value  "String"  -PassThru  |  Set-JSONPath  -Path  "Type1Array[0].Type2Array[0].StringArray[0]"  -Value  "String"  -PassThru  |  Set-JSONPath  -Path  "Type1Array[0].Type2Array[0].IntSingle"  -Value  0  -PassThru  |  Set-JSONPath  -Path  "Type1Array[0].Type2Array[0].IntArray[0]"  -Value  0  -PassThru  |  Set-JSONPath  -Path  "Type1Array[0].StringSingle"  -Value  "String"  -PassThru  |  Set-JSONPath  -Path  "Type1Array[0].StringArray[0]"  -Value  "String"  -PassThru  |  Set-JSONPath  -Path  "Type1Array[0].IntSingle"  -Value  0  -PassThru  |  Set-JSONPath  -Path  "Type1Array[0].IntArray[0]"  -Value  0  -PassThru  |  Set-JSONPath  -Path  "StringSingle"  -Value  "String"  -PassThru  |  Set-JSONPath  -Path  "StringArray[0]"  -Value  "String"  -PassThru  |  Set-JSONPath  -Path  "IntSingle"  -Value  0  -PassThru  |  Set-JSONPath  -Path  "IntArray[0]"  -Value  0 
```

取上面的代码，只保留与您想要设置的值相匹配的行，在一个数组中复制粘贴更多项的部分(不要忘记首先放置较高的索引)，然后修改值本身，您就有了初始化复合对象的代码。比循环和条件要简单和干净得多，我们不需要知道`Type1Array`是类型`Pester.JSONPath.Type1`。

## 跟踪对象实例，提取 JSONPaths 的值

让我们假设我们像这样初始化一个对象

```
$obj=$obj  |  Set-JSONPath  -Path  "Type1Array[1].Type2Single.StringSingle"  -Value  1  -PassThru  |  Set-JSONPath  -Path  "Type1Array[1].Type2Single.IntSingle"  -Value  1  -PassThru  |  Set-JSONPath  -Path  "Type1Array[1].Type2Single.StringArray[1]"  -Value  2  -PassThru  |  Set-JSONPath  -Path  "Type1Array[1].Type2Single.IntArray[1]"  -Value  2  -PassThru  |  Set-JSONPath  -Path  "Type1Array[1].Type2Array[1].StringSingle"  -Value  1  -PassThru  |  Set-JSONPath  -Path  "Type1Array[1].Type2Array[1].IntSingle"  -Value  1  -PassThru  |  Set-JSONPath  -Path  "Type1Array[1].Type2Array[1].StringArray[1]"  -Value  2  -PassThru  |  Set-JSONPath  -Path  "Type1Array[1].Type2Array[1].IntArray[1]"  -Value  2  -PassThru  |  Set-JSONPath  -Path  "Type1Single.Type2Single.StringSingle"  -Value  1  -PassThru  |  Set-JSONPath  -Path  "Type1Single.Type2Single.IntSingle"  -Value  1  -PassThru  |  Set-JSONPath  -Path  "Type1Single.Type2Single.StringArray[1]"  -Value  2  -PassThru  |  Set-JSONPath  -Path  "Type1Single.Type2Single.IntArray[1]"  -Value  2  -PassThru  |  Set-JSONPath  -Path  "Type1Single.Type2Array[1].StringSingle"  -Value  1  -PassThru  |  Set-JSONPath  -Path  "Type1Single.Type2Array[1].IntSingle"  -Value  1  -PassThru  |  Set-JSONPath  -Path  "Type1Single.Type2Array[1].StringArray[1]"  -Value  2  -PassThru  |  Set-JSONPath  -Path  "Type1Single.Type2Array[1].IntArray[1]"  -Value  2  -PassThru  |  Set-JSONPath  -Path  "StringSingle"  -Value  1  -PassThru  |  Set-JSONPath  -Path  "IntSingle"  -Value  1  -PassThru  |  Set-JSONPath  -Path  "StringArray[1]"  -Value  2  -PassThru  |  Set-JSONPath  -Path  "IntArray[1]"  -Value  2  -PassThru 
```

在我们刚刚初始化的`$obj`上，我们执行`Trace-JSONPath -InputObject $obj`来接收

```
IntArray[0]=0
IntArray[1]=2
IntSingle=1
StringArray[0]=""(null)
StringArray[1]="2"
StringSingle="1"
Type1Array[1].Type2Array[1].IntArray[0]=0
Type1Array[1].Type2Array[1].IntArray[1]=2
Type1Array[1].Type2Array[1].IntSingle=1
Type1Array[1].Type2Array[1].StringArray[0]=""(null)
Type1Array[1].Type2Array[1].StringArray[1]="2"
Type1Array[1].Type2Array[1].StringSingle="1"
Type1Array[1].Type2Single.IntArray[0]=0
Type1Array[1].Type2Single.IntArray[1]=2
Type1Array[1].Type2Single.IntSingle=1
Type1Array[1].Type2Single.StringArray[0]=""(null)
Type1Array[1].Type2Single.StringArray[1]="2"
Type1Array[1].Type2Single.StringSingle="1"
Type1Single.Type2Array[1].IntArray[0]=0
Type1Single.Type2Array[1].IntArray[1]=2
Type1Single.Type2Array[1].IntSingle=1
Type1Single.Type2Array[1].StringArray[0]=""(null)
Type1Single.Type2Array[1].StringArray[1]="2"
Type1Single.Type2Array[1].StringSingle="1"
Type1Single.Type2Single.IntArray[0]=0
Type1Single.Type2Single.IntArray[1]=2
Type1Single.Type2Single.IntSingle=1
Type1Single.Type2Single.StringArray[0]=""(null)
Type1Single.Type2Single.StringArray[1]="2"
Type1Single.Type2Single.StringSingle="1" 
```

在输出中嵌入了以下概念。当路径终止于属性时

*   这是一个原语，当`string`时，它将被包含在引号(`"`)中。
*   那不是本原并且是`null`，那就不显了。
*   这是一个空的或`null`字符串，那么它将像`=""(null)`一样呈现，因为对于 PowerShell 来说，`$null`或空的字符串被认为是相同的。
*   这是一个未定义的 int，那么它仍然会像`=0`一样呈现，因为`0`是默认值。
*   这是一个布尔值，然后它会像`=true`或`=false`一样呈现。

# 为什么得不到操作？

最初，该模块是用一个`Get-JSONPath` cmdlet 创建的，但是当 JSONPath 包含数组时，如何提供有意义的输出这个问题很快浮出水面。一个想法是输出一个带有 JSONPath 和值属性的记录集，类似于`Trace-JSONPath`。

# 未来想法

如果 JSONPaths 变得复杂，那就太好了。例如，当遇到一个数组属性时，我们希望首先使用嵌套的 JSONPath 条件过滤它们，然后继续使用原始的 JSONPath。这就像在数组中指定索引，主动过滤数组，只保留索引引用的数组。这里有一个例子

`$obj|Find-JSONPath -Path "Type1Array[Type2Single.IntSingle -EQ 0].Type2Array[0].IntSingle" -NE 0`

在本例中，我们将过滤所有符合以下条件的对象

*   在`Type1Array`属性中有一个元素，由 JSONPath `Type2Single.IntSingle`解析的相对值将是`0`。
*   如果在`Type1Array`属性中有一个元素，那么由 JSONPath `Type2Array[0].IntSingle`解析的相对值就不会是`0`。