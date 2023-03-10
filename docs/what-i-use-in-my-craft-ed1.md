# 我在工艺中使用的东西

> 原文：<https://dev.to/dsample/what-i-use-in-my-craft-ed1>

本文旨在列出我在软件开发工作流程的各个领域中使用的许多工具，希望它们能帮助他人。当我发现值得分享的新东西时，我会尽量保持更新。

# 硬件

我是一个有点固定和隐藏的怪胎。这些年来，我用过各种各样的笔、笔记本和键盘，现在我决定用这些来满足我的日常需求。

*   [**单球销 0.3mm、0.5mm、0.8mm 细衬套**](https://amzn.to/2Ibz3rM) 。这些使用颜料墨水，这意味着它们是持久的“档案”墨水(它们不会褪色)，并且当使用水基笔在顶部着色时也不会渗色。不同的线条宽度允许创建更详细的草图笔记或图表，而不必多次检查线条以突出它们。
*   [**Tombow 双毛笔**](https://amzn.to/2Kr38FN) **(粉彩)**。这些都很棒，再加上颜料细线条来突出图表或草图的部分。
*   [**罗地亚 A5 线装圆点记事本**](https://amzn.to/2uUK4F8) 。我在听完[的《钢笔上瘾者](https://www.relay.fm/penaddict)播客后，偶然发现了罗地亚记事本。纸张光滑，适用于自来水笔、细线条笔和毡尖笔，添加的点网格允许直写，就像在有线条的纸上一样，也允许用直线绘制图表，就像在方形纸上一样。绑定提供了一个简单的处理临时草图的方法，所以它很适合我讨论架构的视觉方式。
*   [**ErgoDox EZ 键盘**](https://ergodox-ez.com) 。任何与 [QMK 固件](https://qmk.fm/)兼容的机械键盘都将让你有机会向键盘添加层、宏和快捷键，这可以让你更有效率，并且消除不断从键盘切换到鼠标的需要。我使用微软人体工程学键盘已经几十年了，所以不想要非分离式键盘，当我看到 ErgoDox 时，我就被吸引住了。我不能说这种古怪没有增加它的吸引力，但它给人的舒适是巨大的。我现在拥有两个(一个 DIY，一个预建)。

# 软件

我的大部分开发都是在 MacBook Pro (2015 版)上完成的，所以我将专注于 OS X。然而，我使用的许多工具都是跨平台的，所以可以在 Linux 上工作，可能还可以在 Windows 上工作。

*   [**iTerm 2**](https://www.iterm2.com/) 。一个强大的替代 OS X 终端，支持分裂窗格和标签。
*   **ZSH 与** [**哦我的 ZSH**](https://ohmyz.sh/) 。如果你经常使用终端，哦，我的 ZSH 可能会提高你的工作效率。你可以自定义提示的外观，也可以启用插件来自动完成 Git、Docker、Helm 和 Node 等应用程序中的选项。我用主题`kafeitu`。
*   [**Visual Studio 代码**](https://code.visualstudio.com/) 。这些年来，我从使用 TextMate，到 Sublime Text，到 Atom，再到现在的 VSCode。这是 Atom 的一个分支，但尽管我在 Atom 和 VSCode 上都安装了数量惊人的扩展，Atom 对我来说明显变慢了，所以我现在倾向于 VSCode。VSCode 增加的调试 JavaScript 代码的能力使它非常适合我的工作。我将在下面列出一些扩展。
*   [**Vim**](https://www.vim.org/) ，一个基于终端的编辑器。我不再用它来写大部分代码，但是我仍然在工作流程的重要部分使用它，比如快速脚本、删除管理、调试等等。
*   **。屏幕分隔器。有一个开源的替代方案叫做[奇观](https://www.spectacleapp.com/)，但是它没有完全相同的 UX 来设置新的快捷方式。**

 **## Visual Studio 代码扩展

除了 VSCode 的内置功能之外，我还发现许多扩展帮助我提高了工作效率。

*   [**Docker**](https://marketplace.visualstudio.com/itemdetails?itemName=PeterJausovec.vscode-docker) 为 Dockerfiles 提供智能，以及一些可从命令面板访问的有用快捷方式。
*   [**EditorConfig**](https://marketplace.visualstudio.com/itemdetails?itemName=EditorConfig.EditorConfig) ，以及存储库/目录中附带的`.editorconfig`文件，将确保基本格式保持一致。(我当前的基本文件可以在[这里](https://gist.github.com/dsample/8518280e8f2e28af09e4df8908122b40#file-editorconfig)找到，还有一些其他有用的文件，用于建立一致性回购)。
*   [**ESLint**](https://marketplace.visualstudio.com/itemdetails?itemName=dbaeumer.vscode-eslint) 将突出代码 Lint 问题，如果您设置了`"eslint.autoFixOnSave": true`设置，它将在您保存文件时纠正这些琐碎问题，而不需要更漂亮。
*   [**展开区域**](https://marketplace.visualstudio.com/itemdetails?itemName=letrieu.expand-region) ，如果你习惯在 Visual Studio 中控制+W 来选择当前的字、行、函数等。随着每次按下它时范围的增加，这为 VSCode 添加了相同的功能。
*   [**文件图标**](https://marketplace.visualstudio.com/itemdetails?itemName=file-icons.file-icons) 有一些不错的图标，可以显示很多文件扩展名。
*   [**GitHub Pull Requests**](https://marketplace.visualstudio.com/itemdetails?itemName=GitHub.vscode-pull-request-github)提供了一种在 VSCode 中查看 GitHub Pull 请求的方法，这对于内部/开源来说非常方便。
*   [**GitLens**](https://marketplace.visualstudio.com/itemdetails?itemName=eamodio.gitlens) 提供了更多访问 Git 元数据和快捷方式的途径。
*   [**缩进彩虹**](https://marketplace.visualstudio.com/itemdetails?itemName=oderwat.indent-rainbow) 显示每一级缩进的愉快颜色
*   [**Markdown All in One**](https://marketplace.visualstudio.com/itemdetails?itemName=yzhang.markdown-all-in-one) 提供了丰富的编辑 Markdown 文件的工具
*   [**Markdownlint**](https://marketplace.visualstudio.com/itemdetails?itemName=DavidAnson.vscode-markdownlint) 突出显示您的降价文件存在的问题
*   [**路径智能感知**](https://marketplace.visualstudio.com/itemdetails?itemName=christian-kohler.path-intellisense) 帮助自动完成文件的关系路径
*   [**Rainbow CSV**](https://marketplace.visualstudio.com/itemdetails?itemName=mechatroner.rainbow-csv)CSV 文件的颜色代码列有助于 CSV 数据的视觉扫描。
*   **为 YAML 文件编辑提供了有益的支持，包括林挺。**

 **### JavaScript 专用扩展

*   [**CodeMetrics**](https://marketplace.visualstudio.com/itemdetails?itemName=kisstkondoros.vscode-codemetrics) 通过让你知道什么时候你的功能变得有点冗长并且需要重构来帮助你。
*   [**搜集**](https://marketplace.visualstudio.com/itemdetails?itemName=wix.glean) 帮助重构 React 代码
*   [**JS 重构**](https://marketplace.visualstudio.com/itemdetails?itemName=cmstead.jsrefactor) 帮助重构 JavaScript 代码
*   **突出显示您所依赖的软件包的问题**
***   [**NPM 智能感知**](https://marketplace.visualstudio.com/itemdetails?itemName=christian-kohler.npm-intellisense) 通过自动完成包名来帮助导入库*   [**版本镜头**](https://marketplace.visualstudio.com/itemdetails?itemName=pflannery.vscode-versionlens) 显示您的软件包依赖的版本，并帮助更新您对较新版本的引用*   [**网页可访问性**](https://marketplace.visualstudio.com/itemdetails?itemName=MaxvanderSchee.web-accessibility) 突出显示了 HTML/React 标记中可能存在可访问性问题的地方******