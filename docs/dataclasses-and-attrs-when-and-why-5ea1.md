# 数据类和属性:何时和为什么

> 原文：<https://dev.to/revsys/dataclasses-and-attrs-when-and-why-5ea1>

Python 3.7 引入了[数据类](https://docs.python.org/3/library/dataclasses.html) ( [PEP557](https://www.python.org/dev/peps/pep-0557/) )。Dataclasses 可以方便地生成主要目标是包含值的类。

数据类的设计基于预先存在的`attr.s`库。事实上，Hynek Schlawack，attrs 的同一个作者，帮助了 PEP557 的写作。

基本上，数据类是 attrs 的精简版本。这是否是一种改进取决于您的具体用例。

我认为向标准库中添加数据类会使 attrs 更加相关。我的看法是，一个是另一个的子集，拥有两个选项是一件好事。根据您在特定代码段中想要的正式程度，您可能应该在您的项目中同时使用这两者。

在本文中，我将展示我使用 dataclasses 和 attrs 的方式，为什么我认为你应该同时使用这两者，以及为什么我认为 attrs 仍然非常重要。

### 他们做什么

标准库的数据类和`attrs`库都提供了一种定义我称之为“结构化数据类型”的方法(我会把`namedtuple`、`dict`和`typeddict`放在同一个家族中)

PS:对他们来说可能有更正确的 CS 术语，但我没上过 CS 学校，所以\ *(ツ)* /

它们都是同一个概念的变体:一个表示包含多个值的数据类型的类，每个值都由某种键寻址。

它们还做了一些更有用的事情:它们提供了排序、序列化和良好的字符串表示。但在大多数情况下，最有用的目的是给需要传递的一组值添加一定程度的形式化。

### 举个例子

我认为一个例子可以更好地说明我使用数据类和属性的目的。
假设您想要呈现一个包含表格的模板。您希望确保表格有标题、描述和行:

```
def render_document(title: str, caption: str, data: List[Dict[str, Any]]):
    return template.render({
        "title" : title,
        "caption": caption,
        "data": data,
}) 
```

现在，假设您想要呈现一个文档，该文档由标题、描述、状态(“草稿”、“审阅中”、“已批准”)和一个表列表组成。你如何将表格传递给`render_document`？

您可以选择将每个表表示为一个`dict` :

```
{
    "title": "My Table",
    "caption": "2019 Earnings",
    "data": [
        {"Period": "QT1", "Europe": 500, "USA": 467},
        {"Period": "QT2", "Europe": 345, "USA": 765},
    ]
} 
```

但是，如何表达对`tables`参数的类型注释，使其正确、明确且易于理解呢？

```
def render_document(title: str, description: str, status: str, tables: List[Dict[str, Any]]):
    return template.render({
        "title": title,
        "description": description,
        "status": status,
        "tables": tables,
    }) 
```

那只能让我们描述第一层 if `tables`。它并没有告诉我们一个`Table`有一个标题或题注。相反，你可以使用一个数据类:

```
@dataclass
class Table:
    title: str
    data: List[Dict[str, Any]]
    caption: str = ""

def render_document(title: str, description: str, tables: List[Table]):
    return template.render({
        "title": title,
        "description": description,
        "tables": tables,
    }) 
```

这样我们就有了类型提示，帮助我们的 IDE 帮助我们。

但是我们可以更进一步，在运行时提供类型验证。这就是 dataclasses 停止的地方，attrs 开始发挥作用:

```
@attr.s
class Table(object):
    title: str = attr.ib(validator=attr.validators.instance_of(str))  # don't you pass no bytes!
    data: List[Dict[str, Any]] = attr.ib(validator=...)
    description: str = attr.ib(validator=attr.validators.instance_of(str), default="")

def render_document(title: str, description: str, tables: List[Table]):
    return template.render({
        "title": title,
        "description": description,
        "tables": tables,
    }) 
```

现在，假设我们还需要呈现一个“报告”，这是一个“文档”的集合

```
@dataclass
class Table:
    title: str
    data: List[Dict[str, Any]]
    caption: str = ""

@attr.s
class Document(object):
    status: str = attr.ib(validators=attr.validators.in_(
        ["draft", "in review", "approved"]
    ))
    tables: List[Table] = attr.ib(default=[])

def render_report(self, title: str, documents: List[Document]):
    return template.render({
        "title": title,
        "documents": documents,
    }) 
```

请注意我是如何验证`Document.status`是允许的值之一的。当您在具有使用`choices`的字段的 Django 模型之上构建抽象时，这尤其方便。数据类做不到这一点。

我不断发现自己处于以下几种模式中:

1.  写一个接受一些参数的函数
2.  将一些参数组合成一个`tuple`
3.  嗯，我要字段名-> `namedtuple`。
4.  嗯，我要类型-> `dataclass`。
5.  嗯，我要验证-> `attrs`。

另一种经常发生的情况是:

1.  写一个接受一些参数的函数
2.  添加输入，这样我的 IDE 可以帮助我
3.  哦，顺便说一下，它需要支持那些东西的列表，而不是一次只支持一个！
4.  重构以使用数据类
5.  该参数只能是这些值之一，或者
    1.  我问自己:我如何确保其他开发人员传递正确的类型和/或值？
6.  切换到属性

有时我会在数据类上停下来。很多次我走到楼梯上。

有时，这种情况会发生:

1.  这个遗留代码库的一半使用`-1`作为`False`的特殊值，另一半使用`False`。切换到`attr.s`，这样我就可以使用`converter=`来正常化。

### 比较

这两个库看起来确实非常相似。为了更清楚地了解它们之间的区别，我列出了我最常用的功能:

| 特征 | 数据类 | attrs |
| --- | --- | --- |
| 冰冻的 | -好的 | -好的 |
| 默认 | -好的 | -好的 |
| 推翻 | -好的 | -好的 |
| todict | -好的 | -好的 |
| 验证器 | 一千 | -好的 |
| 转换器 | 一千 | -好的 |
| 开槽类 | 一千 | -好的 |

如你所见，有很多重叠。但是`attrs`上的附加特性提供了我经常需要的功能。

### 何时使用数据类

数据类只是数据的“形状”。
选择数据类，如果:

*   您不关心字段中的值，只关心它们的类型
*   添加依赖项并不简单

### 何时使用属性

attrs 是关于形状*和*的值。
选择属性，如果:

*   您想要验证值。常见的情况相当于选择字段。
*   您希望规范化或净化输入
*   当您想要比数据类本身所能提供的更多的形式化时
*   你关心的是记忆和表现。`attrs`可以创建[槽类](https://www.attrs.org/en/stable/examples.html#slots)，由 CPython 优化[。](http://threeofwands.com/attrs-ii-slots/)

我经常发现自己在使用数据类，后来因为需求改变或者我发现我需要防范某些特定的值而切换到 attr.s。我认为这是开发软件的一个正常方面，我称之为“持续重构”。

### 我为什么喜欢数据类

我很高兴标准库中增加了数据类，我认为这是一个有益的增加。这是一件非常方便的事情，你可以随时使用。

首先，它将从一开始就鼓励更结构化的编程风格。

但我认为最有说服力的案例是一个实际案例。一些高风险的企业环境(例如:金融机构)要求对每个包进行审查(有充分的理由:我们已经在库中发生过恶意代码事件)。这意味着添加 attrs 并不像在您的`requirements.txt`中添加一行那么简单，还需要等待您的医护兵团队的批准。这些开发人员可以立即使用数据类，他们的代码将立即受益于使用更正式的数据类型。

### 我为什么喜欢 attrs

大多数人不会在如此严格控制的环境中工作。

当然，有时候你不需要 attrs 的所有功能，但是拥有它们并没有坏处。

随着我越来越多地形式化我的代码的 API，我经常最终需要它们。Dataclasses 只完成了我想做的一半。

## 结论

我认为数据类只包含了 attrs 所能提供的一个子集。诚然，它是一个*大的*子集。但是没有包括的特性足够重要，需要的次数足够多，以至于它们使得 attrs 不仅仍然相关和有用，而且是必要的。

在我看来，使用这两者可以让开发人员逐步重构他们的代码，随着应用程序的需求随着时间的推移而稳定，将函数之间的契约从松散定义的参数一直移动到正式描述的数据结构。

拥有数据类的一个好处是，现在开发人员更有动力重构他们的代码，使之更加形式化。在某种程度上，dataclasses 是不够的，这时开发人员将进行重构以使用 attrs。这样，dataclasses 实际上充当了 attrs 的*介绍*。如果 attrs 因为 dataclasses 而变得*更加*受欢迎，我不会感到惊讶。

## 参考文献

*   数据类文件 + [PEP557](https://www.python.org/dev/peps/pep-0557/)
*   [关于属性的文档](https://www.attrs.org/en/stable/index.html)
*   [“为什么不是数据类”在属性文档上](https://www.attrs.org/en/stable/why.html#data-classes)

## 致谢+感谢

我要感谢以下人员修改草案并提供意见和见解:

*   希内克·施莱茨克
*   雅各布·卡普兰-莫斯
*   雅各布·伯奇
*   杰夫·特里普利特