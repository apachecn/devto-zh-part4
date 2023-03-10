# Meu 与代码

> 原文：<https://dev.to/claudiovsjunior/meu-vs-code-4e7f>

[![Alt Text](img/8b6261a080db7829c4795bec457d4067.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--oNiuVpaC--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/nvgk882j5d7zojthtm3o.png)

Visual Studio 代码是我的主要代码编辑器，我用它编写我的 Javascript 项目，
研究 Python，编辑配置文件，mockar 文件。基本上适用于涉及
的任何类型的编辑、查看或创建包含字符的文件。

因为他几乎每次都在我的机器上打开，所以学习使用它和定制以满足我的需要所花费的时间都是很投入的时间。

因此，由于我已经进行了这项投资，我将分享我认为最适合我需要的‘t0’设置。

## 全局设置

首先，我使用字体[【FIRA code】](https://github.com/tonsky/FiraCode)，我认为它可读性很好，除此之外，还可以使用
*【fonts ligations】*，这在某些情况下让代码看起来更令人愉快:

```
{  "editor.fontFamily":  "'Fira Code', 'Operator Mono', 'iA Writer Duospace', 'Source Code Pro', Menlo, Monaco, monospace",  "editor.fontLigatures":  true,  "editor.fontSize":  16,  "editor.lineHeight":  23,  "editor.lineNumbers":  "on"  } 
```

您可能已经使用了 minimap，主要是为了帮助您浏览文件，但是，默认情况下，它会尝试呈现文件中的所有字符，由于名称中已经提到的减小了大小，**minimap**，因此这不是必需的-我...。

```
{  "editor.minimap.renderCharacters":  false,  "editor.minimap.maxColumn":  200,  "editor.minimap.showSlider":  "always"  } 
```

导航项目的另一个有用功能是文件夹树。但是，在
在打开的文件夹级别中间迷失了好几次之后，我发现了
的设置，我相信它减少了*文件夹级别 hell* 造成的混乱:

```
{  "workbench.editor.highlightModifiedTabs":  true,  "workbench.tree.indent":  15,  "workbench.tree.renderIndentGuides":  "always"  } 
```

在处理多个文件时，我认为一个非常重要的设置(再次以“
”为目标)是更改窗口的默认标题“
”以显示文件的完整焦点路径，这可能看起来很傻，但有助于:

```
{  "window.title":  "${dirty} ${activeEditorMedium}${separator}${rootName}"  } 
```

因为现在我处理的几乎所有项目都有‘t0’其 NPM 管理的依赖项，而且由于这些依赖项‘t1’通常都有其自己的依赖项，所以我喜欢将‘T2’文件夹留在‘T3’node _ modules’文件夹中

```
{  "search.exclude":  {  "**/node_modules":  true,  "**/env":  true,  "**/venv":  true  },  "files.watcherExclude":  {  "**/.git/objects/**":  true,  "**/.git/subtree-cache/**":  true,  "**/node_modules/**":  true  },  "files.exclude":  {  "**/.git":  true,  "**/.DS_Store":  true,  "**/.vscode":  true,  "**/__pycache__":  true,  "**/.pytest_cache":  true,  "**/node_modules":  true  }  } 
```

## 我认为必不可少的扩展

*   ### [更漂亮](https://github.com/prettier/prettier-vscode)

[![Prettier](img/3ef1b7d56a5bd27fcc996f8a7cea0c7e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--_rHYRJX2--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/prettier/prettier-logo/mastimg/prettier-banner-light.png)

*Prettier* 是编码格式和标准化的重要扩展
。

它允许您使用通常位于项目根目录中的文件“`.prettiierrc`”或“`prettier.json`”来定义全局 VS 代码设置，甚至是特定于项目的“
”设置。

作为一种以 javascript 代码为主的全局设置，
我认为最好留下一些接近:

```
{  "prettier.arrowParens":  "always",  "prettier.eslintIntegration":  true,  "prettier.jsxBracketSameLine":  true,  "prettier.jsxSingleQuote":  true,  "prettier.printWidth":  120,  "prettier.singleQuote":  true,  "prettier.tabWidth":  2,  "prettier.semi":  false,  "prettier.tslintIntegration":  true,  "prettier.trailingComma":  "all"  } 
```

*   ### [代码拼写检查器](https://github.com/streetsidesoftware/vscode-spell-checker)

另一个我认为很有用的延伸，主要是写
句子或文字，是*码拼写检查器*，它不过是你**与**码中直接拼写的校正员
。

> 默认情况下，它仅提供拼法*【美国英语】*，但可以通过安装其他语言的扩展来扩展
> ，例如
> 【巴西葡萄牙语】和*【巴西葡萄牙语代码拼写检查器】*

```
{  "cSpell.language":  "en,pt,pt_BR"  } 
```

*   ### [书签](https://github.com/alefragnani/vscode-bookmarks)

[![Bookmarks](img/10dd8c91a4a783f5cbc6ac349f136825.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--z8Szhcqy--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://github.com/alefragnani/vscode-bookmarks/raw/mastimg/bookmarks-toggle-labeled.gif)

此扩展使我们能够标记同一项目中不同文件中的不同行，并通过“*activity bar*”中的一个新选项提供了一种在创建的不同书签之间导航的极其简便的方法。

当您需要在应用程序代码中的重要点之间导航时，这是一个极好的选项。

*   ### [识别彩虹](https://github.com/oderwat/vscode-indent-rainbow)

[![ident-rainbow](img/6331362f803443506604809727a9d2ae.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--vPpXGbnL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/oderwat/vscode-indent-rainbow/master/assets/example.png)

只有一个插件可以帮助那些已经在钥匙、括号和亲戚的大海中迷路的人，*相同的彩虹*可以帮助您在文件标识中添加颜色。

*   ### [GitLens](https://gitlens.amod.io/)

[![GitLens](img/02260219954b2ce3e65f03a5da12bc8c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--kzj2XmFE--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/eamodio/vscode-gitlens/mastimg/docs/gitlens-logo.png)

*GitLens* 是一种比已经嵌入 VS 代码的 Git 版本更强大的工具。

它使您可以更轻松地浏览文件历史记录，显示上次更改焦点所在行的时间，以及其它一些非常受欢迎的代码版本帮助。

*   ### [吸血鬼官方主题](https://draculatheme.com/visual-studio-code/)

[![Dracula](img/5b65dc84a1a07b54aaecac11c16d3f88.png)](https://camo.githubusercontent.com/0fabc6ce63d1f7b6870311e1d096d07c11f27d63/68747470733a2f2f64726163756c617468656d652e636f6d2f6173736574732f696d672f64726163756c612e676966)

这是我认为每天工作最愉快的话题之一。它带来了非常微妙的暗主题的概念，并且仍然使 VS Code 的所有原生界面项目都非常清晰可见，而其他一些暗主题则不然。