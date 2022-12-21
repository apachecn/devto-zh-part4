# Sitecore 中的回退和强制版本

> 原文：<https://dev.to/kmac23va/fallback-and-enforcing-version-in-sitecore-5ga2>

我今天在 Sitecore 中尝试获得正确的版本时遇到了一个问题，并在 Sitecore StackExchange 上获得了一个答案:

<header>![](img/70920991d6111d21097e8fcc58a16302.png) [Glass Mapper - Item not null when language version not present](https://sitecore.stackexchange.com/questions/18733/glass-mapper-item-not-null-when-language-version-not-present) May 16 '19 Comments: 3 Answers: 1[![](img/83f13d376e6608cc602ae93b1cdbae4e.png)1![](img/fd423aaf5fec73c645f97544689ea934.png)](https://sitecore.stackexchange.com/questions/18733/glass-mapper-item-not-null-when-language-version-not-present) </header>

我用的是 Glass 5.4.18 搭配 Sitecore 9.0.2。我的网站有一个默认打开语言回退的基本模板，但对于一些项目，客户希望禁用语言回退，因此项目不会出现在某些语言中。

我已经进入了单个项目…

[Open Full Question](https://sitecore.stackexchange.com/questions/18733/glass-mapper-item-not-null-when-language-version-not-present)

我想我应该稍微扩展一下设置，并进入我的基本模板的默认排列。Sitecore 中有两种回退方式，项目和字段，主要应用于多语言网站设置。

*   **项目回退**意味着如果您没有特定语言的版本，它将回退并使用指定回退语言的版本(在`/sitecore/system/Languages`部分设置)。
*   **字段回退**意味着当您创建新的语言版本时，您将看到指定回退语言的内容，而不是空白字段。

打开项目回退很容易，只需创建一个基础项目模板来继承并选中*启用项目回退*选项。在全局范围内打开字段回退有点困难；您可以转到基本字段项目的标准值(`/sitecore/templates/System/Templates/Template field/__Standard Values`)并检查*启用共享语言回退*框，但有一个错误没有该设置传播到线上并正常工作。Sitecore 已经提供了一个补丁 DLL 和配置，他们说可以在 9.0.2 中使用，所以他们可能需要针对其他版本进行调整。请注意，从 9.2 版本开始，Sitecore 中仍未应用该补丁。内核文件，但是您可以在我的 **ScHelix** GitHub 项目中找到它:

## ![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) [ kmac23va ](https://github.com/kmac23va) / [谢里克斯](https://github.com/kmac23va/ScHelix)

### 基线 Sitecore Helix 基础和基于 HelixCore 的发布层

<article class="markdown-body entry-content container-lg" itemprop="text">

## 欢迎来到 GitHub 页面

您可以使用 GitHub 上的[编辑器来维护和预览 Markdown 文件中的网站内容。](https://github.com/kmac23va/ScHelix/edit/master/README.md)

每当你提交到这个库，GitHub Pages 就会运行 [Jekyll](https://jekyllrb.com/) 来从你的 Markdown 文件中的内容重建你站点中的页面。

### 降价

Markdown 是一种轻量级且易于使用的语法，用于设计您的作品。它包括以下约定

```
Syntax highlighted code block

#  Header 1
##  Header 2
###  Header 3

- Bulleted
- List

1. Numbered
2. List

**Bold** and _Italic_ and `Code` text

[Link](url) and ![Image](src)
```

Enter fullscreen mode Exit fullscreen mode

更多详情见 [GitHub 风味降价](https://guides.github.com/features/mastering-markdown/)。

### 哲基尔主题

您的 Pages 站点将使用您在[存储库设置](https://github.com/kmac23va/ScHelix/settings)中选择的 Jekyll 主题的布局和样式。这个主题的名称保存在 Jekyll `_config.yml`配置文件中。

### 支持或联系

页面有问题吗？检查…

</article>

[View on GitHub](https://github.com/kmac23va/ScHelix)
You'll need data from these specific files:

*   [isvalidforlanguagefallback . cs](https://github.com/kmac23va/ScHelix/blob/master/src/Foundation/HelixCore/code/Pipelines/GetFieldValue/IsValidForLanguageFallback.cs)
*   [管道.配置](https://github.com/kmac23va/ScHelix/blob/master/src/Foundation/HelixCore/code/App_Config/Include/Foundation/HelixCore/Pipelines.config)

除了项目设置之外，您还需要向站点定义条目添加一些设置。这包括修补**外壳**站点定义，并将设置添加到其他站点定义:

```
enableItemLanguageFallback="true" enableFieldLanguageFallback="true" 
```

Enter fullscreen mode Exit fullscreen mode

注意，如果您不将这些设置添加到 **shell** 站点定义中，您将不会在编辑器中看到正确的结果。

然后是强制版本存在。这适用于未打开回退的情况，但即使您打开了回退，它也是在您可能对特定项目禁用回退的情况下的良好备份。默认情况下，如果回退或强制版本存在都没有打开，当您通过 Sitecore API(或 Glass Mapper，我的首选)获得一个项目时，您将获得该项目的数据，尽管版本计数将为零。如果您打开强制版本存在，您将得到一个空结果，这是更可取的，以便在您循环通过一组项目的场景中，不会无意中出现由于缺少版本而不属于的项目。(这是我之前遇到的场景，遍历导航树的项目。)

打开版本存在实施就像项目回退一样简单；它是基础模板标准值中*启用项目回退*复选框正下方的*强制版本存在*复选框。此外，您需要将以下设置添加到您的站点定义中，但*不是*的**外壳**站点定义(如果您在**外壳**中设置它，您将只能在项目语言选择中看到您拥有版本的语言，而不是帮助创建新版本的可用语言):

```
enforceVersionPresence="true" 
```

Enter fullscreen mode Exit fullscreen mode

即使你不打算做一个多语言网站，我也喜欢在一开始就激活所有这些选项，和/或把它包含在一个基础螺旋基础层项目中。我不相信会有任何有害的后果，如果客户决定要多语言内容，它会让你做好充分准备；您所要做的就是创建语言选项并设置备用语言，然后您就可以开始了！