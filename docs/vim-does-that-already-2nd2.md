# Vim 已经这样做了

> 原文：<https://dev.to/jamestthompson3/vim-does-that-already-2nd2>

## 生产力的力量

Vim 是一个强大的工具，它可以帮助您保持高生产率，并减少花在编码这种机械工作上的时间。对许多人来说，Vim 似乎过时了，功能不足，甚至令人生畏。我希望说明 Vim 不是这些东西中的任何一个，事实上它是几乎所有开发环境的优秀编辑器。

我想把重点放在三个常见的任务上，Vim 可以帮助您在不离开编辑器的情况下提高工作效率。这些任务是:文件之间的导航和搜索/替换。这篇文章不需要 Vim 经验，但是如果你至少通过`vimtutor`熟悉了基础知识，那肯定会有帮助。

## 文件间导航

作为开发人员，我们每天最常见和最耗时的活动之一就是在文件之间跳转。如果您使用的是更传统的文本编辑器，如 VSCode、Sublime Text 或 Atom，那么您可能会花费更多的时间来浏览目录树，试图找到正确的文件。使用 Vim，文件导航的秘密从`set path`开始。

`path`是 Vim 在执行各种搜索命令时搜索文件的地方。在类似 Unix 的系统上，默认情况下是`.,/usr/include,,`。让我们把它分解一下。第一个`.`表示 Vim 应该包含与当前文件目录相关的文件。应该搜索的目录用逗号分隔，所以 Vim 默认要搜索的下一个地方是`/usr/include/`目录。该目录通常包含头文件，因此如果您正在进行 C 和 C++编程，它会很有用。`,,`的最后一个序列指示 Vim 在当前目录中搜索。开箱即用，我们能够搜索我们项目的大部分。如果我们想在我们的项目中向下递归搜索呢？在项目根目录(通常由 vcs 文件表示，如`.git`文件夹)中打开文本编辑器是很常见的。为了确保当我们搜索项目文件时，Vim 能够找到它们，我们使用了`*`和`**`。星号代表通配符，`*`匹配 0 个或更多字符，`**`仅匹配目录。通过将您的路径设置为`set path=.,,, **`，您可以确保 Vim 将搜索我们所有的项目文件。需要注意的是，默认情况下`**`只搜索 30 个目录深度，所以如果你有一个非常嵌套的目录结构，你可以增加这个限制，更多细节见`:h starstar`。现在我们已经正确设置了路径，是时候导航了！Vim 有一个内置的 keybinding，用于跳转到光标下的文件，它是`gf`。我们通过使用`set suffixesadd`告诉 Vim 应该添加哪些文件结尾。因此，对于前端 web 开发，您的`vimrc`中可能有类似于`au! BufNewFile,BufRead *.js,*.jsx suffixesadd+=.js,.jsx`的内容。这允许我们跳转到带有结尾`.js and .jsx`的文件。

实际上看起来是这样的:

```
import { someFunction } from "./utils"; 
```

在正常模式下，将光标放在`./utils`上并按下`gf`会将你带到`utils.js`文件！

这只是 Vim 在搜索您的代码时所能做的事情的冰山一角，要更深入地了解这一点，我推荐这篇博客文章， [death by a 千 files](https://vimways.org/2018/death-by-a-thousand-files/) 。我想展示一个更有用的命令，它受益于将我们的`path`设置为向下递归搜索，那就是`find`。使用`find`，Vim 将搜索一个文件，如果找到，就打开它进行编辑。如果我们打开了`wildmenu`设置，我们也可以使用`*`通配符扩展和制表符结束。

例如，键入`:find N*C*.jsx`并按 tab 键会展开到`NavigationContainer.jsx`。您也可以键入`Navigation`并按 tab 键来展开 Vim 在当前路径中找到的所有选项。就像这样，你可以模糊搜索你的项目，而不需要任何额外的配置或插件。有时候，项目中有一些我们想忽略的目录，比如`node_modules`、`target`和`dist`。Vim 通过`wildignore`设置决定忽略哪些文件。一个样本`wildignore`可能是这样的:`set wildignore=*/dist*/*,*/target/*,*/builds/*,*/node_modules/*`。Vim 将不再在其路径中包含这些目录。

## 查找并替换

默认情况下，Vim 附带了`vimgrep`，它允许您在文件中搜索字符串。Vim 还附带了`grep`命令，允许您使用外部 grep 工具进行搜索。我个人使用的是 [ripgrep](https://github.com/BurntSushi/ripgrep) ，一个专注于速度的 grep 工具，默认情况下尊重我的`.gitignore`文件。

要使 Vim 能够使用 ripgrep(或任何其他 grep 程序)作为`grep`命令的后端，您需要在您的 vimrc:
中设置 grep 程序

```
set grepprg=rg --smart-case --vimgrep 
```

现在您已经将搜索结果加载到 quickfix 菜单中，并且可以利用 Vim 内置插件`cfilter`。要添加它，只需运行`:packadd cfilter`，Vim 就会加载插件供使用。`cfilter`通过正则表达式过滤快速修复列表中的条目。

如果你有类似这样的搜索结果:

```
datastructures.html|623| 30: mixpanel.track("data structures loaded");
index.html|61| 26: <a href="./datastructures.html"
index.html|62| 19: >Data Structure Memes For Edgy JavaScript Teens</a
_site/index.js|64| 28: import { customStructure } from './datstructures'
_site/utils.js|64| 28: import { customStructure } from 'custom/datstructures' 
```

如果您想只保留 JavaScript 文件的搜索结果，您可以运行这个命令:`:Cfilter! html`。这将保留 quickfix 列表中除包含`html`的匹配之外的所有内容。如果你只想保留第三个匹配，你运行`:Cfilter Memes`，它只保留包含`Memes`的匹配。

使用 quickfix list 的另一个有用的命令是`:cfdo`。它允许您在整个 quickfix 列表中执行您给它的任何命令。例如，如果您想要搜索和替换，您可以这样做:

搜索短语“数据结构”:

`:grep! 'data structures'`

查看结果:

```
datastructures.html|279| 15: Data Structures
datastructures.html|574| 26: with these data structures, since the structure of our data is
datastructures.html|575| 43: irrelevant to react, custom data structures should be simple to
datastructures.html|623| 25: mixpanel.track("data structures loaded"); 
```

将“结构”改为单数“结构”并保存每个文件:

`:cfdo s/ures/ure | update`

最后一个命令的`| update`部分告诉 Vim 在执行替换后保存更改。如果您想在保存前检查您的更改，您可以不做这一部分。如果选择此选项，您仍然需要手动保存每个文件。

## Grep 提示

Vim 使得在大型代码库中查找和替换代码变得快速而简单。为了让它变得更快更容易，这里有一些东西给你。

```
set grepprg=rg\ --smart-case\ --vimgrep " set grep program to ripgrep with smartcase flag. You can set it to anything you like.

" creates a :SearchProject command, makes it so you don't have to escape strings in ripgrep, will tab compete with directories in path.
command! -nargs=+ -complete=dir -bar SearchProject execute 'silent! grep!'.<q-args>.' | cwindow' 
```

我希望这篇文章有助于改善你的 Vim 体验！