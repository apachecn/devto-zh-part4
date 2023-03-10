# 我的 VS 代码设置——充分利用 VS 代码

> 原文：<https://dev.to/deepu105/my-vs-code-setup-making-the-most-out-of-vs-code-4enl>

*最初发布于[deepu . tech](https://deepu.tech/make-the-most-out-of-vscode/)T3。*

[Visual Studio 代码](https://code.visualstudio.com/)(我更喜欢 VS 代码的声音)，我就是喜欢。它是我的主要代码编辑器。

比起 ide，我一直更喜欢轻量级编辑器。很多年前，我用 Eclipse 进行开发，用 [Notepad++](https://notepad-plus-plus.org/) 和一些插件开发其他轻量级的东西。然后我发现了 sublime text 并使用了一段时间。当我做 web 开发的时候，我仍然觉得 Eclipse 太重了。然后是来自土坯的[支架](http://brackets.io/)。这是一个相当不错的编辑器，特别是对于 web 开发，我开始大量使用它进行 web 开发。但是在大型代码库上，括号有点慢。然后是 [Atom](https://atom.io/) ，它引入了 Atom shell，最终变成了 [Electron](https://electronjs.org/) ，彻底改变了 NodeJS 桌面应用程序的面貌。所以我转而使用 Atom，喜欢它光滑的界面和漂亮的可插拔特性。它成了我所有网络开发的主要编辑器。

因此，Electron 为 VS 代码铺平了道路，虽然起初，由于与 Visual Studio 的关联，我对此表示怀疑，但我尝试了一下，并对其速度和用户体验感到惊讶。现在已经没有回头路了。我慢慢地开始在日常开发中使用 VS 代码，除了 JVM 开发，我现在用的是 IntelliJ Ultimate，在 JVM 开发中很难打败 IntelliJ。下面是我用于开发的编辑器/IDE。

*   **VS 代码** : JavaScript、TypeScript、EJS、HTML、CSS、Golang、Rust、Python、Ruby、Shell、Docker、Kubernetes、Terraform 以及中间的一切，包括写这篇博文。
*   IntelliJ Idea : Java，Scala，Kotlin，Groovy
*   **VIM** :用于从命令行进行快速编辑。

# 插件

当然 VS Code 通过允许使用插件使这一切成为可能，并且有很多选择。以下是我个人用来处理上述语言的插件。您可以使用`code --install-extension`命令从终端安装它们。

## 语言支持

基于你使用的语言，你可以添加语法、工具和语言支持插件。我用下面的

### JavaScript/type script/Web

*   [EJS 语言支持](https://marketplace.visualstudio.com/items?itemName=DigitalBrainstem.javascript-ejs-support) -增加 [EJS](https://ejs.co/) 模板支持。> `code --install-extension DigitalBrainstem.javascript-ejs-support`
*   [关闭 HTML/XML 标签](https://marketplace.visualstudio.com/items?itemName=Compulim.compulim-vscode-closetag) -自动关闭 HTML/XML 标签。> `code --install-extension Compulim.compulim-vscode-closetag`
*   [ESLint](https://marketplace.visualstudio.com/items?itemName=dbaeumer.vscode-eslint) -增加对 [ESLint](https://eslint.org/) 规则的支持。> `code --install-extension dbaeumer.vscode-eslint`
*   [TSLint](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-typescript-tslint-plugin) -增加对 [TSLint](https://github.com/palantir/tslint) 规则的支持。> `code --install-extension ms-vscode.vscode-typescript-tslint-plugin`
*   [更漂亮的](https://marketplace.visualstudio.com/items?itemName=esbenp.prettier-vscode) -增加对[更漂亮的](https://github.com/prettier/prettier)格式化程序的支持。> `code --install-extension esbenp.prettier-vscode`
*   [es-beautiier](https://marketplace.visualstudio.com/items?itemName=dai-shi.vscode-es-beautifier)-根据 Eslint 规则格式化 JS。> `code --install-extension dai-shi.vscode-es-beautifier`

### 去吧

*   [Go](https://marketplace.visualstudio.com/items?itemName=ms-vscode.Go) -为 Golang 增加了丰富的语言支持。> `code --install-extension ms-vscode.Go`

### JVM

*   [Java 语言支持](https://marketplace.visualstudio.com/items?itemName=redhat.java) -增加 Java 语言支持。> `code --install-extension redhat.java`
*   [Java 调试器](https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-debug)——增加轻量级 Java 调试支持。> `code --install-extension vscjava.vscode-java-debug`
*   吉普斯特 JDL -增加对吉普斯特 JDL 文件的语法支持。> `code --install-extension jhipster-ide.jdl`

Java 支持确实越来越好，所以我希望有一天我可以完全转到 VS 代码。

液体错误:内部

### 巨蟒

*   [对 Python 的语言支持](https://marketplace.visualstudio.com/items?itemName=ms-python.python)——增加了 Python 语言支持、林挺和调试支持。> `code --install-extension ms-python.python`

### 云，容器&其他

[![](img/4ac879eae6047dfe92b40141436db60c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--hrGMnMi2--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/64sf9ht3f35lsipzafmu.png)

*   添加 Docker 支持(查看和管理容器)和 Docker 支持，Docker-编写文件。

> `code --install-extension ms-azuretools.vscode-docker`

*   Jenkinsfile 支持 -增加对 Jenkinsfile 的语法高亮支持。

> `code --install-extension secanis.jenkinsfile-support`

*   增加对地形文件的支持。

> `code --install-extension mauve.terraform`

*   [Markdown all in one](https://marketplace.visualstudio.com/items?itemName=yzhang.markdown-all-in-one) -全面的 Markdown 支持，包括实时预览、键盘快捷键等。

> `code --install-extension yzhang.markdown-all-in-one`

*   [PlantUML](https://marketplace.visualstudio.com/items?itemName=jebbs.plantuml) -丰富的 [PlantUML](http://plantuml.com/) 支持实时预览。

> `code --install-extension jebbs.plantuml`

*   [Visual Studio IntelliCode](https://marketplace.visualstudio.com/items?itemName=VisualStudioExptTeam.vscodeintellicode)-增加了对多种语言的人工智能辅助智能感知支持。

> `code --install-extension VisualStudioExptTeam.vscodeintellicode`

*   YAML -增加 YAML 的支持。> `code --install-extension redhat.vscode-yaml`

## 主题

[![](img/bdde835d8983285650632fe720e47671.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--QoSADcDb--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/8xsddvbrq5e1mco28vsc.png)

#### [深色++斜体](https://marketplace.visualstudio.com/items?itemName=idbartosz.darkpp-italic)

我的默认主题。类似于 VS 代码默认的深色主题，但是支持 [FiraCode](https://github.com/tonsky/FiraCode) 和[操作符单色](https://www.typography.com/fonts/operator/styles)字体。我个人用 FiraCode。

> `code --install-extension idbartosz.darkpp-italic`

#### [素材图标主题](https://marketplace.visualstudio.com/items?itemName=PKief.material-icon-theme)

一个基于材质图标的漂亮图标主题。

> `code --install-extension PKief.material-icon-theme`

#### [孔雀](https://marketplace.visualstudio.com/items?itemName=johnpapa.vscode-peacock)

微妙地改变你的工作空间的颜色。有助于识别何时有多个窗口打开。

> `code --install-extension johnpapa.vscode-peacock`

## 工具

#### [自动重命名标签](https://marketplace.visualstudio.com/items?itemName=formulahendry.auto-rename-tag)

自动重命名成对的 HTML/XML 标签

> `code --install-extension formulahendry.auto-rename-tag`

#### [括号对上色器 2](https://marketplace.visualstudio.com/items?itemName=CoenraadS.bracket-pair-colorizer-2)

用独特的颜色标记匹配的括号对。这确实让阅读代码变得更好

> `code --install-extension CoenraadS.bracket-pair-colorizer-2`

#### [变化情况](https://marketplace.visualstudio.com/items?itemName=wmaurer.change-case)

在不同的大小写之间转换。相信我，这很方便

> `code --install-extension wmaurer.change-case`

#### [代码拼写检查器](https://marketplace.visualstudio.com/items?itemName=streetsidesoftware.code-spell-checker)

对于代码中的拼写检查非常有用。将珠宝等考虑在内

> `code --install-extension streetsidesoftware.code-spell-checker`

#### [轻松片段生成器](https://marketplace.visualstudio.com/items?itemName=tariky.easy-snippet-maker)

用于存储可重复使用的代码片段。

> `code --install-extension tariky.easy-snippet-maker`

#### [VS 代码的编辑配置](https://marketplace.visualstudio.com/items?itemName=EditorConfig.EditorConfig)

添加对 [EditorConfig](https://editorconfig.org/) 的支持。

> `code --install-extension EditorConfig.EditorConfig`

#### [饭桶历史](https://marketplace.visualstudio.com/items?itemName=donjayamanne.githistory)

允许在 VS 代码中查看 Git 历史。

> `code --install-extension donjayamanne.githistory`

#### [Gitignore](https://marketplace.visualstudio.com/items?itemName=codezombiech.gitignore)

使处理`.gitignore`文件变得容易。

> `code --install-extension codezombiech.gitignore`

#### [隐藏 git 忽略](https://marketplace.visualstudio.com/items?itemName=npxms.hide-gitignored)

从编辑器浏览器中隐藏在`.gitignore`中定义的模式。

> `code --install-extension npxms.hide-gitignored`

#### [标记为被排除](https://marketplace.visualstudio.com/items?itemName=jcmordan.mark-as-excluded)

从资源管理器树中排除一些东西。

> `code --install-extension jcmordan.mark-as-excluded`

#### [切换排除的文件](https://marketplace.visualstudio.com/items?itemName=eamodio.toggle-excluded-files)

轻松切换显示和隐藏排除的文件/文件夹。

> `code --install-extension eamodio.toggle-excluded-files`

#### [IntelliJ IDEA 按键绑定](https://marketplace.visualstudio.com/items?itemName=k--kato.intellij-idea-keybindings)

我的肌肉记忆不好，所以想使用和 IntelliJ 一样的键盘快捷键。有可用于 Sublime、Atom 等的映射。

> `code --install-extension k--kato.intellij-idea-keybindings`

#### [排序 JSON](https://marketplace.visualstudio.com/items?itemName=richie5um2.vscode-sort-json)

对 JSON 对象键进行排序。

> `code --install-extension richie5um2.vscode-sort-json`

#### [测试浏览器 UI](https://marketplace.visualstudio.com/items?itemName=hbenl.vscode-test-explorer)

添加用于运行测试的资源管理器面板。支持多种语言和测试框架。

> `code --install-extension hbenl.vscode-test-explorer`

#### [待办事宜树](https://marketplace.visualstudio.com/items?itemName=Gruntfuggly.todo-tree)

在资源管理器的树视图中聚集 TODO、FIXME 等。

> `code --install-extension Gruntfuggly.todo-tree`

# 终端设置

如果你像我一样用 Oh-my-zsh 使用 Zsh shell，如这里的[所解释的](https://deepu.tech/configure-a-beautiful-terminal-on-unix/)，你可能想要在集成的 VSCode 终端中获得相同的终端体验。

[![](img/8c836fbef35574819dd1b29e6f8c82a1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--LRNOzrcj--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ae6wi9anly8clq12z9na.png)

遵循以下步骤

*   下载并安装一个[补丁字体](https://github.com/ryanoasis/nerd-fonts/blob/master/patched-fonts/SourceCodePro/Medium/complete/Sauce%20Code%20Pro%20Medium%20Nerd%20Font%20Complete%20Mono.ttf)。
*   在 Linux 上，运行`fc-cache -f -v`来刷新字体缓存。
*   在 VSCode 上，打开 Preferences → Settings，点击`{}`图标，打开 JSON 模式，设置如下

```
 "terminal.integrated.shell.linux":  "/usr/bin/zsh",  "terminal.integrated.fontFamily":  "'SauceCodePro Nerd Font Mono','Source Code Pro'",  "terminal.integrated.rightClickCopyPaste":  true,  "terminal.integrated.fontSize":  14,  "terminal.integrated.cursorStyle":  "underline",  "terminal.integrated.cursorBlinking":  true 
```

Enter fullscreen mode Exit fullscreen mode

如果你在苹果电脑上，用`osx`代替`linux`。

* * *

# 结论

这看起来像是太多插件了，但是在[上，我的配置](https://deepu.tech/my-beautiful-linux-development-environment) VS 代码快得像闪电一样，可以立即加载，比 IntelliJ 加载和工作都要快。VS 代码的美妙之处在于，你不需要一直使用所有的插件，你可以禁用每个工作空间不需要的插件，这样会更快。

许多人[问](https://www.reddit.com/r/linux/comments/c42kpk/my_beautiful_linux_development_environment/erum9sf/)我为什么在有 IntelliJ 的情况下使用 VS 代码，我的回答总是一样的。IntelliJ 很棒，但也很重。虽然 Java、Scala 或 Kotlin 开发需要所有这些高级特性，但 VS Code 完全能够为 JS、TS、Go、Python、Rust、Ruby 等轻量级语言提供良好的开发体验。

作为一个经常使用 IntelliJ 和 VS 代码的人，我尽可能更喜欢 VS 代码。用户体验对我来说更好。事实上，对于 JavaScript、TypeScript、Web、Python 和 Golang，我更喜欢 VS 代码中的开发人员体验。此外，在 JVM 项目和其他项目之间切换对我来说并不奇怪，因为我对两者都有相同的键盘映射。这些天来，我唯一一次启动 IntelliJ 是在我想进行全面的 Java 开发的时候。对于其他一切，我使用 VS 代码。

* * *

我希望你觉得这是有用的。如果您有任何问题，或者如果您认为我遗漏了什么，请添加评论。

如果你喜欢这篇文章，请留下赞或评论。

你可以在 [Twitter](https://twitter.com/deepu105) 和 [LinkedIn](https://www.linkedin.com/in/deepu05/) 上关注我。