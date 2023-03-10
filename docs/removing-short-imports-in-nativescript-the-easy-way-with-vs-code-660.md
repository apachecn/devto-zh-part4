# 移除 NativeScript 中的短导入，简单的方法(使用 VS 代码)

> 原文：<https://dev.to/toddanglin/removing-short-imports-in-nativescript-the-easy-way-with-vs-code-660>

随着 NativeScript 的不断发展，一些在 2015 年有意义的功能在 2019 年带来了更多的麻烦。一个这样的特征是所谓的“短导入”

短导入旨在通过节省一些击键次数来改善开发人员的体验。与其这样:

```
import { device } from 'tns-core-modules/platform' 
```

开发人员可以简单地使用:

```
import { device } from 'platform' 
```

这很方便，但却产生了意想不到的副作用。有了像 VS 代码这样可以自动添加导入的工具，这个特性的价值就降低了。

所以，现在它被弃用了(从{N} 5.2 开始)。[在 NativeScript 博客上阅读所有相关内容](https://www.nativescript.org/blog/say-goodbye-to-short-imports-in-nativescript)。

## 现在怎么样了？

虽然短导入仍然有效，但当 webpack 成为默认和唯一可用的{N}构建系统时，它们将成为 NativeScript 6.0 的真正问题。Webpack 需要完整的导入路径，因此短时间的导入很快会导致 webpack 出现问题。

但是如果你正在维护一个已经存在了一段时间的应用程序，删除简短的导入可能是一件相当麻烦的事情。在我的例子中，我更新的应用程序有超过 275 个短导入需要修复！

我开始手动修复导入，然后*很快*意识到这是个错误。

要是有一种方法可以自动修复所有这些导入就好了...

## VS 代码(和正则表达式)来救援

大家都知道 [VS Code 有很棒的搜索和替换工具](https://code.visualstudio.com/docs/editor/codebasics?WT.mc_id=devto-blog-toanglin#_search-across-files)，但可能不那么明显的是，你可以在搜索*和* replace 中使用正则表达式。只需打开“使用正则表达式”并在搜索中使用标准 JavaScript regex，然后使用`$1`、`$2`等在替换中包含匹配组。

*注意:要用反向引用和 lookaround 运行真正高级的正则表达式搜索(就像我们今天要做的),确实需要改变 VS 代码中的设置。进入设置>功能>搜索>使用 PCRE2 并勾选复选框以启用这些额外功能。[参见 VS 代码文档了解更多内容](https://code.visualstudio.com/docs/editor/codebasics?WT.mc_id=devto-blog-toanglin#_search-across-files)。*

我知道，我知道。如果你有一个问题，并使用正则表达式来解决它，现在你有两个问题。但它胜过乏味的选择。

### 正则表达式查找短导入

为这项任务构建正确的正则表达式的技巧是，尝试为那些*应该*具有`tns-core-modules`前缀的内容找到短导入，同时*避免为“非核心”模块和已经具有正确长格式的导入找到*导入。

幸运的是，大多数 NativeScript 模块都遵循类似的约定:

*   核心模块使用不带前缀的“普通”名称(如`application`或`ui/layout`)
*   非核心模块通常在模块名前面加上`nativescript-`或`ns-`
*   路径引用的本地模块通常以`./`或`../`或`~/`开头

例如，我们希望找到如下所示的导入:

*   `import * as imageSource from "image-source";`
*   `import * as util from "utils/utils";`
*   `import { Image } from "ui/image";`

同时避免类似这样的导入:

*   `import { Settings } from "../models/app-models";`
*   `import * as firebase from "nativescript-plugin-firebase";`
*   `import { Color } from "tns-core-modules/color/color";`

然后，当我们有一个匹配时，我们希望在导入路径前面插入“`tns-core-modules/`”，同时保持该行的其余部分不变。

把这些放在一起，我们得到这个搜索正则表达式:

```
(from \")(?!nativescript)(?!ns)(?!tns-core-modules)((?:[\w\-\/])+)(\") 
```

由于 regex 看起来总是像一只猫走过你的键盘，让我们用英语解释一下:

*   通过搜索`from "`(包括报价)开始查找进口商品(并存储在匹配组 1 中)...
*   后跟任意数量的单词字符或`-`(破折号)或`/`(重击)(并存储在匹配组 2 中)...
*   但是忽略匹配组是否包含单词`nativescript`或`ns`或`tns-core-modules`

    *   这些可能是非核心模块或导入，它们已经具有正确的长格式
*   最后，匹配结尾的`"`(引用)

这个组合非常准确地找到了我们想要的导入，而忽略了其他所有东西。您可以在您的浏览器中使用 Regexr 来[使用这个 regex 的工作版本，试验它匹配和错过了什么。](https://regexr.com/4hj93)

当在 VS 代码中运行搜索时，为了避免误报，您显然还想排除不属于您代码的文件和文件夹，比如`node_modules`、`platforms`、`*.js`(假设您使用的是 TypeScript)、`.git`。

### 替换语法

当搜索找到此模式的匹配项时，它将使用这些值创建三个匹配组:

1.  `from "`
2.  `[name of matched module]`
3.  `"`

例如，当匹配以下行时:

```
import { Image } from "ui/image"; 
```

匹配组将包含:

1.  `from "`
2.  `ui/image`
3.  `"`

我们想在组 1 和组 2 之间插入`tns-core-modules/`。使用 VS 代码，我们的替换语法是:

```
$1tns-core-modules/$2$3 
```

嘣！任务完成。几乎...

## 另一个搜索和替换通道

如果使用普通的 JavaScript `require()`语法来导入模块，显然需要不同的搜索模式。即使在我的基于 TypeScript 的应用程序中，由于特定于模块实现的原因，我也用`require()`完成了一些导入。

从概念上讲，我们需要的搜索正则表达式类似于上面的:

```
(require\(\")(globals)+(\"\)) 
```

这种模式匹配:

1.  `require("`
2.  `globals`
3.  `")`

如果需要更广泛的搜索，您可以修改“globals”模式。在我的例子中，这是我的应用程序中唯一需要前缀`tns-core-modules`的`require()`。

替换模式与之前相同:

```
$1tns-core-modules/$2$3 
```

## 总结起来

希望你在这篇文章中学到了两件事:

1.  消除 NativeScript 应用程序中的简短导入不一定是乏味的手动过程
2.  当与正则表达式和匹配组结合使用时，VS 代码搜索和替换功能非常强大